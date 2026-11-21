# <h1 align="center">Laporan Praktikum Modul 8 QUEUE</h1>
<p align="center">Muhammad Haidar Az Zacky - 109082530035</p>

## Dasar Teori
# QUEUE (Antrean)

## 8.1 Pengertian Queue

**Queue** (dibaca: *kyu*) adalah struktur data berurutan yang cara kerjanya persis antrean di loket kereta api atau kasir: data masuk lewat satu ujung dan keluar lewat ujung yang lain. Siapa yang datang lebih dulu, dilayani lebih dulu. Prinsip inilah yang disebut **FIFO (*First In First Out*)** [1].

```
 enqueue  -->  [ A | B | C | D ]  -->  dequeue
                 ^             ^
               HEAD           TAIL
          (depan, tempat    (belakang, tempat
              keluar)            masuk)
```

Queue memiliki dua penunjuk utama, yaitu **HEAD** (elemen terdepan, tempat data diambil) dan **TAIL** (elemen terbelakang, tempat data disisipkan). Kondisi queue pada representasi *linked list* dibedakan sebagai berikut:

| Kondisi | HEAD | TAIL |
|---|---|---|
| Kosong | `Nil` | `Nil` |
| Satu elemen | menunjuk node tunggal | menunjuk node yang sama dengan HEAD |
| Lebih dari satu elemen | node pertama | node terakhir |

Queue dapat dibangun dengan **array (tabel)** maupun **linked list (pointer)** [2][3]. Struktur ini banyak dipakai dalam sistem komputer, misalnya *ready queue* (antrean proses yang menunggu giliran dijalankan) pada sistem operasi dengan algoritma FCFS [2], serta antrean data bersama pada aplikasi paralel [5].

---

## 8.2 Operasi-Operasi dalam Queue

Queue memiliki dua operasi utama yang masing-masing bekerja di satu ujung saja:

| Operasi | Nama lain | Ujung yang dipakai | Fungsi |
|---|---|---|---|
| **Enqueue** | Insert | TAIL | Menambah elemen baru di belakang antrean |
| **Dequeue** | Delete | HEAD | Mengambil elemen terdepan dari antrean |

### 8.2.1 Insert (Enqueue)

Penyisipan selalu di **TAIL**. Prosesnya tiga langkah: (1) buat elemen baru dengan `next = Nil`, (2) sambungkan `tail->next` ke elemen baru, (3) geser `tail` agar menunjuk elemen baru. Jika queue masih kosong, elemen baru langsung menjadi HEAD sekaligus TAIL.

### 8.2.2 Delete (Dequeue)

Penghapusan selalu di **HEAD**. Prosesnya: (1) simpan `head` ke variabel penampung `P`, (2) geser `head` ke elemen berikutnya, (3) putus hubungan `P` dari antrean (`P->next = Nil`), (4) kembalikan `P`. Jika elemen yang diambil adalah satu-satunya elemen, HEAD dan TAIL dikembalikan menjadi `Nil`.

Karena kedua operasi hanya menyentuh ujung antrean dan tidak perlu menelusuri isinya, keduanya selalu cepat dan lamanya tidak bergantung pada banyaknya data (**O(1)**) pada representasi *linked list*. Kecepatan seperti ini juga menjadi tujuan utama pada rancangan queue lain [4].

---

## 8.3 Primitif-Primitif dalam Queue

Primitif disimpan dalam ADT (*Abstract Data Type*) yang dipisah menjadi file header `*.h` (deklarasi) dan file implementasi `*.cpp`. Pemisahan ini membuat cara memakai queue tetap sama walaupun isi di dalamnya diganti.

| Kelompok | Primitif | Fungsi |
|---|---|---|
| Pengecekan | `isEmpty` | Menyatakan queue kosong atau tidak |
| Pembentukan | `CreateQueue` | Membentuk queue kosong |
| Memori | `alokasi`, `dealokasi` | Membuat dan melepas node di memori |
| Pencarian | `findElm`, `fFindElm` | Mencari elemen berdasarkan nilai atau alamat |
| Penambahan | `insert` | Menyisipkan elemen di akhir queue |
| Penghapusan | `delete` | Mengambil elemen pertama queue |
| Proses elemen | `printInfo`, `nbList`, `delAll` | Menampilkan, menghitung, dan menghapus seluruh elemen |

---

## 8.4 Queue (Representasi Tabel)

### 8.4.1 Pengertian

Pada representasi tabel, elemen queue disimpan dalam array berkapasitas tetap. Perbedaannya dengan representasi pointer:

