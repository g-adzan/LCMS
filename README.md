# Sampling tools
## Land Cover Monitoring System

Land Cover Monitoring System merupakan inisiatif kegiatan pemetaan penutup lahan dan pemantauan perubahannya melalui *remote sensing*. Salah satu metode utama yang digunakan dalam memetakan penutup lahan adalah *supervised machine learning*. Tools ini dibuat untuk memudahkan proses pembuatan titik sampel yang terstruktur dan juga memberikan label yang akan digunakan sebagai *input* dalam *supervised machine learning*. Tools ini dibuat dalam platform `Jupyter` dengan menggunakan beberapa *package* seperti `ipywidgets`, `ipyleaflet`, `geopandas`, dan `ee`.

Tools ini terdiri dari dua bagian yang saling berkaitan, yaitu pembuatan `stratified samples` yang distribusinya didasari hasil K-Means Clustering dan *user interface* berbasis `ipyleaflet` dan `ipywidgets` untuk pemberian label penutup lahan.

![Animation8](https://user-images.githubusercontent.com/60416865/130492601-95383dc2-22ca-4cdd-a29b-65702038eade.gif)


