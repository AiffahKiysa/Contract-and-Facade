# Facade

[Kembali](README.md)

## Penjelasan

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

### Langkah keempat

Isi file ```app/Providers/StudentProvider.php``` yang telah dibuat dengan source code berikut:

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

![1653138686853](https://user-images.githubusercontent.com/74708771/169653299-9b604fa1-6f43-4543-b2b4-7f20bfaedd77.jpg)

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