| Aspek | Representasi Tabel | Representasi Pointer |
|---|---|---|
| Kapasitas | Terbatas (ukuran array) | Tidak terbatas (sesuai memori) |
| Manajemen memori | Tidak ada | Ada (`alokasi` dan `dealokasi`) |
| Kondisi penuh | Mungkin terjadi | Hampir tidak pernah |

Terdapat tiga alternatif pengelolaan HEAD dan TAIL [1]:

**A. Alternatif 1 (Head Diam, Tail Bergerak)**

HEAD selalu berada di indeks pertama selama queue tidak kosong, hanya TAIL yang bergerak. *Enqueue* cukup memajukan TAIL. *Dequeue* mengambil elemen HEAD lalu **menggeser seluruh elemen sisa satu langkah ke depan** dan memundurkan TAIL. Cara ini menyerupai orang yang melangkah maju saat antrean berkurang, tetapi boros karena *dequeue* makin lambat jika datanya makin banyak (**O(n)**).

**B. Alternatif 2 (Head Bergerak, Tail Bergerak)**

HEAD dan TAIL sama-sama bergerak maju. *Dequeue* cukup memajukan HEAD tanpa menggeser elemen, sehingga lebih hemat waktu (**O(1)**). Kelemahannya adalah kondisi **penuh semu**: TAIL sudah di ujung array padahal masih ada ruang kosong di depan HEAD. Solusinya, semua elemen digeser (dirapatkan) ke awal array, dan ini hanya dilakukan ketika TAIL sudah mencapai indeks paling ujung.

**C. Alternatif 3 (Head dan Tail Berputar / *Circular Buffer*)**

Indeks HEAD dan TAIL dianggap berputar: setelah indeks terakhir, posisi berikutnya kembali ke indeks awal, dihitung dengan rumus `(i + 1) % MAX` (tanda `%` berarti sisa bagi). Ruang kosong di depan langsung dapat dipakai ulang tanpa pergeseran, sehingga *enqueue* dan *dequeue* sama-sama **O(1)**. Variasi lain adalah mengganti TAIL dengan `COUNT` (jumlah elemen), sehingga banyaknya elemen langsung diketahui, tetapi posisi TAIL harus dihitung setiap kali menambah elemen.

| Alternatif | HEAD | Enqueue | Dequeue | Kelemahan |
|---|---|---|---|---|
| 1 | Diam | O(1) | O(n), ada pergeseran | Boros waktu saat dequeue |
| 2 | Bergerak | O(1) | O(1) | Penuh semu, perlu dirapatkan |
| 3 | Berputar | O(1) | O(1) | Rumus indeksnya lebih rumit |

### 8.4.2 Primitif-Primitif dalam Queue (Representasi Tabel)

ADT queue representasi tabel dideklarasikan pada `queue.h` dan mencakup `isFull`, `isEmpty`, `CreateQueue` (membentuk queue dengan `head = -1` dan `tail = -1`), `enQueue`, `deQueue`, serta `viewQueue` untuk menampilkan isi antrean.



## Guided 1

### 1. Queue.h

```C++
#ifndef QUEUE_H_INCLUDE
#define QUEUE_H_INCLUDE

#include <iostream>
using namespace std;

#define Nil NULL

typedef bool boolean;
typedef int infotype;
typedef struct elmQ *address;

struct elmQ{
    infotype info;
    address next;
};

struct queue {
    address head, tail;
};

boolean isEmpty(queue Q);
void CreateQueue(queue &Q);
address alokasi(infotype X);
void dealokasi(address P);
address findElm(queue Q, infotype X);
boolean fFindElm(queue Q, address P);
void insert(queue &Q, address P);
void deleteQueue(queue &Q, address &P);
void printInfo(queue Q);
int nbList(queue Q);
void delAll(queue &Q);

#endif
```
File `queue.h` adalah *header* yang mendefinisikan ADT Queue berbasis *linked list* dengan isi bertipe integer. Struktur `elmQ` berperan sebagai node (berisi `info` dan pointer `next`), `address` adalah pointer ke node tersebut, dan struktur `queue` memegang dua pointer, yaitu `head` dan `tail`. Makro `Nil` dipakai sebagai pengganti `NULL`, dan `typedef bool boolean` menyesuaikan tipe logika dengan gaya modul. Prototipe yang dideklarasikan mencakup pengecekan (`isEmpty`), pembentukan (`CreateQueue`), manajemen memori (`alokasi`, `dealokasi`), pencarian (`findElm`, `fFindElm`), penambahan (`insert`), penghapusan (`deleteQueue`), serta pemrosesan seluruh elemen (`printInfo`, `nbList`, `delAll`). Fungsi penghapusan dinamai `deleteQueue` karena `delete` sudah menjadi kata kunci bawaan C++.

