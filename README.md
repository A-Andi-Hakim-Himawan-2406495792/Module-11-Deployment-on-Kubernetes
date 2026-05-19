# Module-11-Deployment-on-Kubernetes

## Reflection on Hello Minikube

### 1. Perbandingan Log Aplikasi Sebelum dan Sesudah Ekspos Aktivitas Service
Melalui eksperimen ini, saya mengamati perbedaan yang sangat signifikan pada perilaku log kontainer sebelum dan sesudah adanya interaksi eksternal. 

* **Sebelum Diekspos Sebagai Service:** Saat saya memeriksa log Pod sesaat setelah proses *deployment* berhasil dijalankan, log yang tercatat bersifat statis dan minimalis. Kontainer hanya menampilkan pesan inisialisasi internal sistem, seperti penanda bahwa server HTTP dan server UDP telah berhasil dinyalakan dan siap mendengarkan *traffic* pada port 8080 dan 8081. Pada fase ini, aplikasi seolah-olah berada di dalam ruang isolasi yang belum tersentuh oleh dunia luar kluster.
* **Sesudah Diekspos Sebagai Service:** Kondisi berubah drastis setelah perintah `kubectl expose` dijalankan dan *tunneling* `minikube service` diaktifkan. Ketika tautan URL dibuka di *browser*, setiap tindakan penyegaran (*refresh*) halaman memicu penambahan log baru secara *real-time* di terminal. Kontainer mulai menangkap dan merekam setiap *HTTP request* yang masuk, lengkap dengan metadata interaksi klien. 

**Refleksi Singkat:** Fenomena ini menyadarkan saya bahwa *Deployment* tanpa *Service* hanyalah sekadar menjalankan proses komputasi di latar belakang. Log yang bertambah seiring interaksi *browser* membuktikan bahwa komponen *Service* berhasil bertindak sebagai pintu gerbang yang menjembatani jaringan luar menuju Pod di dalam kluster Kubernetes.

---

### 2. Analisis Penggunaan Opsi `-n` (*Namespace*) dan Isolasi Resource
Latihan ini memberikan pemahaman konkret mengenai konsep *multi-tenancy* dan partisi logis di Kubernetes melalui penggunaan opsi `-n`.

* **Fungsi Opsi `-n`:** Bendera `-n` atau `--namespace` berfungsi sebagai selektor ruang lingkup kerja untuk membatasi perintah `kubectl` agar hanya beroperasi pada kluster virtual (ruang isolasi) tertentu yang kita tuju.
* **Penyebab Pod/Service yang Dibuat Tidak Muncul di `kube-system`:** Ketika saya menjalankan perintah `kubectl get pods, services -n kube-system`, sistem menampilkan komponen infrastruktur internal seperti `coredns`, `kube-proxy`, hingga `metrics-server` yang baru saja dinyalakan. Namun, Pod `hello-node` yang saya buat sebelumnya tidak tercantum di sana. Hal ini terjadi karena ketika kita membuat *Deployment* tanpa mendefinisikan *namespace* secara eksplisit, Kubernetes secara otomatis akan menempatkan seluruh *resource* tersebut ke dalam *namespace* bernama `default`.

**Refleksi Singkat:** Melalui pemisahan ini, saya memahami bahwa *Namespace* berperan penting dalam manajemen arsitektur kluster skala besar. Isolasi antara *namespace* `default` (untuk aplikasi *user*) dan `kube-system` (untuk kebutuhan internal kluster) memastikan bahwa aktivitas mendeploy atau menghapus aplikasi kita tidak akan mengganggu stabilitas core engine dari sistem Kubernetes itu sendiri.
