# Taller2_Wavelet_e_Imagenes-

Transformada **Wavelet** de una señal EEG real y **procesamiento digital de imágenes** (DICOM, color,
imágenes médicas de PhysioNet y cámara en tiempo real) con Python, OpenCV y PyWavelets.

> Asignatura: *Procesamiento digital de señales e imágenes biomédicas* — Taller 2
> Grupo de 2 estudiantes · 

---

## Descripción

El taller continúa el trabajo del [Taller 1](https://github.com/valepiedrahita107/EEG-de-la-base-EEG-During-Mental-Arithmetic-Task-)
(filtrado y Fourier del EEG). Aquí se pasa del análisis en frecuencia al análisis **tiempo-frecuencia**
(Wavelet y STFT) y luego al **procesamiento de imágenes**, incluyendo imágenes médicas reales en formato
DICOM y procesamiento en vivo con la cámara.

Todo el desarrollo está en **un solo notebook**, con celdas markdown que explican cada paso, variables en
español y descarga automática de los datos.

---

## Contenido del notebook

| Punto | Tema | Qué se hace |
|:--:|---|---|
| 1 | **Wavelet y STFT** | Escalograma (CWT con wavelet Morlet) de la señal EEG promedio frontal, usando como referencia la señal de un pulsador con pulsos conocidos; energía de cada banda cerebral en el tiempo; comparación con el espectrograma STFT para dos tamaños de ventana |
| 2 | **Imagen DICOM** | Datos del paciente, histograma, ecualización (subplot de 4) y botones para logaritmo, potencia y negativo, con ajuste de **c** (±1) y **gamma** (±0.1) |
| 3 | **Imágenes a color** | Dos fotos propias (una por integrante): formato, dimensiones y peso en disco comparando JPG/PNG/BMP/TIFF/WEBP; switch de espacios de color RGB, HSV, XYZ, YCbCr y YMC; histograma a color y ecualización canal por canal; imagen dividida en 4 cuadrantes (gris, verde, rojo, azul) |
| 4 | **Imágenes de PhysioNet** | Ganancia `n·x` con botones, rotación, negativo, binarización con umbral de 20 en 20 y los 8 planos de bits obtenidos con la operación AND |
| 5 | **Cámara en tiempo real** | Fase 1 (10 s): RGB + histograma en vivo · Fase 2 (10 s): grises + histograma · Fase 3: binarización recorriendo OTSU, BINARY, BINARY_INV, TRUNC, TOZERO y TOZERO_INV |

---

## Asignación de imágenes (según las cédulas)

```
1020104124 + 1036518080 = 2056622204
2056622204 % 501 = 170      ->  imagen 170 del CT de dicomlibrary.com
124 % 76 = 48               ->  imagen E1154S7I048.png de PhysioNet
 80 % 76 =  4               ->  imagen E1154S7I004.png de PhysioNet
```

---

## Estructura del repositorio

```
.
├── Taller2_vpo_jrm.ipynb     # Notebook único con todo el desarrollo
├── Subject14_2.edf           # Señal EEG del taller anterior (se descarga sola si falta)
├── imagen_dicom.dcm          # Imagen 170 del CT de dicomlibrary (se descarga manual, ver abajo)
├── imagen_color_1.jpg        # Foto a color del integrante 1 (mascota)
├── imagen_color_2.jpg        # Foto a color del integrante 2 (lirios)
├── E1154S7I048.png           # Imágenes de PhysioNet (se descargan solas)
├── E1154S7I004.png
└── README.md
```

---

## Requisitos e instalación

Python 3.9 o superior:

```bash
pip install numpy scipy matplotlib mne PyWavelets pydicom opencv-python ipywidgets
```

La primera celda del notebook revisa qué falta y lo instala sola, así que también funciona en
**Google Colab** sin configurar nada.

---

## Ejecución

```bash
git clone https://github.com/<usuario>/<repositorio>.git
cd <repositorio>
jupyter notebook Taller2_vpo_jrm.ipynb
```

Ejecutar las celdas en orden (*Kernel → Restart & Run All*). El notebook **descarga automáticamente** el
archivo `.edf` del taller anterior y las dos imágenes de PhysioNet; las fotos a color y la imagen DICOM se
toman de la carpeta si están, y si no usa imágenes de respaldo para que nunca se caiga.

### Cómo conseguir la imagen DICOM asignada (número 170)

1. Entrar a [www.dicomlibrary.com](https://www.dicomlibrary.com) y bajar hasta **DICOM Samples**.
2. Abrir el estudio con **Modality: CT** (botón *View*).
3. Dentro del visor avanzar hasta la imagen **170**.
4. Descargarla en formato DICOM y guardarla como `imagen_dicom.dcm` al lado del notebook.

Si el archivo no está, el notebook usa el CT de ejemplo que trae `pydicom` y lo avisa en pantalla.

---

## Notas técnicas

* El escalograma se dibuja con `imshow` en vez de `pcolormesh`: con 31 000 muestras la diferencia es de
  varios minutos a menos de un segundo. El tope de la escala de color se fija en el percentil 98 para que la
  energía de las frecuencias bajas no opaque el resto del mapa.
* Las escalas de la CWT se convierten a **Hz** con la frecuencia central de la wavelet
  (`pywt.central_frequency`), así el eje vertical se lee directamente en frecuencia.
* La imagen DICOM viene en 16 bits con valores negativos (unidades Hounsfield): se normaliza a 8 bits con
  `cv2.normalize` antes de ecualizar, porque `equalizeHist` solo trabaja con `uint8`.
* Los botones usan `ipywidgets` con el patrón `clear_output(wait=True)` + redibujado en la misma celda, que
  funciona igual en Jupyter y en Colab.
* El punto 5 detecta el entorno: en Colab abre la cámara del navegador con JavaScript y en Jupyter local usa
  `cv2.VideoCapture(0)`.

---

## Referencias

* Zyma, I. *et al.* — [EEG During Mental Arithmetic Task Performance](https://physionet.org/content/eegmat/1.0.0/), PhysioNet.
* PhysioNet — [Sample medical images](https://physionet.org/content/images/1.0.0/).
* DICOM Library — [www.dicomlibrary.com](https://www.dicomlibrary.com).
* PyWavelets — [Continuous Wavelet Transform (CWT)](https://pywavelets.readthedocs.io/en/latest/ref/cwt.html).
* OpenCV — [Image Thresholding](https://docs.opencv.org/4.x/d7/d4d/tutorial_py_thresholding.html) ·
  [Histogram Equalization](https://docs.opencv.org/4.x/d5/daf/tutorial_py_histogram_equalization.html).
