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
**Land Cover Sampling Tool** dapat digunakan untuk mendesain dan memberikan label pada sampel yang dapat digunakan dalam proses klasifikasi citra penginderaan jauh dengan teknik *supervised machine learning*. Aplikasi ini dibangun pada platform Jupyter Notebook dengan menggunakan beberapa Python package seperti `ee`, `geopandas`, `ipywidgets`, dan `ipyleaflet`. Aplikasi ini terdiri dari dua bagian: 
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