### 2. Queue.cpp

```C++
#include "queue.h"

boolean isEmpty(queue Q) {
    return (Q.head == Nil);
}

void CreateQueue(queue &Q) {
    Q.head = Nil;
    Q.tail = Nil;
}

address alokasi(infotype X) {
    address P = new (nothrow) elmQ;
    if (P != Nil) {
        P->info = X;
        P->next = Nil;
    }
    return P;
}

void dealokasi(address P) {
    delete P;
}

address findElm(queue Q, infotype X) {
    address P = Q.head;
    while (P != Nil) {
        if (P->info == X) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

boolean fFindElm(queue Q, address P) {
    address R = Q.head;
    while (R != Nil) {
        if (R == P) {
            return true;
        }
        R = R->next;
    }
    return false;
}

void insert(queue &Q, address P) {
    if (isEmpty(Q)) {
        Q.head = P;
        Q.tail = P;
    } else {
        (Q.tail)->next = P;
        Q.tail = P;
    }
}

void deleteQueue(queue &Q, address &P) {
    P = Q.head;
    if (Q.head == Q.tail) {
        Q.head = Nil;
        Q.tail = Nil;
    } else {
        Q.head = P->next;
    }
    P->next = Nil;
}

void printInfo(queue Q) {
    if (isEmpty(Q)) {
        cout << "Queue kosong" << endl;
    } else {
        address P = Q.head;
        while (P != Nil) {
            cout << P->info << " ";
            P = P->next;
        }
        cout << endl;
    }
}

int nbList(queue Q) {
    int jumlah = 0;
    address P = Q.head;
    while (P != Nil) {
        jumlah++;
        P = P->next;
    }
    return jumlah;
}

void delAll(queue &Q) {
    address P;
    while (!isEmpty(Q)) {
        deleteQueue(Q, P);
        dealokasi(P);
    }
}
```
File `queue.cpp` berisi isi dari setiap prototipe di `queue.h`. `isEmpty` menganggap queue kosong bila `head` bernilai `Nil`, dan `CreateQueue` mengosongkan `head` serta `tail`. `alokasi` membuat node baru dengan `new (nothrow)`, sehingga jika memori tidak cukup hasilnya `Nil` dan program tidak langsung berhenti, sedangkan `dealokasi` melepas node dengan `delete`. Pencarian dilakukan dengan menelusuri node dari `head`: `findElm` membandingkan nilai `info`, sedangkan `fFindElm` membandingkan alamat node. `insert` menyambungkan node baru ke `tail->next` lalu memajukan `tail` (dengan kasus khusus saat queue kosong), sementara `deleteQueue` mengambil node di `head`, memajukan `head`, dan mereset `head` dan `tail` bila node tersebut adalah elemen terakhir. Tiga fungsi sisanya menelusuri antrean: `printInfo` menampilkan seluruh isi, `nbList` menghitung jumlah node, dan `delAll` mengosongkan queue dengan memanggil `deleteQueue` dan `dealokasi` berulang kali.

### 3. Main.cpp

```C++
#include <iostream>
#include "queue.h"
using namespace std;

int main() {
    queue Q;
    address P;

    CreateQueue(Q);
    cout << "isEmpty : " << isEmpty(Q) << endl;
    printInfo(Q);

    insert(Q, alokasi(5));
    insert(Q, alokasi(2));
    insert(Q, alokasi(7));
    cout << "Setelah insert 5, 2, 7" << endl;
    printInfo(Q);
    cout << "nbList : " << nbList(Q) << endl;

    P = findElm(Q, 2);
    if (P != Nil) {
        cout << "findElm(2) ketemu, info = " << P->info << endl;
        cout << "findElm : " << fFindElm(Q, P) << endl;
    }
    if (findElm(Q, 9) == Nil) {
        cout << "findElm(9) tidak ketemu" << endl;
    }

    deleteQueue(Q, P);
    cout << "setelah delete, elemen yang keluar = " << P->info << endl;
    dealokasi(P);
    printInfo(Q);

    delAll(Q);
    cout << "setelah delAll" << endl;
    printInfo(Q);
    cout << "isEmpty : " << isEmpty(Q) << endl;

    return 0;
}
```
File `main.cpp` adalah program uji yang menjalankan seluruh primitif secara berurutan. Queue dibentuk kosong lalu statusnya dicek, kemudian nilai 5, 2, dan 7 dimasukkan lewat `insert(Q, alokasi(...))` dan jumlah elemennya ditampilkan dengan `nbList`. Setelah itu pencarian diuji dua kali: nilai 2 harus ditemukan (lengkap dengan pengecekan alamat lewat `fFindElm`) dan nilai 9 harus tidak ditemukan. Tahap berikutnya menguji *dequeue*: `deleteQueue` mengeluarkan elemen terdepan (5) yang langsung dilepas dengan `dealokasi`, menyisakan `2 7`. Terakhir, `delAll` mengosongkan seluruh queue dan `isEmpty` dicek ulang untuk memastikan antrean benar-benar kosong.

