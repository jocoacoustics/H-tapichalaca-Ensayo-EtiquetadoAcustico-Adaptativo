# Ensayo_EtiquetadoAcustico_Adaptativo v0.3.2

https://jocoacoustics.github.io/H-tapichalaca-Ensayo-EtiquetadoAcustico-Adaptativo/

**Directorio oficial de Colab:** `/content/drive/Shareddrives/CONSERVACION/2. Especies/Hyloscirtus tapichalaca - Rana de Tapichalaca/Ensayo_EtiquetadoAcustico_Adaptativo`

## Cómo actualizar sin volver a calcular lo anterior

1. Descomprime el **PARCHE v0.3.2** en el directorio padre de la carpeta del proyecto, reemplazando scripts, notebook y módulos. El parche NO contiene ni borra `data/raw/`, `runs/`, `reports/`, `models/` ni `githubpages/`.
2. Abre de nuevo el notebook actualizado en Colab. Mantén `SOURCES='auto'`, `RUN_YOLO26=True`, `REUSE_EXISTING=True`.
3. El programa busca ejecuciones v0.3.0/0.3.1 en `runs/`. Verifica SHA-256 de ambos audios, CSV, miniaturas, matriz e informes. Si el run `colab_v03_...` y los informes correspondientes siguen intactos, recupera las ROI y comparaciones del corto y de CHAGW101 sin recalcularlos.
4. Las predicciones reales `predicciones_Papiro.json` también se recuperan si contienen los identificadores y modelo esperados. Si faltan, se corre únicamente YOLO con lote adaptativo y resultado nuevo, sin repetir STFT/FSST ni correlación.
5. `runs/<nueva_ejecución>/REUSO.json` documenta qué fuente se recuperó y cuál se recalculó. `progress.jsonl` contiene porcentaje, etapa, tiempo transcurrido y ETA aproximada.
6. Ejecuta las pruebas rápidas al final del notebook; por defecto **no repiten** los análisis costosos. Un test fallido se conserva en `pytest_diagnostico.log` sin eliminar resultados científicos.

**Límites del reuso:** si se modificó un WAV/MP3, faltan los CSV o miniaturas, o el HTML previo fue sobrescrito y no existe copia válida, esa fuente se recalcula. El reuso es de análisis completos por fuente, no de bloques intermedios incompletos. Los pesos COCO de YOLO26-seg no constituyen un clasificador taxonómico de ranas. La GPU se emplea en inferencia YOLO; STFT/FSST siguen en CPU, por lo que ver la T4 poco ocupada durante esas fases es esperado.

**Precaución GitHub Pages:** la referencia BioWeb pertenece a PUCE. Verifica autorización antes de redistribuir su audio públicamente. El sitio en `githubpages/` puede actualizarse sin recalcular señales.

---

# PAPIRO · Ensayo_EtiquetadoAcustico_Adaptativo v0.3.1

Laboratorio reproducible en Google Colab, motor editorial Papiro v4.1 conservado y sitio listo para GitHub Pages.

## Instalación / ruta exacta

Descomprime el ZIP **dentro** de:

`/content/drive/Shareddrives/CONSERVACION/2. Especies/Hyloscirtus tapichalaca - Rana de Tapichalaca/`

El ZIP ya incluye la carpeta raíz `Ensayo_EtiquetadoAcustico_Adaptativo/`. No anides dos carpetas iguales. Abre el notebook de la raíz y ejecuta las celdas. Google Drive debe montarse primero.

## Audios

- `data/raw/audio_slice.wav`: muestra corta, viene incluida.
- `data/raw/cc046A-QCAZ16704.mp3`: referencia BioWeb (PUCE), viene incluida solo para investigación; comprobar derechos para publicar.
- `data/raw/CHAGW101_20260828_020000.wav`: **no viene incluido**, porque no se adjuntó en esta conversación. Si ya lo dejaste en `data/raw` de Drive, el notebook lo descubrirá automáticamente. Nunca se modifica un WAV original.

Con `--sources auto` analiza corto + largo cuando existe; si falta el largo, publica solo el corto y deja inhabilitada la opción. `--sources long` exige que exista el WAV largo. Cambiar el selector en Papiro cambia el informe entero, con sus ROI, tabla, matriz, métricas y reproducciones frente a BioWeb, no solamente el dibujo.

## Detección sin ROI precargadas

`src/papiro/spectral_v4.py` infiere eventos desde la propia señal: STFT, fondo temporal móvil, contraste, umbral, cierre morfológico y etiquetado de regiones temporales. Crea ROI adaptativas en tiempo y frecuencia usando las máscaras provisionales de procesamiento clásico. Ningún CSV de etiquetas anteriores forma parte de la detección ni comparación. Todos los candidatos quedan en `runs/<run>/short|long/candidatos_completos.csv` y las notas mostradas en `roi_inferidas.csv`. Los parámetros, **no las coordenadas**, viven en `config/experimento.yaml`.

Para audios largos, se hace STFT/FSST en bloques solapados (`chunk_seconds`, `overlap_seconds`), se retienen espectrogramas de resolución completa cerca de eventos y columnas más escasas en el resto; las coordenadas absolutas se preservan. Se exporta CSV de **todos** los candidatos. Para evitar matrices de miles de celdas, el informe compara hasta `max_compared_rois: 40` candidatos del archivo investigado, en orden temporal, contra todas las notas BioWeb. Cambia ese límite si necesitas más. Los espectrogramas generales largos tienen un resumen de hasta 9000 columnas; al navegar por las ROI se restauran parches espectrales de alta resolución alrededor del canto. Un canto no detectado por el umbral no será segmentado ni emparejado automáticamente: revisa bandas/umbral y el audio.

