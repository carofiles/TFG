# YOLO-Pose para análisis de ejercicios de rehabilitación de rodilla

> Estado actual: fase de validación de keypoints. Los ángulos articulares no deben interpretarse todavía como mediciones validadas.

## Pipeline

```text
Vídeo → frames → YOLO-Pose → 17 keypoints → control de calidad
      → características biomecánicas → análisis temporal → evaluación
```

## Origen del código

El proyecto parte de `BingfengYan/yolo_pose`, una implementación basada en YOLOv5 para estimación de pose:
https://github.com/BingfengYan/yolo_pose

Las modificaciones de este TFG incluyen la extracción estructurada de keypoints, procesamiento de secuencias, almacenamiento de coordenadas/confianza, cálculo de ángulos articulares y análisis temporal.

Las partes derivadas del repositorio original deben conservar sus avisos y licencia correspondientes.

## Dataset

Se utiliza REHAB24-6:
https://zenodo.org/records/13305826

El dataset **no se incluye** en GitHub. Debe descargarse según las condiciones de sus autores.

## Modelo

No se incluyen checkpoints `.pt`. El experimento inicial utiliza:

`yolov5s6_pose_640.pt`

El peso debe descargarse por separado y colocarse en `weights/`.

## Instalación

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

En el HPC de la universidad se utiliza el entorno virtual propio del servidor.

## Inferencia

```bash
python detect.py   --weights weights/yolov5s6_pose_640.pt   --source ruta/a/imagen.jpg   --img-size 640 640   --conf-thres 0.85   --device 0   --kpt-label
```

El umbral `0.85` es un valor utilizado en los experimentos iniciales, no un umbral óptimo validado.

## Keypoints

El modelo utiliza los 17 keypoints COCO:

| Índice | Keypoint |
|---:|---|
| 0 | nose |
| 1 | left_eye |
| 2 | right_eye |
| 3 | left_ear |
| 4 | right_ear |
| 5 | left_shoulder |
| 6 | right_shoulder |
| 7 | left_elbow |
| 8 | right_elbow |
| 9 | left_wrist |
| 10 | right_wrist |
| 11 | left_hip |
| 12 | right_hip |
| 13 | left_knee |
| 14 | right_knee |
| 15 | left_ankle |
| 16 | right_ankle |

Cada punto se almacena como `x, y, confidence`.

## Ángulo de rodilla

Se calcula a partir de cadera, rodilla y tobillo:

```text
v1 = cadera - rodilla
v2 = tobillo - rodilla

ángulo = arccos(dot(v1,v2) / (|v1||v2|))
```

Son estimaciones 2D procedentes de visión por computador, no mediciones clínicas.

## Estructura

```text
yolo-pose-rehab/
├── README.md
├── requirements.txt
├── .gitignore
├── detect.py
├── train.py
├── test.py
├── models/
├── utils/
├── scripts/
├── analysis/
│   ├── analyze_squat.py
│   └── analyze_temporal.py
├── configs/
├── docs/
│   ├── methodology.md
│   └── validation.md
├── data/
├── weights/
└── results/
```

`detect.py`, `train.py`, `test.py`, `models/` y `utils/` corresponden a la base YOLO-Pose; los scripts de análisis y preparación representan el trabajo específico del TFG.

## Qué NO se sube

El `.gitignore` excluye:

- dataset y vídeos;
- frames;
- checkpoints `.pt`;
- resultados grandes;
- logs y cachés;
- entornos virtuales;
- credenciales y claves;
- archivos temporales.

## Validación

Antes de sacar conclusiones biomecánicas hay que comparar las predicciones con las anotaciones de referencia de REHAB24-6 y documentar modelo, resolución, umbral, subconjunto y métricas.

## Próximos pasos

1. Validar las coordenadas de los keypoints.
2. Comparar modelos/resoluciones.
3. Comparar con las anotaciones 2D de referencia.
4. Cuantificar el error de localización.
5. Estabilizar las trayectorias temporalmente.
6. Calcular variables biomecánicas.
7. Detectar repeticiones.
8. Estudiar criterios objetivos de ejecución.