## Guided 2

### 1. Queue.h

```C++
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

const int MAKSIMAL = 5;

struct queue {
    string nama[MAKSIMAL];
    int head;
    int tail;
};

bool isFull(queue Q);
bool isEmpty(queue Q);
void CreateQueue(queue &Q);
void enQueue(queue &Q, string nama);
void deQueue(queue &Q);
void viewQueue(queue Q);

#endif
```
File `queue.h` mendefinisikan ADT Queue berbasis **array (tabel)** yang menyimpan data bertipe `string`. Konstanta `MAKSIMAL = 5` menetapkan kapasitas antrean, sedangkan struktur `queue` berisi array `nama` serta dua indeks, yaitu `head` (posisi depan) dan `tail` (posisi belakang). Prototipe yang tersedia adalah `isFull` dan `isEmpty` untuk membaca kondisi, `CreateQueue` untuk membuat antrean kosong, `enQueue` untuk menambah data, `deQueue` untuk mengeluarkan data terdepan, dan `viewQueue` untuk menampilkan isi antrean.

### 2. Queue.cpp

```C++
#include "queue.h"
#include <iostream>

using namespace std;

// NOTE : 
// Implementasi 1 = head diam, tail bergerak (Queue Linear Statis, kerana head nya tetap diam)
// Implementasi 2 = head bergerak, tail bergerak (Queue Linear Dinamis, karena head & tail nya sama-sama bergerak)
// Implementasi 3 = head dan tail berputar (Queue Circular, karena jika udh mentok tapi masih ada space, diputar sehingga tail bisa ada didepan head)

bool isEmpty(queue Q){
    if(Q.head == -1 && Q.tail == -1){
        return true;
    } else {
        return false;
    }
}

//isFull implmenetasi 1 & 2
bool isFull(queue Q){
    if(Q.tail == MAKSIMAL - 1){
        return true;
    } else {
        return false;
    }
}

// //isFull implementasi 3
// bool isFull(queue Q){
//     if((Q.tail + 1) % MAKSIMAL == Q.head){
//         return true;
//     } else {
//         return false;
//     }
// }

void CreateQueue(queue &Q){ //terbentuk queue dengan head = -1 dan tail = -1 
    Q.head = -1;
    Q.tail = -1;
}
 

//enqueue implementasi 1 & 2
void enQueue(queue &Q, string nama){
    if(isFull(Q) == true){
        cout << "Queue sudah penuh!" << endl;
    } else {
        if(isEmpty(Q) == true){
            Q.head = Q.tail = 0;
        } else {
            Q.tail++;
        }
        Q.nama[Q.tail] = nama;
        cout << "nama " << nama << " berhasil ditambahkan kedalam queue!" << endl;
    }
}

// //enQueue implementasi 3
// void enQueue(queue &Q, string nama){
//     if(isFull(Q) == true){
//         cout << "Queue sudah penuh!" << endl;
//     } else {
//         if(isEmpty(Q) == true){
//             Q.head = Q.tail = 0;
//         } else {
//             Q.tail = (Q.tail + 1) % MAKSIMAL; // bergerak melingkar
//         }
//         Q.nama[Q.tail] = nama;
//         cout << "nama " << nama << " berhasil ditambahkan kedalam queue!" << endl;
//     }
// }

//dequeue implementasi 1
void deQueue(queue &Q){
    if(isEmpty(Q) == true){
        cout << "Queue kosong!" << endl;
    } else {
        cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
        for(int i = 0; i < Q.tail; i++){
            Q.nama[i] =  Q.nama[i+1];
        }
        Q.tail--;
        if(Q.tail < 0){ //kalo semua isi queue nya udh dikelaurin, set head & tail ke -1
            Q.head = -1;
            Q.tail = -1;
        }
    }
}

// //dequeue implementasi 2
// void deQueue(queue &Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
//         Q.head++;
//         if(Q.head > Q.tail){ //kalo elemennya udh abis (head akan lebih 1 dari tail), maka reset ulang head & tail ke -1
//             Q.head = -1;
//             Q.tail = -1;
//         }
//     }
// }

// //deQueue implementasi 3
// void deQueue(queue &Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
//         if(Q.head == Q.tail){ //kalo elemennya tinggal 1, langsungkan saja head & tail nya reset ke -1
//             Q.head = -1;
//             Q.tail = -1;
//         } else {
//             Q.head = (Q.head + 1) % MAKSIMAL; // bergerak melingkar
//         }
//     }
// }

//viewQueue implementasi 1 & 2
void viewQueue(queue Q){
    if(isEmpty(Q) == true){
        cout << "Queue kosong!" << endl;
    } else {
        for(int i = Q.head; i <= Q.tail; i++){
            cout << i -  Q.head + 1 << ". " << Q.nama[i] << endl;
        }
    }
    cout << endl;
}

// //viewQueue implementasi 3
// void viewQueue(queue Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         int i = Q.head;
//         int count = 1;
//         while(true){
//             cout << count << ". " << Q.nama[i] << endl;
//             if(i == Q.tail){
//                 break;
//             }
//             i = (i + 1) % MAKSIMAL;
//             count++;
//         }   
//     }
// }
```
File `queue.cpp` menyimpan tiga rancangan antrean sekaligus. Yang **aktif** adalah Implementasi 1 (head diam, tail bergerak), sedangkan Implementasi 2 (head bergerak) dan Implementasi 3 (melingkar) disiapkan dalam bentuk komentar supaya bisa dipakai dengan cara menggantinya. Antrean dianggap kosong ketika `head` dan `tail` sama-sama `-1`, dan dianggap penuh ketika `tail` sudah berada di indeks `MAKSIMAL - 1`. `enQueue` memasukkan data di posisi `tail` (jika masih kosong, `head` dan `tail` dimulai dari 0), lalu menampilkan pesan keberhasilan, atau pesan penuh jika kapasitas habis. `deQueue` pada Implementasi 1 menampilkan nama yang dihapus, menggeser semua data satu langkah ke kiri, dan memundurkan `tail`; bila `tail` menjadi negatif, `head` dan `tail` dikembalikan ke `-1`. `viewQueue` mencetak isi dari `head` sampai `tail` dengan nomor urut. Pada versi komentar, Implementasi 2 cukup memajukan `head` saat *dequeue*, sedangkan Implementasi 3 memakai rumus sisa bagi `% MAKSIMAL` agar indeks berputar.

