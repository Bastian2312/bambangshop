# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [v] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [v] Commit: `Create Subscriber model struct.`
    -   [v] Commit: `Create Notification model struct.`
    -   [v] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [v] Commit: `Implement add function in Subscriber repository.`
    -   [v] Commit: `Implement list_all function in Subscriber repository.`
    -   [v] Commit: `Implement delete function in Subscriber repository.`
    -   [v] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [v] Commit: `Create Notification service struct skeleton.`
    -   [v] Commit: `Implement subscribe function in Notification service.`
    -   [v] Commit: `Implement subscribe function in Notification controller.`
    -   [v] Commit: `Implement unsubscribe function in Notification service.`
    -   [v] Commit: `Implement unsubscribe function in Notification controller.`
    -   [v] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Kita tidak memerlukan interface atau trait karena hanya ada satu tipe Subscriber (struct Subscriber). Observer pattern biasanya menggunakan interface jika ada banyak jenis observer dengan perilaku berbeda, tetapi di sini, struct sederhana sudah cukup karena tidak ada variasi perilaku yang perlu di-abstract.

2. Menggunakan Vec tidak cukup efisien karena pencarian subscriber berdasarkan url atau id akan membutuhkan iterasi manual. DashMap lebih cocok karena struktur key-value-nya memungkinkan pencarian dalam waktu konstan, sekaligus aman untuk multithreading.

3. DashMap tetap diperlukan meskipun kita menerapkan Singleton pattern. Singleton hanya memastikan satu instance SUBSCRIBERS yang ada, tetapi DashMap menambahkan thread-safety dengan lock-free reads, sehingga aman diakses oleh banyak thread secara bersamaan tanpa deadlock. Tanpa DashMap, kita harus mengelola locking manual yang lebih rentan error.


#### Reflection Publisher-2

1. Dalam MVC, Model biasanya menangani data dan logika bisnis sekaligus, tetapi memisahkan Service (logika bisnis) dan Repository (akses data) mengikuti prinsip Single Responsibility Principle (SRP). Service fokus pada business logic (seperti validasi atau notifikasi), sementara Repository hanya menangani penyimpanan/data (seperti SubscriberRepository). Pemisahan ini membuat kode lebih modular, mudah di-maintain, dan mengurangi coupling.

2. Jika semua logika bisnis dan penyimpanan digabung dalam Model, interaksi antar-model (misalnya Program, Subscriber, Notification) akan menjadi sangat kompleks. Contoh: Product harus langsung mengakses database (DashMap) dan mengirim notifikasi, yang melanggar prinsip separation of concerns. Kode menjadi kacau (tightly coupled) dan sulit di-debug.

3. Postman sangat membantu dalam menguji API. Saya menggunakannya untuk mengirim request, melihat respons, dan memastikan fitur backend bekerja dengan baik. Fitur seperti Collection membantu mengatur request, sementara Environment Variables menyimpan nilai yang sering digunakan seperti token atau URL. Postman juga mendukung Automated Testing, sehingga kita bisa memverifikasi respons API secara otomatis. Ini membuat debugging lebih cepat dan pengujian lebih sistematis, sangat berguna untuk proyek sekarang maupun di masa depan.

#### Reflection Publisher-3

1. Pada tutorial ini, kita menggunakan model Push dalam Observer Pattern. Dalam model ini, publisher secara aktif mengirimkan notifikasi ke semua subscriber setiap kali terjadi perubahan data, seperti pembuatan, penghapusan, atau publikasi produk. Hal ini terlihat dari implementasi NotificationService.notify, yang mengiterasi semua subscriber dan mengirimkan notifikasi menggunakan metode Subscriber.update. Dengan pendekatan ini, subscriber tidak perlu meminta data secara manual, mereka hanya menerima notifikasi saat ada perubahan.

2. Jika kita menggunakan model Pull sebagai alternatif, terdapat beberapa keuntungan dan kerugian. Keuntungannya, subscriber memiliki kontrol lebih besar terhadap data yang mereka ambil, sehingga mereka hanya menarik informasi saat dibutuhkan. Ini dapat mengurangi beban server dan menghindari pengiriman data yang tidak diperlukan. Selain itu, model Pull dapat mengurangi resiko overloading pada subscriber karena mereka mengambil data sesuai kapasitas mereka sendiri. Namun, kelemahannya adalah subscriber harus secara berkala memeriksa apakah ada pembaruan, yang dapat menyebabkan penggunaan CPU yang tinggi dan latensi dalam menerima informasi.

3. Jika program tidak menggunakan multithreading dalam proses notifikasi, setiap subscriber akan diberi tahu secara berurutan dalam satu thread utama. Hal ini dapat menyebabkan bottleneck karena program harus menunggu hingga satu subscriber selesai menerima notifikasi sebelum melanjutkan ke subscriber berikutnya. Sebagai akibatnya, proses notifikasi menjadi lebih lambat dan tidak efisien, terutama jika jumlah subscriber meningkat. Dalam skenario dengan banyak subscriber, waktu respons aplikasi bisa meningkat drastis, yang mengakibatkan pengalaman pengguna yang buruk dan penurunan skalabilitas sistem.