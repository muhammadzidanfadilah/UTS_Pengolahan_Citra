# UTS PENGOLAHAN CITRA APLIKASI MANIPULASI GAMBAR CITRA


```
NAMA ANGGOTA KELOMPOK:
Abiyanfaras Danuyasa    | 312210103
Birrham Efendi Lubis    | 312210272
Muhammad Zidan Fadillah | 312210277

Kelas : TI.22.A2
Mata Kuliah : Pengolahan Citra
```

# LINK PDF TUTORIAL PENGGUNAAN APLIKASI
[Tutorial.penggunaan.Aplikasi.Manipulasi.Proses.Gambar.Citra_.pdf](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/files/15458650/Tutorial.penggunaan.Aplikasi.Manipulasi.Proses.Gambar.Citra_.pdf)


# Meng import library yang akan di gunakan 

```
import streamlit as st
import cv2
import numpy as np
from matplotlib import pyplot as plt

def set_theme():
    st.markdown(
        """
        <style>
        .reportview-container {
            background: linear-gradient(to bottom, #2e2e2e, #101010) !important;
            color: #EAEAEA !important;
        }
        .css-2trqyj {
            border-radius: 12px !important;
            background-color: #1f77b4 !important;
            color: white !important;
        }
        .css-2trqyj:hover {
            background-color: #105399 !important;
        }
        .title-wrapper {
            perspective: 1000px;
            perspective-origin: center;
            overflow: hidden;
        }
        .title-wrapper h1 {
            position: relative;
            transition: all 0.3s;
            transform-origin: center;
            transform-style: preserve-3d;
            backface-visibility: hidden;
        }
        </style>
        """,
        unsafe_allow_html=True
    )
```
# konversi ke RGB Ke HSV
```
def convert_to_hsv(image):
    hsv_image = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    return hsv_image
```
# Menghitung dan juga Menampilkan HISTOGRAM
```
def compute_histogram(image):
    colors = ('b', 'g', 'r')
    fig, ax = plt.subplots(figsize=(6, 4))
    for i, col in enumerate(colors):
        histogram = cv2.calcHist([image], [i], None, [256], [0, 256])
        ax.plot(histogram, color=col)
        ax.set_xlim([0, 256])
    ax.set_title('Histogram')
    ax.set_xlabel('Pixel Value')
    ax.set_ylabel('Frequency')
    st.pyplot(fig)
```
# Mengatur sesuai keinginan pada BRIGHTNESS DAN CONTRAS
```
def adjust_brightness_contrast(image, brightness, contrast):
    adjusted = cv2.convertScaleAbs(image, alpha=contrast/127.0, beta=brightness)
    return adjusted
```
# Mendeteksi CONTOURS
```
def find_contours(image):
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    blurred = cv2.GaussianBlur(gray, (5, 5), 0)
    edged = cv2.Canny(blurred, 50, 150)
    contours, _ = cv2.findContours(edged, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    return contours
```
# Menampilkan SEGMENTASI
```
def segment_image_kmeans(image, k):
    image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    pixel_values = image_rgb.reshape((-1, 3))
    pixel_values = np.float32(pixel_values)
    criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 100, 0.2)
    _, labels, centers = cv2.kmeans(pixel_values, k, None, criteria, 10, cv2.KMEANS_RANDOM_CENTERS)
    centers = np.uint8(centers)
    segmented_image = centers[labels.flatten()]
    segmented_image = segmented_image.reshape(image.shape)
    return segmented_image
```
# Desain interface pada streamlit
```
def main():
    set_theme()

    st.markdown(
        """
        <div class="title-wrapper">
            <h1 style="font-size: 48px; text-align: center; color: #F08080;
                text-shadow: 2px 2px 4px #000000;">Aplikasi Manipulasi Gambar Citra</h1>
        </div>
        """,
        unsafe_allow_html=True
    )

    uploaded_file = st.file_uploader("Upload Image", type=['jpg', 'jpeg', 'png'])

    if uploaded_file is not None:
        file_bytes = np.asarray(bytearray(uploaded_file.read()), dtype=np.uint8)
        image = cv2.imdecode(file_bytes, 1)

        st.subheader('Original Image')
        st.image(image, channels="BGR", use_column_width=True)

        if st.button('Convert to HSV'):
            hsv_image = convert_to_hsv(image)
            st.subheader('HSV Image')
            st.image(hsv_image, channels="HSV", use_column_width=True)

        if st.button('Compute Histogram'):
            st.subheader('Histogram')
            compute_histogram(image)

        st.subheader('Adjust Brightness and Contrast')
        brightness = st.slider('Brightness', -100, 100, 0)
        contrast = st.slider('Contrast', -100, 100, 0)
        adjusted_image = adjust_brightness_contrast(image, brightness, contrast)
        st.image(adjusted_image, channels="BGR", use_column_width=True)

        if st.button('Find Contours'):
            contours = find_contours(image)
            st.subheader('Contours')
            image_with_contours = cv2.drawContours(image.copy(), contours, -1, (0, 255, 0), 2)
            st.image(image_with_contours, channels="BGR", use_column_width=True)
        
        st.subheader('Segmentasi')
        k = st.slider('Number of clusters (k)', 2, 10, 4)
        if st.button('Segment Image'):
            segmented_image = segment_image_kmeans(image, k)
            st.image(segmented_image, caption='Segmented Image', use_column_width=True)

if _name_ == '_main_':
    main()
```

# Hasil

# Upload gambar
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/d5378e97-3bdd-494e-ba5b-43576c6ec2e2)




# Sudah di upload
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/f03b9621-377f-4f09-ac51-4de070ce5c31)



# HSV
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/a21c8701-f31e-48f1-abd3-78fa16a93d4d)




# HISTOGRAM
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/bd408a17-b3e4-4a8d-9075-af8b9c6e7892)




# BRIGHTNESS DAN CONTRAS
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/0f73185f-afe2-440a-81c8-9ad8b2eb5dbc)



# CONTOURS
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/8a42f292-285a-46a6-9882-753daa111775)




# SEGMENTASI
![image](https://github.com/AbiyanfarasDanuyasa/UTS_pengolahan_citra/assets/115553474/d9226615-f988-4b22-b844-36b91a0077e7)


