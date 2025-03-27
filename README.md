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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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
1. Dalam pattern Observer, semua subscriber perlu mengimplementasi interface yang sama agar implementasi yang berbeda-beda akan tetap merespon pembaruan secara konsisten. Oleh karena itu, dalam konteks BambangShop memiliki beberapa jenis subscriber, lebih baik interface/trait digunakan dalam project agar semua subscriber memiliki perilaku sesuai yang sama. Untuk sekarang, hanya terdapat satu jenis subscriber maka boleh menggunakan hanya satu struct Model. Namun, agar proyek lebih fleksibel jika ingin menambah jenis subscriber lain, lebih baik kita menggunakan interface/trait sejak awal.

2. Karena id di Program dan url di Subscriber harus unik, maka DashMap lebih sesuai untuk digunakan. Hal ini disebabkan Vec (list) merupakan list biasa yang tidak menjamin keunikan data, sehingga jika kita mau memastikan data tetap unik, kita harus melakukan iterasi manual satu per satu. Sementara, DashMap (map/dictionary) secara otomatis menjamin keunikan data, karena key dalam map/dictionary harus unik sehingga penggunaan DashMap untuk memastikan id dan url unik jauh lebih efisien dari Vec. 

3. Dalam kasus ini, kita memerlukan DashMap karena akan menghandle banyak thread (subscribers) dalam waktu yang bersamaan. DashMap merupakan HashMap tanpa lock, jadi bisa diakses banyak thread tanpa menyebabkan bottleneck dan aman untuk concurrency. Di atas itu, kita juga lebih baik menggunakan Singleton pattern untuk memastikan hanya ada satu instance dari HashMap yang digunakan sembari DashMap memastikan concurrency aman.

#### Reflection Publisher-2
1. Kita memisahkan Service dan Repository dari Model di MVC karena jika tidak, maka Model akan memiliki terlalu banyak tanggung jawab, yaitu representasi data, handling database, dan memproses logika bisnis. Hal ini melanggar prinsip SRP dari SOLID. Agar kode lebih mudah dipelihara dan dipahami, maka kita pisahkan masing-masing tanggungjawabnya, yaitu Model untuk representasi data, Repository untuk handling CRUD data, dan Service untuk mengurus logika bisnis. Dengan ini, kode akan lebih mudah dikelola, lebih bersih, lebih mudah dites, dan kita juga bisa merubah database tanpa mengubah logika bisnis.

2. Jika kita hanya menggunakan Model, artinya Model akan menghandle ketiga tanggung jawab yang disebutkan pada poin satu. Hal ini akan menyebabkan kode menjadi susah dibaca dan dipelihara karena semua fungsi tertumpuk pada model dan bisa bergantung pada satu sama lain. Tentunya, ini juga melanggar Single Responsibility Principle (SRP) dari SOLID. Selain itu, potongan kode akan memiliki reusability rendah karena dicampur menjadi satu, dan testing juga menjadi lebih sulit. Jika kita hanya menggunakan Model di proyek ini, maka semua dihandle oleh Model: Model harus query Subscriber, membuat data Notification, dan kirim notifikasi ke API eksternal. Karena semua tanggung jawab tergabung menjadi satu, maka ada kemungkinan kompleksitas kode meningkat.

3. Ya, saya sudah mengeksplorasi Postman. Fitur-fitur yang saya rasa akan membantu saya, yaitu request testing (langsung API GET, POST, PUT, DELETE tanpa coding di FE/BE dahulu), collection (simpan request API dalam satu set), environment variables (ganti URL atau token API tanpa harus manual mengedit di setiap request), dan automated tests (skrip untuk mengecek apakah API mengembalikan data yang benar/error). Postman akan sangat membantu menghemat waktu saya dan rekan-rekan saya dalam proses pengerjaan suatu proyek.

#### Reflection Publisher-3
1. Pada tutorial kali ini, kita menggunakan Push Model, yaitu publisher (BambangShop) mengirim data ke subscriber ketika ada perubahan data (seperti create, update, delete). Perubahan data memicu NotificationService untuk mengiterasi list subscriber dan mengirim notifikasi ke subscriber, tanpa subscriber perlu meminta.

2. Jika kita menggunakan Pull Model, maka kelebihannya adalah kita bisa lebih efisien dan menghemat resources karena notifikasi hanya perlu dikirim ketika subscriber meminta, dan kontrol sepenuhnya diberikan kepada subscriber. Di sisi lain, kekurangannya adalah subscriber tidak menerima data tepat waktu, bisa terjadi delay karena subscriber harus mengecek sendiri apakah ada data baru. Lalu, boro-boro menghemat resource, bisa jadi malah boros jika subscriber terus menerus mengecek data meski tidak ada perubahan baru. Metode ini juga kurang cocok untuk real-time updates.

3. Jika kita tidak menggunakan multi-threading, notifikasi akan dikirim ke satu per satu subscriber secara berurutan/synchronous. Akibatnya, yaitu proses notifikasi akan menjadi lambat, program utama juga dapat menjadi lambat atau bahkan freeze akibat proses notifikasi, dan program menjadi tidak scalable apabila jumlah subscriber terus bertambah.