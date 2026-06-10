# AccessTrade

AccessTrade es una aplicación de escritorio que permite a personas con ceguera total analizar mercados financieros de forma autónoma, sin asistencia visual ni de terceros. Está diseñada desde cero para funcionar al 100% con lectores de pantalla (NVDA, JAWS) y manejarse íntegramente por teclado.

## ¿Qué resuelve?

Las plataformas bursátiles tradicionales (brokers, terminales, sitios de finanzas) dependen en gran medida de gráficos, tablas dinámicas, widgets interactivos y elementos visuales que los lectores de pantalla no interpretan correctamente. Esto deja a los usuarios ciegos fuera del análisis técnico cotidiano. AccessTrade traduce esa información a un formato textual estructurado, lineal y predecible:

- **Cotizaciones en vivo** de acciones e índices.
- **Resúmenes en lenguaje natural** del comportamiento del activo, listos para ser leídos por voz.
- **Velas históricas** descritas como texto (apertura, cierre, máximo, mínimo, volumen, dirección alcista/bajista).
- **Indicadores técnicos** (medias móviles, RSI, MACD, etc.) con su valor numérico y una interpretación textual.
- **Watchlist personal** persistente, navegable con flechas y atajos.
- **Anuncios contextuales** vía lector de pantalla cuando cambian datos, se carga información o se detectan errores.

El objetivo no es replicar visualmente un broker, sino ofrecer una vía completamente accesible y eficiente para que un trader o inversor ciego pueda seguir mercados, evaluar activos y tomar decisiones de forma autónoma.

**Autor:** Agustin Martinez
**Versión:** 0.01
**Licencia:** GNU General Public License, versión 2 (GPL v2)

## Estado actual

**Paso 2 — Capa de datos real.** La aplicación se conecta con Yahoo Finance vía `yfinance`, calcula indicadores técnicos sobre datos reales, persiste la watchlist en SQLite y carga toda la información en hilos de fondo para no bloquear la UI ni el lector de pantalla.

## Mercados soportados

| Mercado | Estado | Sufijo | Ejemplo |
|---|---|---|---|
| Estados Unidos (NYSE / NASDAQ) | Disponible | — | AAPL |
| Chile (Bolsa de Santiago) | Disponible | .SN | FALABELLA.SN |
| Brasil (B3) | Disponible | .SA | PETR4.SA |
| México (BMV) | Disponible | .MX | AMXL.MX |
| Argentina (BCBA) | Disponible | .BA | GGAL.BA |
| Colombia (BVC) | No disponible (pendiente de validación) | — | — |
| Perú (BVL) | No disponible (pendiente de validación) | — | — |

Colombia y Perú aparecen en el selector pero están deshabilitados. Se habilitarán cuando se validen los sufijos correctos en yfinance.

## Requisitos

- Python 3.12 o superior
- Sistema operativo: Windows (recomendado, probado con NVDA/JAWS). Funciona también en Linux/macOS para desarrollo.
- Dependencias: `wxPython`, `yfinance`, `pandas`, `pyttsx3`.

## Instalación (desarrollo)

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -e .
```

## Ejecución

```powershell
accesstrade
```

## Cómo probar con NVDA

1. Inicia NVDA antes de abrir AccessTrade.
2. Abre AccessTrade. La primera vez la watchlist está vacía; NVDA anuncia "Watchlist vacía. Pulsa Ctrl+N para agregar un ticker."
3. Pulsa `Ctrl+N`. Se abre el diálogo de agregar ticker.
4. Selecciona un mercado con las flechas. Escribe un ticker (ej. AAPL).
5. Pulsa Enter. La barra de estado anuncia "Validando AAPL contra Yahoo Finance...". Tras 1-2 segundos se cierra el diálogo y AAPL aparece en la lista.
6. Pulsa `F5` para refrescar. NVDA anuncia "Cargando N tickers..." y luego "N tickers cargados."
7. Pulsa Enter sobre un ticker para abrir el detalle.
8. En el detalle, `Ctrl+2` te lleva a Velas, `Ctrl+T` rota el timeframe.

## Atajos de teclado

### Ventana principal

| Atajo | Acción |
|---|---|
| `Ctrl+N` | Agregar ticker |
| `Enter` | Abrir detalle |
| `Supr` | Quitar ticker seleccionado |
| `F5` | Refrescar datos (invalida caché) |
| `Alt+F4` | Cerrar |

### Detalle del activo

| Atajo | Acción |
|---|---|
| `Ctrl+1..4` | Saltar a Información / Velas / Resumen / Indicadores |
| `Ctrl+T` | Rotar timeframe (1D → 1W → 1M) |

## Persistencia

La watchlist se guarda en:
- **Windows**: `%APPDATA%\AccessTrade\accesstrade.db`
- **Otros**: `~/.config/AccessTrade/accesstrade.db`

Es una BD SQLite que puede inspeccionarse con cualquier herramienta estándar.

## Estructura del código

```
src/accesstrade/
├── app.py                      Punto de entrada
├── ui/
│   ├── main_frame.py           Watchlist
│   ├── asset_detail_frame.py   Detalle del activo
│   ├── add_ticker_dialog.py    Agregar ticker
│   ├── about_dialog.py         Diálogo "Acerca de"
│   └── threading_utils.py      Helpers para llamadas asíncronas
└── data/
    ├── types.py                Tipos de dominio (WatchlistItem, Candle, Indicator, AssetInfo)
    ├── markets.py              Catálogo de mercados
    ├── market_data.py          Capa real sobre yfinance
    ├── indicators.py           Cálculo de indicadores técnicos
    ├── summary.py              Generación del resumen textual
    ├── cache.py                Caché en memoria con TTL
    ├── watchlist_store.py      Persistencia SQLite
    └── mock.py                 Mock para tests sin red
```

## Uso de inteligencia artificial

Durante el desarrollo de este proyecto se utilizaron herramientas de inteligencia artificial generativa como apoyo para agilizar tareas de codificación, refactorización y documentación. Todas las decisiones de diseño y la validación final corresponden al autor humano, quien revisa, ajusta y prueba el código antes de incorporarlo al proyecto.

## Licencia

Copyright (C) 2026 Agustin Martinez.

Este programa es software libre: puedes redistribuirlo y/o modificarlo bajo los términos de la Licencia Pública General de GNU, versión 2, publicada por la Free Software Foundation.

Este programa se distribuye con la esperanza de que sea útil, pero SIN NINGUNA GARANTÍA; ni siquiera la garantía implícita de COMERCIABILIDAD o IDONEIDAD PARA UN PROPÓSITO PARTICULAR. Consulta la Licencia Pública General de GNU para más detalles.

Deberías haber recibido una copia de la Licencia Pública General de GNU junto con este programa; si no, consulta <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html>.