## Experiencia Papiro preservada

- Figura principal: STFT original o FSST, ejes físicos comparables, arrastre horizontal **independiente**, zoom, ROI opcionales, `⏮ ROI / ROI ⏭`, reproducción de nota y desde el cursor, reproducir audio completo.
- Sección 03: ROI con STFT, suave, contorno clásico y YOLO importado; botón `Escuchar` en cada una; 520 ms × 850–2100 Hz, sin cambiar proporciones.
- Sección 04: matriz visual naranja/cian de U × B, miniaturas alineadas y sin alinear, selección y métricas.
- Sección 05: inspección antes/después, ΔHz/Δms, Pearson 2D, DTW, IoU, reproducción de la pareja, alineamiento visual.
- Índice editorial a la derecha; metadatos y CSV por ejecución.

## YOLO26-seg

`--mode infer` usa Ultralytics real y CUDA si está disponible; los pesos oficiales COCO no fueron entrenados para vocalizaciones. Inferencia sin detecciones es posible y no debe reinterpretarse como ausencia de rana. Las pseudomáscaras clásicas y predicciones YOLO están separadas. Para un clasificador taxonómico validado faltan grabaciones verificadas independientes; un resultado de correlación no es probabilidad de misma especie.

## Publicación GitHub Pages

Después de ejecutar Colab, copia **el contenido de `githubpages/`**, no la carpeta externa, a la raíz de un repositorio GitHub. Configura `Settings → Pages → Deploy from branch → main / (root)`; visita `https://USUARIO.github.io/REPOSITORIO/`. Incluye `index.html`, `long.html` si el audio largo estaba presente, `.nojekyll`, archivos MP3 comprimidos en `assets/audio/` y `site_manifest.json`. El HTML publicado tiene referencias relativas (no enlaces absolutos de Drive ni a `/mnt/data`).

**Permisos:** confirma derecho de redistribución del audio BioWeb antes de hacerlo público. La generación del repositorio incluye de forma técnica su copia comprimida para que el reproductor funcione; eso no implica autorización de publicación.

## Comandos

```bash
python scripts/run_pipeline.py --mode baseline --sources auto
python scripts/run_pipeline.py --mode infer --sources auto
python scripts/run_pipeline.py --mode baseline --sources long
python -m pytest -q tests/
```

`reports/index.html` y `reports/long.html` son entradas locales; `reports/Papiro_short.html` y `reports/Papiro_long.html` son las últimas generaciones. `runs/<id>/<short|long>/` conserva tablas, notas, ROI, modelos/inferencia y auditoría con hashes de origen. `reports/baseline/` conserva v4.1, sin interferir con nuevos resultados.

## Limitaciones conocidas

El detector clásico no reconoce todas las especies por sí mismo y puede omitir notas fuera de banda o muy débiles. La vista principal larga utiliza reducción visual fuera de las ROI y parches de alta resolución dentro. No se incluye el CHAGW101 original porque solo está en tu Drive. La ruta GitHub Pages no ejecuta inferencia en el navegador, solo visualiza resultados calculados en Colab. Las variantes y audio de BioWeb no certifican identificación taxonómica.


## v0.3.1 · Telemetría de progreso

`runs/<run_id>/progress.jsonl` contiene las actualizaciones de avance por fuente. La barra de Colab recibe estos eventos directamente y muestra etapa, bloques procesados, tiempo transcurrido y ETA aproximada que se recalcula. El tiempo es una extrapolación observacional, no una garantía. Ejecutar el notebook actualizado con el proyecto en unidad compartida `Shareddrives`.

## v0.3.3 — Visor continuo y autor

- Autora/autor del estudio: **Pedro Galindo Vera** (debajo del título en Papiro).
- Reparación del problema de bandas y parches verticales del audio largo: el fondo STFT/FSST del visor se toma a **intervalos temporales uniformes desde toda la onda original**, con una única normalización global de imagen por método y archivo. Los espacios entre llamadas nunca se reconstruyen interpolando solo las ROI.
- Detalle de resolución: la representación del audio largo limita el número máximo de columnas para evitar agotar RAM o producir imágenes imposibles de abrir. La escala temporal efectiva de esa imagen aparece en el visor; los tiempos, audio original, ROI, frecuencias y métricas no son modificados por esa optimización **solo visual**.
- **Sin recálculos innecesarios**: con `REUSE_EXISTING=True`, la v0.3.3 recupera las ROI, 28/280 comparaciones y predicciones YOLO validadas de una corrida anterior, y recalcula únicamente la imagen espectral principal necesaria para arreglar el visor. No altera `runs/` anteriores.
- `githubpages/` se vuelve a exportar desde el HTML corregido. Confirmar permiso de publicación del audio de PUCE antes de hacerlo público.
- Instalación sobre el proyecto existente: descomprimir el parche v0.3.3 en la carpeta padre de `Ensayo_EtiquetadoAcustico_Adaptativo`, reemplazando únicamente código, YAML, notebook y documentación. No reemplazar `data/raw`, `runs`, `models` ni `reports`.
