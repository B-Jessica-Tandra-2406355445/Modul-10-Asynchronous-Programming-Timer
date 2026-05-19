# Advanced Programming - Module 10
**Asynchronous Programming**

## Experiment 1.2: Understanding how it works

### Hasil Eksekusi
> ![](images\Screenshot 2026-05-19 132215.png)

Setelah baris `println!("Jessica's Komputer: hey hey");` ditambahkan di luar blok `spawner.spawn`, urutan teks yang dicetak di terminal berubah menjadi:
1. `Jessica's Komputer: hey hey`
2. `Jessica's Komputer: howdy!`
3. *(jeda 2 detik)*
4. `Jessica's Komputer: done!`

Hal ini terjadi karena fungsi `spawner.spawn(...)` tidak langsung menjalankan *future* atau *task* pada saat itu juga. Fungsi tersebut hanya bertugas membuat sebuah *task* baru dan memasukkannya (*enqueue*) ke dalam *channel* milik *executor* agar siap dieksekusi nanti.

Karena *task* tersebut tidak langsung dijalankan (sifat *lazy* pada Rust Future), *thread* utama (*main thread*) akan terus melanjutkan eksekusi ke baris kode berikutnya secara sinkron. Oleh karena itu, program langsung mengeksekusi `println!("Jessica's Komputer: hey hey");` yang berada di bawahnya. 

*Task* *asynchronous* yang berisi `howdy!` dan `done!` baru benar-benar dieksekusi (di-*poll*) ketika program memanggil fungsi `executor.run()` di baris paling akhir. Saat `executor.run()` dipanggil, program mulai mengambil *task* dari antrean dan menjalankannya, sehingga `howdy!` tercetak, kemudian *task* tersebut ditunda (*yield*) selama 2 detik oleh `TimerFuture`, dan akhirnya diselesaikan dengan mencetak `done!`.