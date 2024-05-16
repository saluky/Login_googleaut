# Login_googleauth
Login dengan google auth
Untuk membuat form login dengan Google Authentication menggunakan PHP, Anda dapat mengikuti langkah-langkah berikut. Dalam contoh ini, kita akan menggunakan library OAuth2 dari Google.

1. **Siapkan Proyek Google API**:
   - Kunjungi [Google Cloud Console](https://console.cloud.google.com/).
   - Buat proyek baru atau gunakan proyek yang sudah ada.
   - Aktifkan Google+ API (atau OAuth 2.0 jika tidak ada Google+ API) di dalam proyek.
   - Buat kredensial OAuth 2.0 untuk aplikasi web.
   - Catat `Client ID` dan `Client Secret`.

2. **Instalasi Library Google API Client di PHP**:
   - Instal library menggunakan Composer. Jika belum ada, instal Composer terlebih dahulu dari [getcomposer.org](https://getcomposer.org/).
   - Jalankan perintah berikut di terminal untuk menginstal library Google API Client:
     ```bash
     composer require google/apiclient:^2.0
     ```

3. **Buat File Konfigurasi dan Skrip PHP**:
   - Buat file `config.php` untuk menyimpan konfigurasi OAuth.

   ```php
   <?php
   require_once 'vendor/autoload.php';

   session_start();

   $client = new Google_Client();
   $client->setClientId('YOUR_CLIENT_ID');
   $client->setClientSecret('YOUR_CLIENT_SECRET');
   $client->setRedirectUri('http://your-domain.com/callback.php');
   $client->addScope('email');
   $client->addScope('profile');
   ?>
   ```

   - Buat file `index.php` untuk form login:

   ```php
   <?php
   require_once 'config.php';

   $loginUrl = $client->createAuthUrl();
   ?>
   <!DOCTYPE html>
   <html>
   <head>
       <title>Login with Google</title>
   </head>
   <body>
       <a href="<?php echo $loginUrl; ?>">Login with Google</a>
   </body>
   </html>
   ```

   - Buat file `callback.php` untuk menangani callback dari Google:

   ```php
   <?php
   require_once 'config.php';

   if (isset($_GET['code'])) {
       $token = $client->fetchAccessTokenWithAuthCode($_GET['code']);
       $client->setAccessToken($token);

       // Mendapatkan informasi pengguna
       $oauth2 = new Google_Service_Oauth2($client);
       $userInfo = $oauth2->userinfo->get();

       // Anda bisa menyimpan informasi pengguna ke dalam sesi atau database
       $_SESSION['user'] = $userInfo;

       // Redirect ke halaman profil atau halaman utama
       header('Location: profile.php');
       exit();
   } else {
       header('Location: index.php');
       exit();
   }
   ```

   - Buat file `profile.php` untuk menampilkan informasi pengguna setelah login:

   ```php
   <?php
   session_start();

   if (!isset($_SESSION['user'])) {
       header('Location: index.php');
       exit();
   }

   $user = $_SESSION['user'];
   ?>
   <!DOCTYPE html>
   <html>
   <head>
       <title>User Profile</title>
   </head>
   <body>
       <h1>Welcome, <?php echo htmlspecialchars($user->name); ?></h1>
       <p>Email: <?php echo htmlspecialchars($user->email); ?></p>
       <p>Profile Image: <img src="<?php echo htmlspecialchars($user->picture); ?>" alt="Profile Image"></p>
       <a href="logout.php">Logout</a>
   </body>
   </html>
   ```

   - Buat file `logout.php` untuk logout pengguna:

   ```php
   <?php
   session_start();
   session_destroy();

   header('Location: index.php');
   exit();
   ```

4. **Uji Aplikasi Anda**:
   - Jalankan server lokal Anda (misalnya, menggunakan `php -S localhost:8000` jika Anda menggunakan server built-in PHP).
   - Akses `http://localhost:8000/index.php` di browser Anda.
   - Klik "Login with Google" dan ikuti proses autentikasi.
   - Setelah login, Anda akan diarahkan ke halaman profil dengan informasi pengguna yang diambil dari Google.

Dengan langkah-langkah ini, Anda akan memiliki sistem login dengan Google Authentication yang sederhana menggunakan PHP. Pastikan untuk mengganti placeholder seperti `YOUR_CLIENT_ID` dan `YOUR_CLIENT_SECRET` dengan kredensial yang Anda dapatkan dari Google Cloud Console.
