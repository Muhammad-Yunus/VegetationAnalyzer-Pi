# VegetationAnalyzer-Pi 

Merupakan project analisis vegetasi menggunakan Raspberry Pi dan Kamera NoIR

- Normalized Difference Vegetation Index (NDVI)
- Enhanced Vegetation Index (EVI)

### Basic Concept
Vegetasi seperti pohon, rumput, semak dan lainya memiliki respon vegetasi yang sangat khas. Vegetasi yang sehat akan memantulkan banyak cahaya inframerah yang didapatkan dari sinar matahari. Sedangkan warna visible (Merah, hijau dan biru) memiliki pantulan lebih rendah.
![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/Indice-vegetativo-NDVI.jpg)

Berikut adalah kurva reflektasi(%) gelombang elektromagnetik yang bersumber dari sinar Matahari untuk object vegetasi :
![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/plot%20vegetation%20reflectance.png)

Karakteristik unik ]ini dapat kita manfaatkan untuk menganalisa level kesehatan tumbuhan secara remote melalui citra yang didapatkan dari kamera inframerah. 

### Vegetation Index Formula
Berikut adalah dua formula yang digunakan untuk menganalisa index kesehatan tumbuhan :
1. Normalized Difference Vegetation Index (NDVI)
Dihitung dengan cara mencari fraksi selisih reflektasi saluran inframerah  terhadap saluran visible light (red, green, blue), yang umum digunakan adalah saluran merah.
![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/ndvi_example.jpg)


2. Enhanced Vegetation Index (EVI)
Merupakan modifikasi dari Formula NDVI dengan menambahkan pengaruh dari saluran biru.
![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/EVI%20formula.png)
Koefisien pada formula dapat diadopsi dari penerapan citra MODIS-EVI dimana L=1, C1 = 6, C2 = 7.5, and G = 2.5 merupakan gain factor. Citra MODIS-EVI merupakan citra yang didapatkan dari satelit pengindraan jauh Terra-modis-1B.


### Hardware Implementation
Untuk mendapatkan citra inframerah, digunakan Raspberry Pi + Kamera NoIR dengan filter Biru.
![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/RPi-NoIR.PNG)


### Software Implementation
- Untuk menghitung NDVI Pada Raspberry Pi kita akan menggunakan Python dengan Library `PiCammera`, `Numpy` dan `Matplotlib` 
- Image array `img` yang didapatkan dari kamera NoIR menggunakan library `PiCammera` dapat dibunakan untuk membuat template layer komponen NDVI,
```
h, w, c = img.shape

NIR = np.zeros((h,w), dtype=float)
G = np.zeros((h,w), dtype=float)
B = np.zeros((h,w), dtype=float)
NDVI = np.zeros((h,w), dtype=float)
```
- Selanjutnya kita assign tiap layer pada raw image NoIR kedalam template layer `NIR`, `G` dan `B`. Selain itu lakukan normalisasi data agar tiap nilai pada citra memiliki rentang 0-1 (note : rentang nilai pixel 0-255 untuk 8 bit citra).
```
NIR = img[:, :, 2]
G = img[:, :, 1]
B = img[:, :, 0]

NIR = NIR/255.0
G = G/255.0
B = B/255.0
```
- Penggunaan `img[:,:,c]` akan mengambil tiap channel raw image. Dimana c = 0 merupakan saluran biru `B`, c = 1 merupakan saluran Hijau `G`, c = 2 merupakan saluran inframerah `NIR` (pada kamera RGB biasa c=2 merupakan saluran merah).
- Setelah itu kita hitung NDVI dengan menerapkanya pada formula NDVI,
```
NDVI = (NIR - B) / (NIR + B)
```
- Plot gambar yang dihasilkan menggunakan `Matplotlib`,
```
# show image using matplotlib
imgplot = plt.imshow(NDVI, cmap='jet')
plt.show()
```
- Result gambar yang dihasilkan,

![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/ndvi-result.png)

- Original NoIR image,

![](https://raw.githubusercontent.com/Muhammad-Yunus/VegetationAnalyzer-Pi/master/img-resource/img-20200605-063558.png)
