# Memulai

**Selamat datang di Aurelia!** Tutorial ini akan mengantar Anda membuat aplikasi sederhana dengan menggunakan Aurelia dan secara singkat menjelaskan konsep utamanya. Kami menganggap Anda sudah familiar dengan JavaScript, HTML, dan CSS. Untuk mendapatkan gambaran singkat, kami sarankan Anda melompat ke bagian yang berjudul "Menyiapkan Halaman HTML" sehingga Anda langsung dapat melihat bagaimana menggunakan Aurelia. Kemudian, ketika Anda siap untuk benar-benar membangun sesuatu, kembali dan baca tentang "Konfigurasi Lingkungan Anda" dan "Menyiapkan Struktur Proyek dan Membangun". Untuk melihat hasil menyelesaikan tutorial ini, silakan lihat di [kerangka proyek navigasi](https://github.com/aurelia/skeleton-navigation/releases) kami.

> **Note:** Mencari panduan ini dalam bahasa lain? Silahkan lihat di [repo dokumentasi kami](https://github.com/aurelia/documentation).

## Konfigurasi Lingkungan Anda

Mari kita mulai dengan mengatur perkakas untuk membangun aplikasi JavaScript modern. Semua perkakas kami dibangun di atas [Node.js](http://nodejs.org/). Jika Anda sudah menginstalnya, bagus! Jika belum, Anda harus pergi ke [situs web resmi](http://nodejs.org/), unduh dan instal. Segala sesuatu yang lain kita perlu akan diinstal melalui manajer paket Node ini ([NPM](https://docs.npmjs.com/getting-started/what-is-npm)). Jika Anda sudah memiliki NPM diinstal, pastikan Anda punya [versi terbaru](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) untuk menghindari masalah dengan perkakas lain.

Pertama, mari kita mulai dengan menginstal [Gulp](http://gulpjs.com/) yang akan kita gunakan untuk otomatisasi *build*. Jika Anda belum memilikinya, Anda dapat menggunakan NPM untuk menginstalnya (Anda mungkin perlu menggunakan `sudo`):

  ```shell
  npm install -g gulp
  ```

Berikutnya, kita perlu menginstal [jspm](http://jspm.io/). Ini akan berfungsi sebagai manajer paket sisi-klien kita. Anda dapat melakukannya seperti ini:

  ```shell
  npm install -g jspm
  ```

> ** Catatan ** jspm, seperti Bower dan Yeoman, memanfaatkan [git](http://git-scm.com/) sehingga Anda perlu menginstal bila Anda tidak memilikinya. Juga, jspm mengkueri GitHub untuk menginstal paket, namun GitHub memiliki batas tingkat untuk permintaan API anonim. Hal ini disarankan bahwa Anda mengkonfigurasi jspm dengan mandat GitHub Anda untuk menghindari masalah. Anda dapat melakukan ini dengan menjalankan `jspm registry config github` dan mengikuti petunjuknya. Jika Anda lebih memilih untuk mengotorisasi jspm oleh akses token daripada password (lihat GitHub `Pengaturan> Access Personal Tokens`), akses`public_repo` untuk token diperlukan.

> ** Catatan:** Tidak ingin menggunakan jspm? Tidak masalah. Semua paket Aurelia juga tersedia melalui [Bower](http://bower.io/).

## Menyiapkan Struktur Proyek dan Membangun

Dengan perkakas terpasang, kita sekarang dapat berfokus untuk membuat sebuah struktur dasar untuk aplikasi Anda. Mulailah dengan [unduh kerangka navigasi](https://github.com/aurelia/skeleton-navigation/releases). *Unzip* dan ubah nama folder ke _navigation-app_.

> ** Catatan:** Atau, Anda dapat menggunakan [Yeoman](http://yeoman.io) untuk menghasilkan kerangka proyek dalam folder target, sebagai berikut:
>
>  ```
>  npm install -g yo generator-aurelia
>  yo aurelia
>  ```
>
> Ini juga menjalankan perintah-perintah `npm` dan `jspm` seperti tercantum di bawah.

Dalam folder Anda sekarang akan menemukan semua yang anda butuhkan termasuk dasar *build*, konfigurasi paket, gaya, dan banyak lagi.

Anda dapat memeriksa berkas _index.html_ yang disediakan dan seluruh file di _src_ dan _dist_, namun kami sarankan menghapus folder ini sebelum belajar dengan tutorial ini. Dengan demikian, Anda dapat lebih efektif belajar bagaimana membangun sebuah aplikasi Aurelia dari awal.

Mari kita menjalankan beberapa perintah.

1. Buka konsol dan mengubah direktori Anda untuk _navigation-app_.

2. Jalankan perintah berikut untuk menginstal plugin Gulp tercantum di bagian _devDependencies_ dari paket manifest.

  ```shell
  npm install
  ```
3. Berikutnya, jalankan perintah berikut untuk menginstal pustaka Aurelia, bootstrap dan font-awesome, tercantum di bagian _jspm.dependencies_ dari manifest paket.

  ```shell
  jspm install -y
  ```

Segala yang telah kita lakukan sejauh ini adalah prosedur standar membangun dan manajemen paket Node.js. Hal tadi tidak memiliki sesuatu yang spesifik hubungannya dengan Aurelia sendiri. Kami hanya mengantar Anda melalui pengaturan proyek JavaScript modern dan membangun konfigurasi dari awal. Anda mungkin akrab dengan ini sudah, tAPI jika tidak maka selamat datang ke dunia baru dan menarik ini!

> ** Catatan: **Bootstrap dan Font-awesome **bukan** dependensi Aurelia. Kami hanya menggunakan mereka sebagai bagian dari tutorial ini agar cepat mencapai tampilan yang cantik.

## Menyiapkan Halaman HTML

Jika Anda telah mengikuti sejauh ini, Anda sekarang memiliki semua pustakaan, konfigurasi *build* dan alat yang Anda butuhkan untuk membuat aplikasi JavaScript keren dengan Aurelia. Hal berikutnya yang harus dilakukan adalah membuat file _index.html_ di root folder proyek Anda. Berkas contoh ini menyediakan template yang baik untuk aplikasi baru berbasis Aurelia.

### index.html
```markup
<!doctype html>
<html>
  <head>
    <title>Aurelia</title>
    <link rel="stylesheet" href="styles/styles.css">
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

Ya, itu dia. Ini adalah satu-satunya halaman HTML dalam aplikasi kita. *Head* HTML ini cukup simpel: hanya sebuah link *stylesheet* dan beberapa metadata. Bagian *body*-nya lah yang menarik.

Mari kita mulai dengan tag script. Pertama kita punya _system.js_, *loader* modul berbasis standar ES6. Itu yang memuat pustaka Aurelia serta kode Anda sendiri. Berikutnya kita punya _config.js_. Ini berisi konfigurasi untuk loader. Ini dibuat secara otomatis setiap kali Anda mengeksekusi perintah jspm. jspm adalah manajer paket sisi-klien yang kami sarankan karena memberikan pengembang pengalaman yang luar biasa dengan mengintegrasikan manajemen paket sisi-klien dengan *loader* modul ES6 modul yang sesuai..

> **Catatan:** *Framework* Aurelia tidak terikat jspm atau SystemJS. Kami juga mendukung API *require-style* seperti RequireJS dan Dojo Loader. Juga, Anda dapat menerapkan loader Anda sendiri dan menangani manajemen paket yang anda inginkan. Namun kami pikir, jspm/SystemJS adalah yang solusi terbaik berorientasi ES6 hari ini dan itu pendekatan yang kami rekomendasikan.

Setelah kita memiliki loader modul dan konfigurasi, kita muat modul `aurelia-bootstrapper` dengan panggilan untuk `System.import`.

Ketika bootstrapper memuat, ia memeriksa dokumen HTML untuk atribut _aurelia-app_. Dalam hal ini akan menemukan bahwa tubuh HTML memiliki `aurelia-app` atribut. Ini akan menyuruh bootstrapper untuk memuat view-model dan view _app_ kita, biasanya terletak di _app.js_ dan _app.html_ dan kemudian menulis mereka sebagai aplikasi Aurelia di DOM.

Tunggu sebentar... kita tidak memiliki view dan view-model _app_. Ummm... SEKARANG BAGAIMANA!?

## Menciptakan Layar Pertama Anda

Dalam Aurelia, elemen antarmuka pengguna terdiri dari _view_ dan _view-model_ pasangan, kadang-kadang disebut bersama-sama sebagai "komponen". _view_ ditulis dengan HTML dan diterjemahkan ke dalam DOM. The _view-model_ ditulis dengan JavaScript dan menyediakan data dan perilaku untuk _view_ tersebut. _databinding_ Aurelia yang kuat akan menghubungkan keduanya, sehingga perubahan data Anda akan tercermin dalam _view_ dan sebaliknya. *Separation of Concerns*ini baik untuk kolaborasi pengembang & desainer, perawatan, fleksibilitas arsitektur, dan bahkan kontrol sumber.

Mari kita lihat cara kerjanya ...

Dalam folder _src_ buatlah file _app.html_ dan file _app.js_. Ini adalah view dan view-model yang akan bootstrapper cari. Mari kita mulai dengan _view-model_ dengan menciptakan kelas sederhana untuk mengadakan _firstName_ dan _lastName_. Kami juga akan menambah properti dihitung untuk _fullName_ dan metode untuk "mengirimkan" data si orang. Akan terlihat seperti ini:

### app.js
```javascript
export class Welcome{
  heading = 'Welcome to the Aurelia Navigation App!';
  firstName = 'John';
  lastName = 'Doe';

  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }

  submit(){
    alert(`Welcome, ${this.fullName}!`);
  }
}
```

Apa?! Apakah itu JavaScript?

Betul! Itu adalah ECMAScript 2016 (sebelumnya ES7), versi berikutnya dari JavaScript yang memperkenalkan banyak fitur baru untuk bahasa. Untungnya file Gulp Anda download di atas telah Anda mengatur dengan [Babel](https://babeljs.io/), sebuah transpiler menakjubkan yang memungkinkan Anda untuk menulis JavaScript baru dan menjalankannya pada browser jaman sekarang. Sekarang Anda dapat menggunakan modul, kelas, lambda, interpolasi string dan banyak lagi. Keren! Jadi, bagaimana Anda membuat _view-model_? Anda membuat kelas polos dan _export_ ke kerangka. Mudah!

> ** Catatan: ** Anda tidak harus menggunakan Babel atau bahkan ES2016 untuk menulis sebuah aplikasi Aurelia. Anda dapat menggunakan bahasa seperti TypeScript dan CoffeeScript ... atau bahasa browser hari ini: ES5 dan ES2015. Yang harus Anda lakukan adalah mengikuti pola standar bahasa untuk menciptakan kelas dan semuanya akan bekerja dengan baik. Kami pikir ES2016 mengagumkan meskipun dan berharap Anda akan mempertimbangkan terlebih dahulu. Untuk mempelajari lebih lanjut tentang versi terbaru dari JavaScript termasuk fitur seperti ekspor modul dan kelas kami sarankan membaca [Panduan Belajar Babel] (http://babeljs.io/docs/learn-es6/).

Oke. Sekarang bahwa kita memiliki _view-model_ dengan beberapa data dasar dan perilaku, mari kita sohibnya... _view_.

### app.html
```markup
<template>
  <section>
    <h2>${heading}</h2>

    <form submit.delegate="submit()">
      <div>
        <label>First Name</label>
        <input type="text" value.bind="firstName">
      </div>
      <div>
        <label>Last Name</label>
        <input type="text" value.bind="lastName">
      </div>
      <div>
        <label>Full Name</label>
        <p>${fullName}</p>
      </div>
      <button type="submit>Submit</button>
    </form>
  </section>
</template>
```

Hal pertama yang harus diperhatikan adalah bahwa semua view terkandung dalam tag `template`, bagian dari spesifikasi W3C untuk Web Components. View khusus ini adalah form input dasar. Lihatlah kontrol input. Apakah Anda melihat `value.bind="firstName"`? Itu melakukan databind _value_ input terhadap properti _firstName_ di view-model. Setiap kali properti view-model berubah, input akan diperbarui dengan nilai baru. Setiap kali Anda mengubah nilai dalam kontrol input, Aurelia akan memasukkan nilai baru ke dalam view-model Anda. Semudah itu.

Ada beberapa hal yang lebih menarik dalam contoh ini. Pada *form group* terakhir yang Anda dapat melihat sintaks ini di isi HTML: `$ {fullName}`. Itu interpolasi string. Ini adalah one-way binding dari view-model ke view yang secara otomatis dikonversi ke string dan diinterpolasi ke dalam dokumen. Lihat juga elemen form. Anda harus memperhatikan ini: `submit.delegate ="submit()"`. Itu event-binding. Ini menggunakan event delegation untuk mengikat event _submit_ sehingga mengeksekusi metode _submit_ setiap form dikirimkan.

> **Catatan:** Jika Anda belum pernah mendengar event delegation, itu adalah teknik yang digunakan untuk lebih efisien menangani event di browser dengan melampirkan event handler tunggal di tingkat dokumen yang menangani semua tipe event, daripada melampirkan *event handlers* ke setiap node.

Mari kita jalankan dan lihat aksinya. Pada konsol Anda, gunakan perintah berikut untuk membangun dan meluncurkan server.

```shell
gulp watch
```

Anda sekarang dapat membuka [http://localhost:9000/](http://localhost:9000/) untuk melihat app. Ketik di form input dan perhatikan bahwa Nama Lengkap akan selalu diperbarui sesuai perubahan pada properti. Klik tombol untuk melihat bahwa metode Anda dipanggil.

> **Catatan:** Jika ini tidak bekerja, coba [update](https://github.com/npm/npm/wiki/Troubleshooting#try-the-latest-stable-version-of-node) ke versi terbaru dari NPM.

> **Catatan:** Aurelia memiliki mekanisme databinding yang menggunakan teknik adaptif untuk memilih cara terbaik untuk mengamati perubahan di setiap properti. Misalnya, jika Anda menggunakan browser dengan dukungan [Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe), baik _firstName_ dan _lastName_ akan diamati dengan strategi itu. Jika tidak, kita akan menghasilkan getter dan setter bahwa perubahan batch untuk Micro Task Queue untuk meniru perilaku Object.observe. Jika properti _fullName_ tidak dapat diamati dengan salah satu dari teknik ini, kita menggunakan pemeriksaan kotor (dirty-checking). TAPI, anda dapat mendeklarasikan dependensinya untuk memungkinkan kita untuk mengamati dengan benar. Kami akan menggunakan teknik terbaik tergantung pada situasi dan Anda bahkan dapat menyambungkan strategi kustom juga untuk "mengajarkan" framework bagaimana untuk mengamati jenis khusus dari pola Model.

> **Catatan:** Perintah `.bind` menggunakan default perilaku yang mengikat untuk properti apapun. Standarnya adalah satu arah mengikat (Model ke View) untuk segala sesuatu kecuali bentuk kontrol, yang default dua arah. Anda selalu dapat menggantikan ini dengan menggunakan perintah mengikat eksplisit `.one-way`,` .two-way` dan `.one-time`. Demikian pula, Anda dapat menggunakan `.delegate` untuk delegasi acara tetAPI Anda juga dapat menggunakan` .trigger` untuk melampirkan langsung ke elemen target.

## Menambahkan Navigasi

Karena ini adalah aplikasi navigasi, kita mungkin harus menambahkan beberapa layar lebih dan mendirikan sebuah router sisi-klien, bukan begitu? Mari kita mulai dengan mengganti nama _app.js_ dan _app.html_ ke _welcome.js_ dan _welcome.html_ masing-masing. Ini akan menjadi halaman pertama dari aplikasi kita. Sekarang, mari kita buat _app.js_ baru dan _app.html_ yang akan berfungsi sebagai "tata letak" kita, "halaman master" atau "komponen root". View ini akan berisi UI navigasi kita dan placeholder konten untuk halaman saat ini dan pandangan-model yang akan mengkonfigurasi contoh router dengan rute kita. Kita akan mulai dengan pandangan model sehingga Anda dapat melihat cara mengatur router:

### app.js

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'], name: 'welcome', moduleId: 'welcome', nav: true, title:'Welcome' }
    ]);

    this.router = router;
  }
}
```

Ok, ada beberapa hal menarik di sini. Pertama, kita akan menggunakan beberapa komponen navigasi bootstrap dalam satu menit, jadi kami menggunakan sistem modul ES6 mengimpor bootstrap sehingga itu aktif dan siap untuk berjalan. Berikutnya, kita ingin menggunakan router, jadi kita mulai dengan membuat dan mengekspor kelas _App_ kita dan setelah itu menerapkan callback `configureRouter`. Callback ini dengan dipanggil dengan konfigurasi objek. Dengan itu, Anda dapat mengatur judul untuk menggunakan saat membuat judul dokumen, maka Anda memetakan rute Anda. Setiap rute memiliki sifat sebagai berikut:

* `route`: Ini adalah pola yang, ketika dicocokkan, akan menyebabkan router untuk menavigasi ke rute ini. Anda dapat menggunakan rute statis seperti di atas, tetAPI Anda juga dapat menggunakan parameter seperti ini: `pelanggan/:id`. Ada juga dukungan untuk rute wildcard dan parameter string kueri. Rute ini bisa menjadi pola string tunggal atau array pola seperti di atas.
* `name`: Ini adalah nama yang akan digunakan dalam kode saat membuat URL untuk rute.
* `moduleId`: Ini adalah jalan yang menentukan komponen Anda ingin membuat untuk rute ini.
* `title`: Anda opsional dapat memberikan judul yang akan digunakan dalam menghasilkan judul dokumen.
* `nav`: Jika rute ini harus dimasukkan dalam model_ _navigation karena Anda ingin menghasilkan UI dengan itu, set ini ke true (atau order yang menunjukkan angka).

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>

  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

Mengikut konvensi sederhana kami, kelas `App` akan databound ke tampilan di atas dalam _app.html_. Sebagian besar dari markup ini berkaitan dengan pengaturan struktur navigasi utama. TAPI sebelum kita masuk ke itu... Anda melihat elemen `require` di bagian atas tampilan? Dengan cara yang sama yang dapat Anda menggunakan pernyataan `import` di ES2015, Aurelia memungkinkan Anda untuk menggunakan elemen `require` di HTML. Elemen `require` memungkinkan memuat hal seperti elemen dan perilaku kustom, dan dalam hal ini, CSS. Di sini, karena aplikasi sampel kami akan menggunakan beberapa gaya bootstrap dan ikon dari font-awesome, kita membutuhkan stylesheet mereka. Dengan itu, kita dapat menggunakan bootstrap untuk tata letak struktur navigasi kami, seperti di atas. TAPI itu bukan bagian yang menarik dari view  ini. Apa yang kita benar-benar ingin fokus pada adalah binding dan elemen kustom.

Karena Anda telah melihat dasar *binding* dan *string interpolation*, mari kita fokus pada hal-hal baru. Lihatlah elemen `ul` pada navbar-nav. `li`-nya menunjukkan bagaimana menggunakan repeater dengan ekspresi berikut `repeat.for="row of router.navigation"`. Ini akan membuat satu `li` untuk setiap item dalam array `router.navigation`. Variabel lokal _row_ dan Anda dapat melihat bahwa digunakan di seluruh `li` dan elemen anaknya.

> **Catatan:** Properti `navigation` pada router adalah array yang diisi dengan semua rute yang ditandai sebagai` nav: true` dalam konfigurasi rute Anda. Aurelia memodelkan sintaks `repeat.for` dari loop ES6 `for..of`.

Juga pada `li` Anda dapat melihat demonstrasi bagaimana menggunakan interpolasi string untuk secara dinamis menambahkan/menghapus kelas. Lebih jauh ke bawah dalam tampilan, ada `ul` kedua. Lihat binding pada anak `li` tunggal? `if.bind ="router.isNavigating"` ini kondisional menambahkan/menghapus `li` berdasarkan nilai dari ekspresi terikat. Mudah, router akan memperbarui `isNavigating` properti setiap kali itu bernavigasi.

Bagian terakhir kita ingin lihat adalah `router-view`, elemen kustom dekat bagian bawah tampilan. Elemen ini, disediakan oleh Aurelia, merupakan lokasi di mana DOM "halaman" saat ini akan diberikan, berdasarkan *state* (keadaan) konfigurasi router itu.

Mulailah server dev dengan `gulp watch`. Buka browser dan lihatlah. Sekarang Anda akan melihat sebuah navigasi utama dengan tab "Selamat datang" yang sedang terpilih. View _welcome_ harus menampilkan di area konten utama dan berfungsi seperti sebelumnya. Buka alat debug browser dan lihat DOM. Anda akan melihat bahwa isi tampilan _welcome_ ditampilkan dalam `router-view`.

> **Catatan:** Jika Anda meninggalkan `gulp watch` berjalan, Anda mungkin telah memperhatikan browser Anda auto-refresh setiap kali Anda membuat perubahan. Itu berkat `browser sync`, yang sudah kami konfigurasi untuk kenyamanan Anda sebagai bagian dari konfigurasi gulp default.

## Menambahkan Halaman Kedua

Nah, kita sudah secara teknis punya aplikasi navigasi sekarang ... tAPI itu tidak terlalu menarik karena hanya ada satu halaman. Mari menambahkan halaman kedua. Anda bisa menebak bagaimana melakukannya? Saya yakin Anda bisa ...

Mari kita menampilkan beberapa pengguna dari Github. Untuk melakukan itu, mari kita pertama mengkonfigurasi router untuk halaman hipotetis:

### app.js (updated)

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'],  name: 'welcome',  moduleId: 'welcome',  nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',    moduleId: 'users',    nav: true, title:'Github Users' }
    ]);

    this.router = router;
  }
}
```

Jika Anda menduga bahwa kita perlu membuat file _users.js_ dan _users.html_, Anda benar. Berikut kodenya:

### users.js

```javascript
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-fetch-client';
import 'fetch';

@inject(HttpClient)
export class Users{
  heading = 'Github Users';
  users = [];

  constructor(http){
    http.configure(config => {
      config
        .useStandardConfiguration()
        .withBaseUrl('https://API.github.com/');
    });

    this.http = http;
  }

  activate(){
    return this.http.fetch('users')
      .then(response => response.json())
      .then(users => this.users = users);
  }
}
```

Ada banyak hal menarik di sini. Mari kita mulai dari awal. Kami mengimpor `HttpClient` dari Aurelia Fetch Plugin, serta mengambil polyfill. Hal ini memungkinkan kita membuat permintaan HTTP dengan cara yang sangat sederhana, berdasarkan standar Fetch mendatang. Plugin ini tidak disertakan dengan konfigurasi default Aurelia, jadi Anda perlu menginstal paket. Untuk melakukan itu, jalankan perintah ini di console:

`` `shell
jspm menginstal aurelia-fetch-client
`` `

Sekarang saya harap Anda melihat kekuatan manajer paket terpadu dan loader. Anda cukup menginstal paket dengan jspm dan kemudian Anda mengimpor dalam kode Anda menggunakan identifier yang sama persis. Anda dapat menginstal hampir semua hal dari GitHub atau NPM dengan cara ini.

Sekarang, kita lihat dekorator `inject` ES2016. Apa yang dilakukan? Nah, Aurelia menciptakan komponen UI yang diperlukan untuk membuat aplikasi Anda. Hal ini dilakukan dengan menggunakan [Dependency Injection](http://en.wikipedia.org/wiki/Dependency_injection) kontainer yang mampu memberikan dependensi konstruktor seperti HttpClient. Bagaimana sistem DI tahu apa yang harus memberikan? Yang harus Anda lakukan adalah menambahkan bahwa ES2015 `dekorator inject` untuk kelas Anda yang melewati daftar jenis untuk memberikan contoh. Harus ada satu argumen untuk setiap parameter konstruktor. Dalam contoh di atas, kami membutuhkan HttpClient contoh, jadi kami menambahkan `HttpClient` ketik` inject` dekorator dan kemudian ditambahkan parameter yang sesuai dalam constructor.

> ** Catatan: ** Jika Anda tidak suka menggunakan dekorator dalam hal ini, Anda juga dapat menambahkan metode `inject` statis atau properti untuk kelas yang mengembalikan sebuah array dari jenis untuk menyuntikkan.

Jika Anda menggunakan TypeScript> = 1.5, Anda dapat menambahkan dekorator `@autoinject` untuk kelas Anda dan meninggalkan type-type dalam panggilan dekorator, tetAPI hanya menggunakannya pada tanda tangan konstruktor. Akan terlihat seperti:

```javascript
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-fetch-client';
import 'fetch';

@autoinject
export class Users{
  ...

  constructor(public http:HttpClient){}

  ...
}
```

Router Aurelia menegakkan siklus hidup pada view-model setiap kali rute berubah. Hal ini disebut sebagai "Siklus Hidup Layar Aktivasi" atau "Navigasi Siklus Hidup". Secara opsional, View-model dapat mengait ke berbagai bagian dari siklus hidup untuk mengontrol aliran masuk dan keluar dari rute. Ketika rute Anda siap untuk mengaktifkan router akan memanggil `activate`, jika ada. Dalam kode di atas, kita menggunakan buku ini untuk memanggil API GitHub dan mendapatkan beberapa pengguna kembali. Perhatikan bahwa kita kembali hasil dari permintaan http kembali dari `metode `activate` kita. Semua `HttpClient` API mengembalikan `Promise`. Router akan mendeteksi `Promise` dan menunggu untuk menyelesaikan navigasi sampai setelah menyelesaikan. Jadi, dengan cara ini, Anda dapat opsional memaksa router untuk menunda menampilkan halaman sampai diisi dengan data.

> **Catatan:** The siklus hidup navigasi lengkap termasuk `canActivate`,`activate`, `canDeactivate`, dan `deactivate`. Metode can* dapat mengembalikan boolean (atau promise boolean) untuk menerima atau menolak transisi ke dalam atau keluar dari layar saat ini.

> **Catatan:** Jika Anda tidak akrab dengan [Promise] (http://www.html5rocks.com/en/tutorials/es6/promises/), ini adalah fitur baru dari ES2015 yang dirancang untuk meningkatkan pemrograman asinkron. Sebuah `Promise` adalah sebuah objek yang merupakan hasil masa depan. Pada dasarnya, itu merupakan "promise" untuk menyelesaikan beberapa pekerjaan atau untuk menyediakan beberapa data di masa depan.

### users.html

```markup
<template>
  <section>
      <h2>${heading}</h2>
      <div class="row au-stagger">
        <div class="col-sm-6 col-md-3 card-container" repeat.for="user of users">
            <div class="card">
                <canvas class="header-bg" width="250" height="70"></canvas>
                <div class="avatar">
                    <img src.bind="user.avatar_url" crossorigin />
                </div>
                <div class="content">
                    <p class="name">${user.login}</p>
                    <p><a target="_blank" class="btn btn-default" href.bind="user.html_url">Contact</a></p>
                </div>
            </div>
        </div>
      </div>
  </section>
</template>
```

Tampilan untuk layar ini cukup jelas. Tidak ada yang Anda belum lihat sebelumnya.

Jalankan aplikasi Anda lagi. Sekarang Anda akan melihat dua item di bar nav dan dapat beralih bolak-balik antara mereka. Hore!

Mari kita rekap. Untuk menambahkan halaman untuk aplikasi Anda:

1. Tambahkan konfigurasi rute ke router _app.js_.
2. Tambahkan pandangan model.
3. Tambahkan pandangan dengan nama yang sama (tapi dengan ekstensi .html).
4. Rayakan.

> **Catatan:** Pada titik ini, mungkin layak bagi Anda untuk melihat kerangka kode asli untuk layar ini. Anda akan menemukan beberapa barang lain ada seperti animasi, elemen yang mengikat, nilai konverter dan atribut kustom. Anda juga akan melihat cara alternatif untuk memulai kerangka itu sendiri dan menginstal plugin pihak ke-3.

## Bonus: Membuat Elemen Kustom

 Anda overachiever! Saya melihat Anda tertarik untuk belajar beberapa tambahan mengagumkan pada hari ini baik-baik saja. Dalam hal ini, mari kita membuat elemen HTML kustom. Saya pikir calon yang baik untuk ini adalah navbar kita. Banyak HTML dalam file _app.html_ kami. Mengapa tidak mengambil kebiasaan `<nav-bar>` elemen untuk membuat hal-hal sedikit lebih deklaratif? Inilah yang ingin kita tulis:

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>
  <require from='nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

Kode ini membutuhkan `elemen nav-bar` dari" nav-bar "dan setelah itu tersedia dalam tampilan, kita dapat menggunakannya seperti elemen lainnya, termasuk penyatuan data sifat kustom (seperti _router_). Jadi, bagaimana kita bisa produk akhir ini?

Tebak apa? Kami sederhana lihat model / view konvensi masih berlaku untuk elemen kustom. Mari kita membuat _nav-bar.js_ dan _nav-bar.html_. Berikut kode untuk tampilan-model pertama:

### nav-bar.js

```javascript
import {bindable} from 'aurelia-framework';

export class NavBar {
  @bindable router = null;
}
```

Untuk membuat elemen kustom, Anda membuat dan mengekspor kelas. Sejak kelas ini akan digunakan dalam HTML sebagai elemen, kita perlu memberitahu framework properti apa di kelas yang harus muncul sebagai atribut pada elemen. Untuk melakukan itu, kita menggunakan dekorator _bindable_. Seperti _inject_, _bindable_ adalah cara untuk memberikan informasi tentang kelas Anda untuk kerangka Aurelia. Aurelia cerdas dan dapat menyimpulkan banyak hal, tetapi ketika tidak dapat atau ketika Anda ingin melakukan sesuatu yang berbeda dari konvensi, Anda menyediakan beberapa informasi tambahan melalui dekorator. Dekorator `bindable` memberitahu framework bahwa kita ingin properti `router` kelas kita akan muncul sebagai atribut dalam HTML. Setelah itu muncul sebagai atribut, kita dapat ikat/bind ke view.

### nav-bar.html

```markup
<template>
  <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle Navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">
        <i class="fa fa-home"></i>
        <span>${router.title}</span>
      </a>
    </div>

    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
          <a href.bind="row.href">${row.title}</a>
        </li>
      </ul>

      <ul class="nav navbar-nav navbar-right">
        <li class="loader" if.bind="router.isNavigating">
          <i class="fa fa-spinner fa-spin fa-2x"></i>
        </li>
      </ul>
    </div>
  </nav>
</template>
```

Ini terlihat hampir identik dengan HTML navbar dalam file _app.html_ kita. Kita pada dasarnya telah mengekstrak itu dan memasukkannya ke dalam template ini. Alih-alih mengikat (binding) _app.js_, sekarang mengikat _nav-bar.js_.

Ini adalah elemen kustom yang sangat sederhana tanpa perilaku nyata, tetapi lengkap dan dapat digunakan seperti yang ditunjukkan di atas.

Tunggu! Aku tahu apa yang Anda pikirkan. Elemen kustom ini sangat sederhana ... tampaknya sedikit konyol untuk meminta kelas JavaScript hanya untuk menentukan properti `router`. Tidak bisa kita menyingkirkan yang entah bagaimana? Nah, jawabannya adalah YA. Untuk elemen-elemen yang sangat sederhana yang tidak memiliki perilaku tetapi hanya memberikan pandangan yang dapat terikat satu set properti, kita dapat menghilangkan JavaScript mengajukan sama sekali. Mari kita lihat bagaimana yang bekerja.

Pertama, menghapus file _nav-bar.js_. Berikutnya, kita perlu membuat satu perubahan ke file _nav-bar.html_. Pada elemen Template, kita dapat mendeklarasikan sifat bindable elemen kami seperti ini:

```markup
<template bindable="router">
  ...
</template>
```

Kita dapat memiliki lebih dari satu properti dengan memisahkan mereka dengan koma. Akhirnya, kita perlu memperbarui file _app.html_ kita sehingga elemen `require` menunjuk ke komponen html kita. Inilah yang seharusnya:

### app.html

```markup
<template>
  <require from="bootstrap/css/bootstrap.css"></require>
  <require from="font-awesome/css/font-awesome.css"></require>
  <require from='nav-bar.html'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>
```

> **Catatan:** Apa pun yang diperlukan dalam pandangan dengan elemen `require` lokal untuk pandangan. Akibatnya, Anda tidak perlu khawatir tentang konflik nama. Anda juga dapat memuat elemen-elemen se-aplikasi dan perilaku lainnya untuk kenyamanan sehingga Anda tidak perlu memerlukan sumber daya umum di setiap tampilan. Lihat dokumentasi untuk informasi lebih lanjut tentang itu.

Anda mungkin bertanya-tanya bagaimana Aurelia menentukan nama dari elemen kustom. Dengan konvensi, ia akan menggunakan nama ekspor kelas, menurunkan dan ditulis dgn tanda penghubung. (Dalam skenario html-satunya kita, itu akan menggunakan nama file.) Namun, Anda dapat selalu eksplisit. Untuk melakukannya, tambahkan `customElement ('nav-bar')` dekorator untuk kelas Anda. Bagaimana jika elemen kustom Anda tidak memiliki tampilan template karena itu semua dilaksanakan dalam kode? Tidak masalah, menambahkan `noView()` dekorator. Ingin menggunakan ShadowDOM untuk elemen kustom Anda? Melakukannya seperti pro dengan menggunakan `useShadowDOM ()` dekorator. Jangan khawatir tentang apakah atau tidak browser mendukungnya. Kami memiliki efisien, penuh kesetiaan pelaksanaan ShadowDOM mundur. Anda dapat membaca lebih lanjut tentang dekorator ini dalam elemen kustom bagian dari dokumentasi.

Selain menciptakan elemen kustom, Anda juga dapat membuat atribut khusus yang menambahkan perilaku baru untuk elemen yang ada. Pada kesempatan Anda bahkan mungkin perlu atribut untuk secara dinamis mengontrol template dengan menambahkan dan menghapus DOM dari pandangan, seperti `repeat` dan` if` kita gunakan di atas. Anda dapat melakukan semua itu dan banyak lagi dengan mesin template kuat dan extensible Aurelia. Ini rahasianya... tidak ada behaviour Aurelia built-in yang benar-benar built-in. Mereka berada di pustaka mereka sendiri dan "dipasang" ke Aurelia sebagai plugin. Kami menyediakan built-in kami menggunakan inti yang sama yang Anda miliki untuk membangun aplikasi dan plugin Anda sendiri.

## Bonus: Memanfaatkan Router Anak

Masih kurang cukup? Saya punya hadiah untuk Anda. Mari menambahkan halaman ketiga untuk aplikasi kita... dengan router itu sendiri! Kami menyebutnya router anak. Router anak memiliki konfigurasi rute mereka sendiri dan menavigasi relatif terhadap router induk. Persiapkan dirimu untuk kegilaan ini!

Pertama, mari kita memperbarui _app.js_ kami dengan konfigurasi baru. Inilah yang seharusnya terlihat seperti:

### app.js (diperbaharui... lagi)

```javascript
import 'bootstrap';

export class App {
  configureRouter(config, router){
    config.title = 'Aurelia';
    config.map([
      { route: ['','welcome'],  name: 'welcome',      moduleId: 'welcome',      nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',        moduleId: 'users',        nav: true, title:'Github Users' },
      { route: 'child-router',  name: 'child-router', moduleId: 'child-router', nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Tak ada yang baru di sini. Bagian menariknya ada di _child-router.js_ ini

### child-router.js

```javascript
export class ChildRouter{
  heading = 'Child Router';

  configureRouter(config, router){
    config.map([
      { route: ['','welcome'],  name: 'welcome',       moduleId: 'welcome',       nav: true, title:'Welcome' },
      { route: 'users',         name: 'users',         moduleId: 'users',         nav: true, title:'Github Users' },
      { route: 'child-router',  name: 'child-router',  moduleId: 'child-router',  nav: true, title:'Child Router' }
    ]);

    this.router = router;
  }
}
```

Apa!? Isinya sama seperti `App`? Apa? Mengapa? Nah... Anda tidak perlu pernah melakukan hal ini dalam kehidupan nyata ... tapi ini cukup keren. Ini adalah router rekursif, dan kami melakukannya hanya karena kami bisa.

Selengkapnya, inilah view-nya:

### child-router.html

```javascript
<template>
  <section>
    <h2>${heading}</h2>
    <div>
      <div class="col-md-2">
        <ul class="well nav nav-pills nav-stacked">
          <li repeat.for="row of router.navigation" class="${row.isActive ? 'active' : ''}">
            <a href.bind="row.href">${row.title}</a>
          </li>
        </ul>
      </div>
      <div class="col-md-10" style="padding: 0">
        <router-view></router-view>
      </div>
    </div>
  </section>
</template>
```

Jalankan aplikasi dan lihatlah keajaiban... Jangan lupa berdoa agar alam semesta tidak meledak.

## Kesimpulan

Dengan fokus yang kuat pada pengalaman pengembang, Aurelia dapat memungkinkan Anda untuk tidak hanya membuat aplikasi menakjubkan, tetapi juga menikmati prosesnya. Kami telah merancang dengan konvensi sederhana dalam pikiran sehingga Anda tidak perlu membuang waktu dengan ton konfigurasi atau menulis kode boilerplate hanya untuk memenuhi kerangka keras kepala atau membatasi. Anda tidak akan pernah menemukan hambatan dengan Aurelia. Aurelia telah dirancang dengan hati-hati untuk menjadi *pluggable* dan mudah disesuaikan.

Terima kasih telah meluangkan waktu untuk membaca panduan kami. Kami berharap Anda akan menjelajahi dokumentasi dan membangun sesuatu yang mengagumkan. Kami berharap dapat melihat apa yang akan Anda buat.