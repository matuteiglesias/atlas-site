## Extrapolación (consolidado y deduplicado)

**Decisión operativa**  
- Extrapolar **cada serie de forma independiente** desde su **última fecha válida**.  
- Integrar al `DataFrame` maestro por **índice temporal** (no por columnas) usando `combine_first`/`update`.  
- **Cero NaN bleed**: extrapolar una columna **no** debe introducir `NaN` en las demás.  
- Dos métodos habilitados:
  1) `linear` — regresión lineal sobre las **últimas `k` observaciones** (nivel o tendencia).
  2) `trend_plus_seasonal_median` — extrapola **tendencia** y le suma el **perfil estacional** por **mediana mensual de residuales** `original - trend`.

**Contrato mínimo**
- **Entrada**: `df` con `DatetimeIndex` **mensual** (`freq='MS'`) llamado `indice_tiempo`.  
- **Parámetros**: `n_months` (horizonte, por defecto 6), `last_k` (ventana, por defecto 4).  
- **Salida**: series nuevas indexadas en las fechas futuras; integración no destructiva al `df`.

**Guardas**
- Si hay menos de `last_k` puntos no nulos → devolver `NaN` en el horizonte y loggear advertencia.  
- Si el índice no es mensual → **fallar** con mensaje o adaptar explícitamente antes.

### API de referencia

~~~python
from __future__ import annotations
import numpy as np, pandas as pd
from sklearn.linear_model import LinearRegression

# ---------- utilidades ----------
def _assert_monthly_index(df: pd.DataFrame):
    if not isinstance(df.index, pd.DatetimeIndex):
        raise ValueError("Se espera DatetimeIndex en df.index")
    # toleramos freq missing, pero validamos mensualidad por diferencias
    diffs = df.index.to_series().diff().dropna().value_counts().index
    if len(diffs) and not any(pd.Timedelta(days=27) <= d <= pd.Timedelta(days=32) for d in diffs):
        raise ValueError("Índice no parece mensual; adapte antes de extrapolar")

def _future_index(last_date: pd.Timestamp, n_months: int) -> pd.DatetimeIndex:
    start = (last_date + pd.offsets.MonthBegin(1)).normalize()
    return pd.date_range(start, periods=n_months, freq="MS", name="indice_tiempo")

# ---------- método 1: lineal ----------
def extrapolate_linear(df: pd.DataFrame, column: str, n_months: int = 6, last_k: int = 4) -> pd.Series:
    _assert_monthly_index(df)
    s = df[column].dropna().tail(last_k)
    fidx = _future_index(df.index.max(), n_months) if s.empty else _future_index(s.index.max(), n_months)
    if len(s) < last_k:
        return pd.Series([np.nan] * n_months, index=fidx, name=column)
    X = np.arange(len(s)).reshape(-1, 1)
    y = s.values
    model = LinearRegression().fit(X, y)
    yhat = model.predict(np.arange(len(s), len(s) + n_months).reshape(-1, 1))
    return pd.Series(yhat, index=fidx, name=column)

# ---------- método 2: tendencia + mediana mensual de residuales ----------
def extrapolate_trend_plus_seasonal_median(
    df: pd.DataFrame,
    column_trend: str,
    column_original: str,
    n_months: int = 6,
    last_k: int = 4,
) -> pd.Series:
    _assert_monthly_index(df)
    t = df[column_trend].dropna().tail(last_k)
    fidx = _future_index(df.index.max(), n_months) if t.empty else _future_index(t.index.max(), n_months)
    if len(t) < last_k:
        return pd.Series([np.nan] * n_months, index=fidx, name=column_trend)
    # extrapolación lineal de la tendencia
    X = np.arange(len(t)).reshape(-1, 1)
    y = t.values
    fore_trend = LinearRegression().fit(X, y).predict(
        np.arange(len(t), len(t) + n_months).reshape(-1, 1)
    )
    # perfil estacional robusto: mediana mensual de (original - tendencia)
    resid = (df[column_original] - df[column_trend]).copy()
    monthly_med = resid.groupby(df.index.month).median()
    adj = monthly_med.reindex(fidx.month).to_numpy()
    return pd.Series(fore_trend + adj, index=fidx, name=column_trend)

# ---------- integración segura (sin NaN bleed) ----------
def extend_df_with_forecasts(
    df: pd.DataFrame,
    spec: dict,
    n_months: int = 6,
    last_k: int = 4,
) -> pd.DataFrame:
    """
    spec:
      {'IPC':'linear',
       'EMAE_trend':('trend_plus_seasonal_median','EMAE_trend','EMAE_original')}
    Integra predicciones por índice temporal usando combine_first.
    """
    if df.index.name != "indice_tiempo":
        df = df.set_index("indice_tiempo")
    for col, method in spec.items():
        if isinstance(method, str) and method == "linear":
            fore = extrapolate_linear(df, col, n_months=n_months, last_k=last_k)
            df = df.combine_first(fore.to_frame())
        elif isinstance(method, tuple) and method[0] == "trend_plus_seasonal_median":
            _, col_trend, col_orig = method
            fore = extrapolate_trend_plus_seasonal_median(
                df, col_trend, col_orig, n_months=n_months, last_k=last_k
            )
            df = df.combine_first(fore.to_frame())
        else:
            raise ValueError(f"Especificación de método inválida para '{col}': {method}")
    return df
~~~

### QA mínimo

* **No-NaN bleed**: para columnas **no extrapoladas**, `na_counts` antes/después debe coincidir.
* **Primer punto futuro** = `last_valid_date(col) + 1M`.
* **Horizonte exacto**: `len(future_index) == n_months`.
* **Determinismo**: mismos insumos ⇒ mismo resultado (no hay aleatoriedad).
* **Logs**: advertir si `len(no_nulos) < last_k` (se devuelven `NaN`).

### Errores típicos (y cómo evitarlos)

* **Concatenar columnas** y crear filas huecas → siempre integrar por índice (`combine_first`).
* **Parchear lista `interpolate`/stops sin validar índice mensual** → validar frecuencia antes; adaptar a mensual previamente.
* **Usar medias en vez de medianas** para el patrón mensual → mayor sensibilidad a outliers; mantener **medianas**.

### Anclas (0138 "headers only")

* `#prep-extract` — extracción/limpieza previa a extrapolación.
* `#json-export` — export de resultados (ver *metodos/etl\_json\_policies*).
* `#merge-post` — merge posterior con otras tablas/series por índice temporal.

