# Rotación · Smart-Money Flow Terminal — App de móvil

Panel de **rotación sectorial** (estilo RRG) con **datos reales de fin de día** (Stooq → Yahoo,
sin API key). Se actualiza solo cada día en GitHub y se ve en el móvil como una app.

---

## 1) Probarlo en tu ordenador (opcional)

```bash
pip install -r requirements.txt
python rotacion.py
```

Genera la carpeta `site/` con `index.html` y lo abre en tu navegador.

---

## 2) Publicarlo como app que se actualiza sola (GitHub Pages)

Necesitas una cuenta gratuita en https://github.com

1. **Crea un repositorio** nuevo (botón *New*). Ponle nombre, por ejemplo `rotacion`.
   Márcalo como **Public** (necesario para Pages gratis).
2. **Sube estos archivos** al repositorio:
   - En la página del repo: *Add file → Upload files*.
   - Arrastra **todo el contenido** de esta carpeta (incluida la carpeta `static`
     y la carpeta oculta `.github`). Confirma con *Commit changes*.
3. **Activa GitHub Pages con Actions**:
   - *Settings → Pages*.
   - En *Build and deployment → Source*, elige **GitHub Actions**.
4. **Lanza la primera construcción**:
   - Pestaña *Actions → Construir panel de rotacion → Run workflow*.
   - En 1–2 minutos tendrás tu panel en una URL del tipo:
     `https://TU-USUARIO.github.io/rotacion/`

A partir de ahí se reconstruye **solo cada día laborable** tras el cierre de Wall Street.

> La tarea programada usa horario **UTC** (`0 23 * * 1-5`). Si quieres otra hora,
> edita `.github/workflows/build.yml`. También puedes pulsar *Run workflow* cuando quieras.

---

## 3) Instalarlo en el móvil (icono en la pantalla de inicio)

Abre tu URL de GitHub Pages en el móvil y:

- **iPhone (Safari):** botón *Compartir* → **Añadir a pantalla de inicio**.
- **Android (Chrome):** menú ⋮ → **Instalar aplicación** (o *Añadir a pantalla de inicio*).

Queda con su icono y a pantalla completa, como una app. Al abrirla verás siempre
el último panel publicado, y guarda el último en caché para verlo sin conexión.

---

## Personalizar

Edita las constantes al principio de `rotacion.py`:

- `SECTORS`, `SATELLITES` — qué activos sigues.
- `BENCH` — índice de referencia (por defecto `SPY`).
- `WEEKS`, `TAIL` — historia y longitud de la estela del RRG.
- `BACKTEST` — activa/desactiva el backtest.
- `FRED_API_KEY` — opcional, para macro real (curva de tipos, crédito).

## Funciones pro

**Flujo de dinero por volumen.** Acumulación/Distribución (OBV + línea A/D) por sector.
Verde = entra dinero, rojo = sale. Detecta **divergencias**: precio sube pero el dinero
sale (distribución oculta = aviso) o precio flojo pero entra dinero (acumulación oculta =
entrada temprana). Avisa antes que el precio.

**Macro real con FRED (opcional).** Key gratuita en https://fred.stlouisfed.org en
`FRED_API_KEY` (o como Secret en GitHub). Añade tipo a 10 años, pendiente 2s10s,
diferencial de crédito HY y tipo real, y afina el régimen con ellos.

**Backtest causal.** Compara una estrategia simple (mantener cada semana los sectores en
*Líder*/*Mejorando*; si no hay, el índice) frente a comprar y mantener el índice. No mira
el futuro. Historia corta y sin comisiones: orientativo, no garantía.

**Avisos automáticos al móvil.** Mensaje cuando hay giros de rotación o divergencias.

- *Telegram (recomendado):* crea un bot con **@BotFather** (token) y tu *chat id* con
  **@userinfobot**. En local: `TELEGRAM_TOKEN` y `TELEGRAM_CHAT_ID`. En GitHub: *Settings →
  Secrets and variables → Actions → New repository secret* y crea `TELEGRAM_TOKEN` y
  `TELEGRAM_CHAT_ID`.
- *Discord/Slack:* crea un webhook y ponlo en `WEBHOOK_URL` (o como Secret del mismo nombre).
- *FRED en GitHub:* crea también el Secret `FRED_API_KEY` para la macro real.

**Análisis con IA (opcional).** Si pones `ANTHROPIC_API_KEY` (de console.anthropic.com, de pago por uso, céntimos al mes) en local o como Secret en GitHub, el panel incluye un comentario automático de IA cada día. Sin key, tienes igualmente el botón **«Copiar resumen para IA»**: copia la foto del día y la pegas en Claude/ChatGPT. (El panel no es IA en tiempo real: calcula los datos de cierre y, si quieres, los comenta la IA.)

**Más ETFs.** Además de los 11 sectores: China (FXI), emergentes (EEM), espacio/defensa (ITA), 7 Magníficos (MAGS), viajes (JETS), semiconductores (SMH), banca regional (KRE), biotech (XBI), mineras de oro (GDX) y software (IGV). Edítalos en `THEMATIC` y `EXTRA`. Cuando hay muchos, usa la **vista de cuadrantes** (no se solapan).

**Acciones líderes por sector (RS Rating).** Calcula un rating de fuerza relativa 1–99 (estilo IBD/O'Neil) para las principales acciones de cada sector y muestra las líderes (RS 90+), donde suele entrar el dinero institucional. Edita la lista en `SECTOR_STOCKS` y desactívalo con `STOCK_LEADERS = False` si quieres que tarde menos.

- Universo del percentil (`RS_UNIVERSE`): `"sector"` (~164 acciones, rápido) o `"sp500"` (las ~500 del S&P 500, percentil de mercado real; descarga en bloque con yfinance, tarda unos minutos). Descargar *todo* el NYSE (~6.000+) no es viable gratis y además empeora el percentil al meter micro-caps ilíquidas; el S&P 500 es el universo limpio y representativo.

---

## Aviso

Herramienta de apoyo a la decisión basada en fuerza relativa con datos de cierre
(con retardo). **No es asesoramiento financiero** y no sustituye tu gestión de
riesgo (tamaño de posición y stops).