### 3. Main.cpp

```C++
#include "queue.h"
#include <iostream>

using namespace std;

int main(){
    queue Q;

    CreateQueue(Q);
    enQueue(Q, "Dimas");
    enQueue(Q, "Arvin");
    enQueue(Q, "Rizal");
    enQueue(Q, "Hafizh");
    enQueue(Q, "Fathur");
    enQueue(Q, "Daffa");
    cout << endl;

    cout << "--- Isi Queue Setelah enQueue ---" << endl;
    viewQueue(Q);
    cout << endl;

    deQueue(Q);
    deQueue(Q);
    deQueue(Q);
    deQueue(Q);
    // deQueue(Q);
    // deQueue(Q);
    cout << endl;

    cout << "--- Isi Queue Setelah deQueue ---" << endl;
    viewQueue(Q);

    return 0;
}
```
File `main.cpp` menguji ADT antrean array dengan skenario kapasitas. Antrean dibentuk, lalu enam nama dimasukkan berurutan; karena kapasitasnya hanya lima, nama terakhir (Daffa) ditolak dengan pesan "Queue sudah penuh". Isi antrean kemudian ditampilkan, disusul empat kali `deQueue` yang menghapus Dimas, Arvin, Rizal, dan Hafizh secara berurutan. Tampilan akhir hanya menyisakan Fathur. Dua baris `deQueue` terakhir sengaja dikomentari agar antrean tidak dikosongkan seluruhnya.

## Unguided 

### 1. Buatlah ADT Queue menggunakan ARRAY sebagai berikut di dalam file “queue.h”: 
Buatlah implementasi ADT Queue pada file “queue.cpp” dengan menerapkan mekanisme 
queue Alternatif 1 (head diam, tail bergerak).

