# Land Cover Sampling Tool

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#overview">Overview</a></li>
    <li><a href="#background">Background</a></li>
    <li><a href="#prerequisites">Prerequisites</a></li>
    <li><a href="#usage">Usage</a></li>
  </ol>
</details>

## Overview
**Land Cover Sampling Tool** dapat digunakan untuk mendesain dan memberikan label pada sampel yang dapat digunakan dalam proses klasifikasi citra penginderaan jauh dengan teknik *supervised machine learning*. Aplikasi ini dibangun pada platform Jupyter Notebook dengan menggunakan beberapa Python package seperti `ee`, `geopandas`, `ipywidgets`, `ipyleaflet`, dan `geemap`. Aplikasi ini terdiri dari dua bagian: 
1. membangun set data sampel yang distribusinya didasarkan atas hasil K-Means Clustering dengan proporsi jumlah sesuai luas setiap kluster; 
2. memberikan label pada setiap titik sampel secara interaktif melalui *widget* `ipyleaflet`.

## Background
Peta penutup lahan yang dibuat dengan teknik *supervised machine learning* (misalnya dengan Maximum Likelihood, Support Vector Machine, Classification and Regression Trees, Random Forests, dsb.) akurasinya banyak dipengaruhi oleh kualitas sampel yang digunakan. *Labelled samples* dapat diperoleh salah satunya dari hasil observasi lapangan, misalnya plot sampel dan hasil survey. Namun terkadang untuk area pemetaan yang besar, sampel tersebut tidak cukup representatif dari segi kuantitas dan distribusi spasialnya untuk digunakan sebagai sampel dalam model *supervised classification*.

Sampel penutup lahan banyak dibangun dengan membuat data sampel melalui proses observasi dan digitisasi (berupa titik maupun area) pada citra penginderaan jauh resolusi spasial tinggi. Permasalahannya, proses ini seringkali menghasilkan data sampel yang tidak cukup ideal bagi model *supervised classification*. Misalnya, banyak sampel yang *redundant* dan secara statistik tidak meliput distribusi nilai piksel keseluruhan secara utuh. Artinya kecenderungan tidak terambilnya "kluster" sampel dengan karakteristik tertentu bisa terjadi.

Solusi yang dapat digunakan untuk mengatasi permasalahan tersebut salah satunya adalah pendekatan *semisupervised learning*. Dalam pendekatan *semisupervised learning*, pertama dilakukan proses *unsupervised learning* untuk mengelompokkan piksel menjadi beberapa kluster. Setiap kluster kemudian diberikan label tipe penutup lahan, beberapa di antaranya mungkin masih berupa percampuran beberapa tipe penutup lahan. Melalui observasi, kluster-kluster yang menggambarkan kelas penutup lahan murni kemudian dijadikan acuan untuk membuat *labelled sampel*.

Aplikasi **Land Cover Sampling Tool** mencoba mengadaptasi pendekatan *semisupervised learning* tersebut dengan menggunakan metode K-Means Clustering. Dari setiap kluster yang dihasilkan, sejumlah titik sampel kemudian diambil secara acak dengan jumlah proporsi berdasarkan luas setiap kluster. Proses berikutnya adalah *data labelling*, yaitu memberikan label pada setiap titik sampel tersebut. Aplikasi ini dapat digunakan untuk berbagai citra multispektral maupun hiperspektral, namun dalam prototipe ini digunakan liputan citra Landsat 8 OLI sebagai contoh.

## Prerequisites
Untuk menjalankan **Land Cover Sampling Tool** Jupyter Notebook Anda perlu membuat Python virtual environment dan melakukan instalasi beberapa Python package, direkomendasikan menggunakan `conda`.

Pertama, Anda perlu memastikan bahwa Python dan `anaconda` telah terpasang pada komputer Anda. Ketika memasang `anaconda`, Python3 otomatis akan terpasang pada komputer Anda. Instalasi `anaconda` dapat dilihat [di sini](https://docs.anaconda.com/anaconda/install/index.html).

Kedua, Anda harus memiliki akun [Google Earth Engine](https://earthengine.google.com/) ([sign up](https://accounts.google.com/signin/v2/identifier?service=ah&passive=true&continue=https%3A%2F%2Fuc.appengine.google.com%2F_ah%2Fconflogin%3Fcontinue%3Dhttps%3A%2F%2Fsignup.earthengine.google.com%2F&flowName=GlifWebSignIn&flowEntry=ServiceLogin)).

Anda dapat membuat conda environment (lebih detil [di sini](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)) dan memasang beberapa Python package dengan command berikut:

```python
conda create --name lcms -c conda-forge python=3.8 earthengine-api geopandas mamba
conda activate lcms
mamba install geemap xarray_leaflet -c conda-forge
```

Anda juga dapat memasang [Jupyter notebook extension](https://github.com/ipython-contrib/jupyter_contrib_nbextensions) (tidak wajib).
```python
conda install jupyter_contrib_nbextensions -c conda-forge
```

## Usage
### Sample generator
**Sample generator** akan membuat *unlabelled samples dataset* yang sebaran dan jumlahnya didasari hasil K-Means Clustering. Pada bagian **User Editable Part** pengguna dapat mengisikan variabel jumlah kluster (k) dan jumlah total titik sampel yang ingin dibuat. Titik sampel akan dibuat dengan jumlah proporsional berdasarkan luasan dari setiap kluster. Selain itu pengguna juga perlu mengisikan beberapa parameter lain seperti id asset GEE citra yang akan digunakan (**ee.Image()**), area pemetaan, dan folder **Google Drive** untuk ekspor data sampel yang dihasilkan.
```python
### USER EDITABLE PART

# Bagian 1A: nama file object Earth Engine berikut alamat asset-nya
# Contoh: "users/kfaisal/LCMS_Borneo_2016/L8_Borneo_2016_int" 
img_asset = "users/kfaisal/LCMS_Borneo_2016/L8_Borneo_2016_int"

# Bagian 1B: kombinasi RGB
# Bands: {B2 = Blue, B3 = Green, B4 = Red, B5 = NIR, B6 = SWIR1, B7 = SWIR2}
# Pilihan kombinasi RGB yang tersedia: "432" (komposit warna asli), "543", "562", "563", "564", "567"
comp_rgb = "562"

# Bagian 2: Study area
# Pilihan: Sumatera, Jawa, Bali_NusaTenggara, Borneo, Sulawesi, Maluku, Papua
studyArea = "Borneo" # jangan lupa membubuhkan tanda petik

# Bagian 3: Jumlah K-Means clusters
num_cluster = 15

# Bagian 4: Jumlah titik sampel yang diinginkan
num_out_samples = 1000

# Bagian 5: Parameter ekspor samples ke Google Drive
folderName = "LCMS_samples"
exportName = "Borneo_2016"
```
