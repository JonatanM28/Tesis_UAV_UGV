# Navegación Autónoma Colaborativa UAV–UGV (Visión + Deep Learning)

Implementación reproducible del sistema de cooperación **UAV–UGV** basado exclusivamente en **visión artificial** y **aprendizaje profundo**.  
El **UGV** sigue un carril con una **ResNet-18** que predice el **PWM de dirección**; el **UAV** se centra sobre el UGV mediante **ArUco** y asiste con **detección de obstáculos (YOLOv8n)**.  
La orquestación es **off-board**, con comunicación de baja latencia por Wi-Fi.  

Este repositorio contiene los códigos desarrollados durante la tesis *“Navegación Autónoma Colaborativa UAV–UGV Mediante Visión Artificial”*.

---

## 📂 Mapa del repositorio

| Archivo | Función principal |
|---------|------------------|
| `Calibracion_Camara.py` | Calibración intrínseca de cámara con tablero (chessboard/charuco). Estima matriz K y coeficientes de distorsión. |
| `Captura_Img_Tablero.py` | Captura imágenes del tablero para el dataset de calibración. |
| `Deteccion_Aruco.py` | Detección y estimación de pose de marcadores ArUco. Sirve para el centrado del UAV sobre el UGV. |
| `EntrenamientoResNet18.py` | Entrenamiento de ResNet-18 para predecir el PWM de dirección del UGV a partir de imágenes. |
| `Yolov8n.py` | Entrenamiento y validación de YOLOv8n para detección de obstáculos y del UGV. |
| `UGV.py` | Inferencia en el vehículo terrestre: abre cámara, carga modelo ResNet-18 y publica comandos de dirección. |
| `UAV.py` | Inferencia en el dron: detección ArUco y YOLOv8n para centrado y advertencias. |
| `CodigoUAV_UGV.py` | Script de integración: coordina UAV y UGV, aplica política de freno seguro y registra métricas. |
| `requirements.txt` | Dependencias necesarias para ejecutar los códigos. |

---

## ⚙️ Requisitos

- Python 3.8+
- OpenCV (contrib), NumPy, Pandas, Matplotlib
- PyTorch y Torchvision
- Ultralytics YOLOv8
- (Opcional) Roboflow para gestión de datasets

---

## 🚀 Instalación

```bash
# Clonar repositorio
git clone https://github.com/JonatanM28/Tesis_UAV_UGV.git
cd Tesis_UAV_UGV

# Crear entorno virtual (opcional)
python -m venv .venv
source .venv/bin/activate     # Windows: .venv\Scripts\activate

# Instalar dependencias
pip install -r requirements.txt




## 🚀 Guías rápidas

1) Calibración de cámara y ArUco

# Captura de imágenes para calibración
python Captura_Img_Tablero.py --out ./data/calib/ --frames 200

# Calibración intrínseca
python Calibracion_Camara.py --imgs ./data/calib/*.png --save ./artifacts/camera_params.yaml

# Prueba de detección de ArUco
python Deteccion_Aruco.py --cam 0 --params ./artifacts/camera_params.yaml


2) Entrenamiento de modelos

ResNet-18 (dirección del UGV)

python EntrenamientoResNet18.py --images ./data/steering/imgs --labels ./data/steering/labels.csv \
--epochs 50 --batch 64 --out ./artifacts/resnet18/

YOLOv8n (detección de obstáculos + UGV)

python Yolov8n.py --data ./data/yolo/data.yaml --model yolov8n.pt \
--epochs 100 --batch 16 --out ./artifacts/yolov8n/

3) Ejecución en pista (integración)

UGV

python UGV.py --model ./artifacts/resnet18/best.pth


UAV

python UAV.py --params ./artifacts/camera_params.yaml --yolo ./artifacts/yolov8n/best.pt


Integración completa

python CodigoUAV_UGV.py