```C++
// queue.h
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

const int MAX = 5;
typedef int infotype;

struct Queue {
    infotype info[MAX];
    int head, tail;
};

void CreateQueue(Queue &Q);
bool isEmptyQueue(Queue Q);
bool isFullQueue(Queue Q);
void enqueue(Queue &Q, infotype x);
infotype dequeue(Queue &Q);
void printInfo(Queue Q);

#endif
```
```C++
// queue.cpp
#include "queue.h"

void CreateQueue(Queue &Q) { Q.head = Q.tail = -1; }
bool isEmptyQueue(Queue Q) { return Q.tail == -1; }
bool isFullQueue(Queue Q) { return Q.tail == MAX - 1; }

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
        return;
    }
    if (isEmptyQueue(Q)) Q.head = 0;
    Q.info[++Q.tail] = x;
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[0];
    for (int i = 0; i < Q.tail; i++) Q.info[i] = Q.info[i + 1];
    if (--Q.tail < 0) Q.head = -1;
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << " \t | ";
    if (isEmptyQueue(Q)) cout << "empty queue";
    else for (int i = Q.head; i <= Q.tail; i++) cout << Q.info[i] << " ";
    cout << endl;
}
```
```C++
// main.cpp
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    CreateQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T 	 | Queue Info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q); printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q); printInfo(Q);
    dequeue(Q); printInfo(Q);
    return 0;
}
```
### Output Unguided 1 :

##### Output 1
![Output-8-1.jpg](Output/Output-8-1.png)

**Penjelasan Singkat:**
Soal 1 meminta ADT Queue berbasis array berkapasitas 5 elemen dengan mekanisme **Alternatif 1**: `head` selalu diam di indeks 0 selama antrean berisi, dan hanya `tail` yang bergerak. Setiap kali elemen depan diambil, sisa elemen digeser ke kiri sehingga posisi depan tetap terisi.

**Alur Kode:**
*   **`queue.h`**: Mendeklarasikan struct `Queue` (array `info[5]`, `head`, `tail`) dan enam primitif sesuai soal.
*   **`queue.cpp`**:
    *   `enqueue`: Menolak data jika sudah penuh; jika kosong, `head` diset 0; lalu data disimpan pada `++tail`.
    *   `dequeue`: Mengambil `info[0]`, menggeser elemen sisa ke kiri dengan satu perulangan, lalu `tail` mundur. Jika antrean menjadi kosong, `head` dan `tail` kembali `-1`.
    *   `printInfo`: Menampilkan pasangan `head - tail` dan isi antrean, atau `empty queue`.
*   **`main.cpp`**: Menjalankan skenario soal: tambah 5, 2, 7, lalu hapus satu, tambah 4, lalu hapus dua kali.

**Poin Penting:** Pergeseran di `dequeue` membuat prosesnya makin lambat jika datanya banyak (O(n)), tetapi posisi `head` mudah dipahami karena selalu 0. Terlihat pada keluaran bahwa setelah `5 2 7` dihapus satu elemen, hasilnya `0 - 1 | 2 7` (tail mundur, head tetap).


### 2. Buatlah implementasi ADT Queue pada file “queue.cpp” dengan menerapkan mekanisme 
queue Alternatif 2 (head bergerak, tail bergerak).

```C++
// queue.h
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

const int MAX = 5;
typedef int infotype;

struct Queue {
    infotype info[MAX];
    int head, tail;
};

void CreateQueue(Queue &Q);
bool isEmptyQueue(Queue Q);
bool isFullQueue(Queue Q);
void enqueue(Queue &Q, infotype x);
infotype dequeue(Queue &Q);
void printInfo(Queue Q);

#endif
```
```C++
// queue.cpp
#include <algorithm>
#include "queue.h"

void CreateQueue(Queue &Q) { Q.head = Q.tail = -1; }
bool isEmptyQueue(Queue Q) { return Q.head == -1; }
bool isFullQueue(Queue Q) { return Q.head == 0 && Q.tail == MAX - 1; }

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
        return;
    }
    if (isEmptyQueue(Q)) Q.head = 0;
    else if (Q.tail == MAX - 1) {
        copy(Q.info + Q.head, Q.info + Q.tail + 1, Q.info);
        Q.tail -= Q.head;
        Q.head = 0;
    }
    Q.info[++Q.tail] = x;
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[Q.head];
    if (Q.head == Q.tail) CreateQueue(Q);
    else Q.head++;
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << " \t | ";
    if (isEmptyQueue(Q)) cout << "empty queue";
    else for (int i = Q.head; i <= Q.tail; i++) cout << Q.info[i] << " ";
    cout << endl;
}
```
```C++
// main.cpp
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    CreateQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T 	 | Queue Info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q); printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q); printInfo(Q);
    dequeue(Q); printInfo(Q);

    for (int x : {1, 3, 6, 8, 9}) { enqueue(Q, x); printInfo(Q); }
    dequeue(Q); printInfo(Q);
    enqueue(Q, 10); printInfo(Q);
    return 0;
}
```
### Output Unguided 2 :

##### Output 1
![Output-8-2.jpg](Output/Output-8-2.png)

