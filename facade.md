# Facade

[Kembali](README.md)

## Penjelasan

![1445024592facades-1024x549](https://user-images.githubusercontent.com/74708771/169654739-5d64f4a9-8a21-4915-b79d-dc14b7b000ee.jpg)

Facade adalah sebuah fitur dari Laravel yang mengimplementasikan Facade Pattern. Facade Pattern adalah salah satu *software design pattern* pada *object oriented programming* (OOP) yang membungkus suatu library yang kompleks agar menjadi sebuah interface yang lebih **simpel dan mudah untuk dibaca**. Pada facade pattern, terdapat sebuah class yang disebut facade class yang akan mengakses class lain. Class pengguna (client) tidak akan mengakses class yang di-'facade' secara langsung, melainkan mengaksesnya melalui facade class. Tujuan dari facade pattern ini adalah untuk menyembunyikan kerumitan.

Facade pada Laravel menyediakan sebuah static interface ke kelas-kelas yang tersedia pada service container dari aplikasi yang dibuat. Facade pada Laravel dapat diasumsikan sebagai cara singkat dan ringkas untuk mengakses sebuah object di container, sekaligus mempermudah keperluan testing (*testability* dan *flexibility*). Ketika menggunakan facade harus berhati-hati agar tidak terjadi ***scope creep***. Karena facade mudah untuk digunakan sehingga memungkinkan untuk class yang dibuat menjadi semakin besar dan menggunakan banyak facade. Sebaiknya ukuran dari class tetap diperhatikan agar scope classs tidak terlalu besar. Jika memang ukuran class sudah terlalu besar, class sebaiknya dipecah menjadi beberapa class yang lebih kecil.

## Konsep

## Langkah-langkah tutorial

### Langkah pertama

Buat direktori dan file baru di dalam direktori ```app``` seperti berikut:

```
app/
--Classes/
----Facade/
------StudentFacade.php
----Student.php
```

![1653135842021](https://user-images.githubusercontent.com/74708771/169653260-a4ecb693-38a0-4510-bc59-085bd5e3299a.jpg)

### Langkah kedua

Isi file ```app/Classes/Student.php``` dengan source code berikut:

```php
<?php
namespace App\Classes;

class Student {
	public function studentName() {
		return 'Nama Siswa 1';
	}
}
```

### Langkah ketiga

Isi file ```app/Classes/Facade/StudentFacade.php``` dengan source code berikut:

```php
<?php
namespace App\Classes\Facade;
use Illuminate\Support\Facades\Facade;

class StudentFacade extends Facade {
	protected static function getFacadeAccessor() {
		return 'student';
	}
}
```

### Langkah keempat

Buat provider dengan menjalankan perintah berikut di terminal:

```
php artisan make:provider StudentProvider
```

Kemudian, isi file ```app/Providers/StudentProvider.php``` yang telah dibuat dengan source code berikut:

```php
<?php
namespace App\Providers;
use Illuminate\Support\ServiceProvider;

class StudentProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        $this->app->bind('student', function() {
            return new \App\Classes\Student;
        });
    }
}
```

### Langkah kelima

Setelah membuat provider, maka perlu dilakukan penambahan provider yang dibuat ke dalam file ```config/app.php``` dengan cara menambahkan 2 baris berikut pada **providers class** dan **aliases class**:

```php
    'providers' => [
   
        //...
        App\Providers\StudentProvider::class,

    ],
    
    ...

    'aliases' => [

        //...
        'StudentFacade' => App\Classes\Facade\StudentFacade::class,

    ],
```

### Langkah keenam

Generate ulang file ```autoload.php``` agar kelas-kelas yang baru dibuat tadi dapat langsung dikenali oleh aplikasi (tanpa harus include/require secara manual) dengan menjalankan perintah berikut pada terminal:

```
composer dump-autoload
```

### Langkah ketujuh

Facade telah selesai dibuat dan dapat digunakan dimana saja di project laravel. Sebagai contoh untuk menampilkan facade yang telah dibuat di view welcome,  file ```resources/views/welcome.blade.php``` dapat diisi menjadi seperti berikut:

```html
<h1>{{StudentFacade::studentName()}}</h1>
```
Untuk hasil view nya adalah seperti berikut:

![1653139982065](https://user-images.githubusercontent.com/74708771/169654904-fa1485ba-7ec7-4c26-b126-94a3fc2f174b.jpg)

### Langkah kedelapan

Cara lain untuk mencobanya adalah menggunakan tinker dengan menjalankan perintah berikut di terminal:

```
php artisan tinker
```

Kemudian memanggil fungsi ```stundentName()``` yang telah dibuat pada class ```StudentFacade``` dengan perintah:

```
StudentFacade::studentName()
```

![1653139380015](https://user-images.githubusercontent.com/74708771/169653584-1dbd31db-c445-40e0-8952-e4ac1d265ca0.jpg)

## Referensi
* https://laravel.com/docs/9.x/facades
* https://www.sitepoint.com/how-laravel-facades-work-and-how-to-use-them-elsewhere/
* https://www.freecodecamp.org/news/how-to-use-facades-in-laravel/
