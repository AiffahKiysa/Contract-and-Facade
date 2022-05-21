# Facade

[Kembali](README.md)

## Penjelasan

![1445024592facades-1024x549](https://user-images.githubusercontent.com/74708771/169654739-5d64f4a9-8a21-4915-b79d-dc14b7b000ee.jpg)

Facade adalah sebuah fitur dari Laravel yang mengimplementasikan Facade Pattern. Facade Pattern adalah salah satu *software design pattern* pada *object oriented programming* (OOP) yang membungkus suatu library yang kompleks agar menjadi sebuah interface yang lebih **simpel dan mudah untuk dibaca**. Pada facade pattern, terdapat sebuah class yang disebut facade class yang akan mengakses class lain. Class pengguna (client) tidak akan mengakses class yang di-'facade' secara langsung, melainkan mengaksesnya melalui facade class. Tujuan dari facade pattern ini adalah untuk menyembunyikan kerumitan.

Facade pada Laravel menyediakan sebuah static interface ke kelas-kelas yang tersedia pada service container dari aplikasi yang dibuat. Facade pada Laravel dapat diasumsikan sebagai cara singkat dan ringkas untuk mengakses sebuah object di container, sekaligus mempermudah keperluan testing (*testability* dan *flexibility*). Ketika menggunakan facade harus berhati-hati agar tidak terjadi ***scope creep***. Karena facade mudah untuk digunakan sehingga memungkinkan untuk class yang dibuat menjadi semakin besar dan menggunakan banyak facade. Sebaiknya ukuran dari class tetap diperhatikan agar scope classs tidak terlalu besar. Jika memang ukuran class sudah terlalu besar, class sebaiknya dipecah menjadi beberapa class yang lebih kecil.

## Konsep

Penggunaan facade pada Laravel cukup sering digunakan. Sebagai contoh, untuk routing pada Laravel biasanya menggunakan code berikut:

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
	return  view('welcome');
});
```

Route merupakan salah satu bentuk dari facade di Laravel. Selain route, terdapat banyak facade lain di Laravel. Semua Facade yang ada di Laravel secara default terdapat di direktori ``` vendor/laravel/framework/src/Illuminate/Support/Facades ```:

![1653137136458](https://user-images.githubusercontent.com/74708771/169655876-f81a7c03-29d1-4b84-9285-c0189f79b802.jpg)

Semua facade di Laravel terdefinisikan dengan namespace ```Illuminate\Support\Facades```. Untuk menggunakannya cukup mudah seperti contoh berikut:

```php
<?php

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Route;

Route::get('/cache', function () {
	return Cache::get('key');
});
```

Isi code dari facade pada laravel kurang lebihnya sama. Sebagai contoh code pada file ```DB.php``` adalah seperti berikut:

```php
<?php

namespace Illuminate\Support\Facades;

class DB extends Facade
{
    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return 'db';
    }
}
```

Terlihat bahwa ```DB``` adalah class yang meng-extend base class ```Facade```. Isinya adalah satu method protected yaitu ```getFacadeAccessor()```,yang mengembalikan string ```db```. ```db``` yang merupakan nama dari facade ini dikembalikan agar facade dapat diakses di mana pun dalam project laravel.

Facade pada Laravel juga menyediakan testing methods. Sebagai contoh untuk menguji apakah method ```Cache::get``` sudah dipanggil dengan argumen yang diharapkan:

```php
<?php

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Route;

Route::get('/cache', function () {
    return Cache::get('key');
});
```

Untuk mengujinya dapat menggunakan test script seperti berikut:

```php
<?php

use Illuminate\Support\Facades\Cache;

/**
 * A basic functional test example.
 *
 * @return void
 */
public function testBasicExample()
{
    Cache::shouldReceive('get')
         ->with('key')
         ->andReturn('value');

    $response = $this->get('/cache');

    $response->assertSee('value');
}
```

Facade dipanggil dengan menggunakan double colons (::) karena merupakan static methods. Pada static methods, tidak diperlukan inisiasi sebuah class untuk mereferensi class tersebut, sedangkan pada non-static methods, harus melakukan inisiasi class agar dapat di-refer.

Berikut adalah contoh dari static methods dan non-static methods pada PHP:
```php
<?php

//static methods
class backend {
	private const language = "php";
	public static function language() {
    	echo self::language;
  	}
}
backend::language();  //php

//non-static methods
class backend{
	public function language($name){
		echo $name;
	}
}
$test = new backend; //inisiasi instance dari class
$test->language('php'); //php
```

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