**Penjelasan Singkat:**
Soal 2 menerapkan **Alternatif 2**: `head` ikut bergerak maju saat *dequeue*, sehingga tidak ada pergeseran elemen dan penghapusan menjadi O(1). Harga yang dibayar adalah munculnya kondisi **penuh semu** ketika `tail` sudah di ujung array, padahal ruang di depan `head` kosong.

**Alur Kode:**
*   **`queue.h`**: Sama dengan soal 1 (struct `Queue` dan enam primitif).
*   **`queue.cpp`**:
    *   `isFullQueue`: Benar-benar penuh hanya jika `head == 0` dan `tail == MAX - 1`.
    *   `enqueue`: Jika `tail` sudah di ujung tetapi `head > 0`, semua elemen disalin ke awal array dengan `copy` (dirapatkan ke depan), baru kemudian data disisipkan. Jika benar-benar penuh, ditampilkan `Queue penuh!`.
    *   `dequeue`: Mengambil `info[head]` lalu `head++`; bila elemen terakhir diambil, queue direset lewat `CreateQueue`.
*   **`main.cpp`**: Memakai skenario yang sama dengan soal 1, lalu ditambah blok demo (isi 1, 3, 6, 8, 9) untuk memperlihatkan proses merapatkan elemen dan kondisi penuh.

**Poin Penting:** Pada keluaran tampak `head` berpindah (`1 - 2`, `2 - 3`, `3 - 3`). Saat `tail` mencapai indeks 4 dan `head` masih di 3, penambahan elemen berikutnya membuat semua elemen dirapatkan ke depan sehingga `head - tail` kembali ke `0 - 2`.


### 3. Buatlah implementasi ADT Queue pada file “queue.cpp” dengan menerapkan mekanisme 
queue Alternatif 3 (head dan tail berputar).

```C++
// queue.h
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

const int MAX = 5;
typedef int infotype;

struct Queue {
    infotype info[MAX];
    int head, tail;
};

void CreateQueue(Queue &Q);
bool isEmptyQueue(Queue Q);
bool isFullQueue(Queue Q);
void enqueue(Queue &Q, infotype x);
infotype dequeue(Queue &Q);
void printInfo(Queue Q);

#endif
```
```C++
// queue.cpp
#include "queue.h"

void CreateQueue(Queue &Q) { Q.head = Q.tail = -1; }
bool isEmptyQueue(Queue Q) { return Q.head == -1; }
bool isFullQueue(Queue Q) { return (Q.tail + 1) % MAX == Q.head; }

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
        return;
    }
    if (isEmptyQueue(Q)) Q.head = 0;
    Q.tail = (Q.tail + 1) % MAX;
    Q.info[Q.tail] = x;
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[Q.head];
    if (Q.head == Q.tail) CreateQueue(Q);
    else Q.head = (Q.head + 1) % MAX;
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << " \t | ";
    if (isEmptyQueue(Q)) cout << "empty queue";
    else {
        int n = (Q.tail - Q.head + MAX) % MAX + 1;
        for (int k = 0; k < n; k++) cout << Q.info[(Q.head + k) % MAX] << " ";
    }
    cout << endl;
}
```
```C++
// main.cpp
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    CreateQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T 	 | Queue Info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q); printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q); printInfo(Q);
    dequeue(Q); printInfo(Q);

    for (int x : {1, 3, 6, 8, 9}) { enqueue(Q, x); printInfo(Q); }
    dequeue(Q); printInfo(Q);
    enqueue(Q, 10); printInfo(Q);
    return 0;
}
```
### Output Unguided 3 :

##### Output 1
![Output-8-3.jpg](Output/Output-8-3.png)

**Penjelasan Singkat:**
Soal 3 menerapkan **Alternatif 3 (circular buffer)**: `head` dan `tail` berputar mengelilingi indeks array memakai rumus sisa bagi `% MAX`. Ruang yang dikosongkan *dequeue* langsung dipakai ulang tanpa menggeser atau merapatkan elemen, sehingga *enqueue* dan *dequeue* sama-sama O(1).

**Alur Kode:**
*   **`queue.h`**: Sama dengan soal 1 dan 2.
*   **`queue.cpp`**:
    *   `isFullQueue`: Penuh bila `(tail + 1) % MAX == head`. Karena kondisi kosong ditandai `head == -1`, rumus ini tetap memakai kelima kotak array.
    *   `enqueue`: `tail = (tail + 1) % MAX`; saat kosong, nilai awal `tail = -1` otomatis menghasilkan indeks 0.
    *   `dequeue`: Mengambil `info[head]` lalu `head = (head + 1) % MAX`; direset bila elemen terakhir diambil.
    *   `printInfo`: Menghitung jumlah elemen `(tail - head + MAX) % MAX + 1`, lalu mencetak melingkar dari `head`.
*   **`main.cpp`**: Skenario soal, ditambah blok demo (isi 1, 3, 6, 8, 9) agar `tail` terlihat berputar ke indeks 0 dan 1.

**Poin Penting:** Pada keluaran demo, `head - tail` berubah menjadi `3 - 0` dan `3 - 1`: `tail` berada di indeks lebih kecil daripada `head`, tetapi urutan isi antrean tetap benar (`4 1 3 6`). Inilah ciri antrean melingkar.

## Kesimpulan

Berdasarkan praktikum Modul 8 tentang struktur data Queue (Antrean), diperoleh kesimpulan berikut:

1. **Konsep Dasar Queue:** Queue adalah struktur data berurutan dengan prinsip **FIFO (*First In First Out*)**.
    *   `enqueue` selalu menambah elemen di ujung belakang (**tail**).
    *   `dequeue` selalu mengambil elemen dari ujung depan (**head**).
    *   Struktur ini banyak dipakai pada sistem nyata, misalnya antrean proses yang menunggu giliran dijalankan oleh sistem operasi [2].

2. **Representasi Linked List (Guided 1):**
    *   Antrean tersusun dari node yang dialokasikan secara dinamis, sehingga kapasitasnya tidak terbatas selain oleh memori.
    *   Penyisipan dan penghapusan selalu cepat (**O(1)**, tidak terpengaruh banyaknya data) karena `head` dan `tail` disimpan sebagai pointer.
    *   Pengelolaan memori menjadi tanggung jawab programmer lewat `alokasi` dan `dealokasi`.

3. **Representasi Array (Guided 2 dan Unguided):** Kapasitas antrean tetap, dengan tiga alternatif pengelolaan indeks:

    | Alternatif | Cara Kerja | Kelebihan | Kekurangan |
    |---|---|---|---|
    | **1. Head diam, tail bergerak** | Elemen digeser ke kiri saat `dequeue` | Sederhana, mirip antrean nyata | `dequeue` O(n) |
    | **2. Head & tail bergerak** | `head` maju tanpa pergeseran | `dequeue` O(1) | Penuh semu, perlu dirapatkan |
    | **3. Head & tail berputar** | Indeks berputar dengan rumus sisa bagi (%) | Semua operasi O(1), ruang terpakai ulang | Logika indeks lebih rumit |

4. **Hal Teknis yang Perlu Diperhatikan:**
    *   Selalu cek kondisi **kosong** sebelum `dequeue` dan kondisi **penuh** sebelum `enqueue`.
    *   Kasus hanya satu elemen harus ditangani khusus: setelah diambil, `head` dan `tail` dikembalikan ke keadaan kosong.
    *   Pada antrean melingkar, penanda kosong `head = -1` membuat seluruh kotak array bisa terisi.

5. **Pemilihan Metode:**
    *   **Linked list** cocok bila jumlah data tidak dapat diperkirakan.
    *   **Array melingkar (Alternatif 3)** cocok bila kapasitas sudah pasti dan dibutuhkan operasi cepat tanpa alokasi memori tambahan.
    *   Penerapan queue pada array maupun linked list ini juga dibahas pada penelitian lain [3].

## Referensi

[1] Modul Praktikum Struktur Data. (n.d.). *Modul 8: QUEUE*. Fakultas Informatika, Telkom University (Informatics Lab).

[2] Trijayanti, A., & Gunawan, I. (2025). Implementasi struktur data antrian queue dalam sistem penjadwalan proses pada sistem operasi. *Jurnal Publikasi Teknik Informatika, 4*(2), 48–53. https://doi.org/10.55606/jupti.v4i2.4170

[3] Sihombing, J. (2019). Penerapan stack dan queue pada array dan linked list dalam Java. *Jurnal Ilmiah INFOKOM, 7*(2). Politeknik Piksi Ganesha. https://garuda.kemdiktisaintek.go.id/documents/detail/3056169

[4] Okasaki, C. (1995). Simple and efficient purely functional queues and deques. *Journal of Functional Programming, 5*(4), 583–592. https://doi.org/10.1017/S0956796800001489

[5] Michael, M. M., & Scott, M. L. (1996). Simple, fast, and practical non-blocking and blocking concurrent queue algorithms. In *Proceedings of the 15th ACM Symposium on Principles of Distributed Computing (PODC '96)* (pp. 267–275). ACM. https://doi.org/10.1145/248052.248106
