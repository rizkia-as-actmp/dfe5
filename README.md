
# # Laporan Pembuatan Aplikasi Pengelolaan dan Perpindahan Data Kartu Keluarga

### Anggota Kelompok 
- 5220411010 M Rifqi Al Amin SP
- 5220411012 Arrum Juia Budiarti
- 5220411036 Oktabella Tri Saputra
- 5220411051 Rizkia Adhy Syahputra

## Pendahuluan
Aplikasi ini dibuat untuk memenuhi tugas kuliah yang bertujuan untuk mengelola data Kartu Keluarga secara digital, guna menggantikan proses manual yang sering memakan waktu dan rawan kesalahan. Dengan aplikasi ini, pengguna dapat mengelola, mengedit, dan melihat data Kartu Keluarga serta data anggota keluarga secara mudah dan efisien.

Aplikasi ini memiliki beberapa fitur utama yang memberikan kemudahan bagi pengguna dalam mengelola data keluarga, termasuk fitur login untuk otentikasi pengguna, pengelolaan Kartu Keluarga, serta pengelolaan anggota keluarga. Semua data disimpan dan dikelola melalui API backend, yang memastikan keamanan dan integritas data.

1. **Login**: Otentikasi pengguna menggunakan email dan password.
2. **Pengelolaan Kartu Keluarga**: 
   - Menampilkan daftar Kartu Keluarga.
   - Menambahkan data Kartu Keluarga.
   - Mengedit data Kartu Keluarga.
   - Menampilkan detail Kartu Keluarga beserta daftar anggota keluarga.
3. **Pengelolaan Anggota Keluarga**: Menampilkan anggota keluarga berdasarkan data yang terkait dengan Kartu Keluarga.

## Teknologi yang Digunakan


### 1. **Frontend**

-   **HTML**: Digunakan untuk membangun struktur halaman aplikasi, memastikan tampilan halaman web sesuai dengan yang diinginkan.
-   **CSS**: Menangani styling dan visualisasi tampilan agar aplikasi mudah digunakan dengan tampilan yang bersih dan terorganisir.
-   **JavaScript**: Menyediakan interaktivitas dan logika aplikasi, seperti mengirim data ke backend melalui API, menangani perubahan halaman secara dinamis, dan merender data.


### 2. **Backend**
#### Pocketbase
-   **REST API**: Backend menggunakan arsitektur REST menggunakan pocketbase untuk menghubungkan frontend dengan basis data. API ini menangani berbagai operasi CRUD (Create, Read, Update, Delete) yang diperlukan untuk pengelolaan data Kartu Keluarga.
    -   Login: `/api/collections/pemerintah/auth-with-password`
    -   Mengambil daftar Kartu Keluarga: `/api/collections/kartu_keluarga/records`
    -   Menambahkan data Kartu Keluarga: `/api/collections/kartu_keluarga/records`
    -   Mengedit data Kartu Keluarga: `/api/collections/kartu_keluarga/records/:id`
    -   Mengambil detail Kartu Keluarga: `/api/collections/kartu_keluarga/records/:id`

## Fitur Utama

### 1. **Login**

![enter image description here](https://raw.githubusercontent.com/rizkia-as-actmp/dfe5/refs/heads/main/Screenshot%20from%202025-01-02%2016-19-48.png)

Pengguna dapat melakukan login ke aplikasi dengan memasukkan email dan password yang sudah terdaftar. Setelah berhasil login, token JWT disimpan di Local Storage, dan pengguna diarahkan ke halaman utama aplikasi untuk melihat daftar Kartu Keluarga.

Kode login menggunakan API:

javascript

Copy code
```javascript
const loginForm = document.getElementById('login-form');
loginForm.addEventListener('submit', async (event) => {
  event.preventDefault();
  const email = document.getElementById('email').value;
  const password = document.getElementById('password').value;
  const response = await fetch('http://127.0.0.1:8090/api/collections/pemerintah/auth-with-password', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ identity: email, password: password })
  });
  if (response.ok) {
    const data = await response.json();
    localStorage.setItem('token', data.token);
    window.location.href = 'list-kartu-keluarga.html';
  } else {
    alert('Login gagal!');
  }
});

```

**Penjelasan:**

-   Token JWT disimpan di Local Storage untuk otorisasi permintaan API selanjutnya.
-   Jika login berhasil, pengguna diarahkan ke halaman daftar Kartu Keluarga.

### 2. **Daftar Kartu Keluarga**

![enter image description here](https://raw.githubusercontent.com/rizkia-as-actmp/dfe5/refs/heads/main/Screenshot%20from%202025-01-02%2016-22-38.png)

Halaman ini menampilkan daftar semua Kartu Keluarga yang terdaftar dalam sistem. Setiap Kartu Keluarga menampilkan informasi seperti nama Kepala Keluarga dan alamat, dengan opsi untuk melihat detail atau mengedit data.

Kode untuk menampilkan daftar Kartu Keluarga:
```javascript
const listContainer = document.getElementById('kk-list');
fetch('http://127.0.0.1:8090/api/collections/kartu_keluarga/records', {
  headers: { 'Authorization': `Bearer ${localStorage.getItem('token')}` }
})
  .then(response => response.json())
  .then(data => {
    data.items.forEach(kk => {
      const item = document.createElement('div');
      item.innerHTML = `
        <h3>${kk.kepala_keluarga}</h3>
        <p>${kk.alamat}</p>
        <button onclick="viewDetails('${kk.id}')">Detail</button>
        <button onclick="editKK('${kk.id}')">Edit</button>
      `;
      listContainer.appendChild(item);
    });
  });

```

**Penjelasan:**

-   Menggunakan token JWT untuk otorisasi, daftar Kartu Keluarga ditampilkan di halaman.
-   Setiap item memiliki tombol untuk melihat detail atau mengedit data.

### 3. **Tambah Kartu Keluarga**
![enter image description here](https://raw.githubusercontent.com/rizkia-as-actmp/dfe5/refs/heads/main/Screenshot%20from%202025-01-02%2016-23-05.png)
Pengguna dapat menambahkan data Kartu Keluarga baru dengan mengisi nama Kepala Keluarga dan alamat. Data kemudian dikirimkan ke server menggunakan API untuk disimpan.

Kode untuk menambahkan data Kartu Keluarga:
```javascript
const form = document.getElementById('create-kk-form');
form.addEventListener('submit', async (event) => {
  event.preventDefault();
  const kepalaKeluarga = document.getElementById('kepala_keluarga').value;
  const alamat = document.getElementById('alamat').value;

  const response = await fetch('http://127.0.0.1:8090/api/collections/kartu_keluarga/records', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${localStorage.getItem('token')}`
    },
    body: JSON.stringify({ kepala_keluarga: kepalaKeluarga, alamat: alamat })
  });
  if (response.ok) {
    window.location.href = 'list-kartu-keluarga.html';
  } else {
    alert('Gagal menambahkan data!');
  }
});
```

**Penjelasan:**

-   Data dari form dikirimkan ke API untuk menambahkan Kartu Keluarga baru ke sistem.
-   Setelah berhasil, pengguna diarahkan kembali ke halaman daftar Kartu Keluarga.

### 4. **Edit Warga**

![enter image description here](https://raw.githubusercontent.com/rizkia-as-actmp/dfe5/refs/heads/main/Screenshot%20from%202025-01-02%2016-24-09.png)
Kode untuk mengedit warga:
```javascript
document.addEventListener('DOMContentLoaded',  async  ()  =>  {
const  form  =  document.getElementById('edit-form');
const  backButton  =  document.getElementById('back-to-list');
const  urlParams  =  new  URLSearchParams(window.location.search);
const  wargaId  =  urlParams.get('id');
const  token  =  localStorage.getItem('jwt_token')
  
if (!token) {
alert('Token tidak ditemukan. Silakan login kembali.');
window.location.href  =  'index.html';
return;
}
  
if (!wargaId) {
alert('ID warga tidak ditemukan.');
return;
}
try  {
// Fetch data warga berdasarkan ID
const  response  =  await  fetch(`http://127.0.0.1:8090/api/collections/warga/records/${wargaId}`,  {
method:  'GET',
headers:  {
'Content-Type':  'application/json',
'Authorization':  `Bearer ${token}`,
},
});
 
if (!response.ok) {
throw  new  Error('Gagal memuat data warga.');
const  warga  =  await  response.json();
// Isi form dengan data warga
document.getElementById('nik').value =  warga.NIK;
document.getElementById('no_kk').value =  warga.no_kk;  // Menambahkan No. 
document.getElementById('nama').value =  warga.nama;
document.getElementById('agama').value =  warga.agama;
document.getElementById('jenis_kelamin').value =  warga.jenis__kelamin
document.getElementById('alamat').value =  warga.alamat;
// Menangani pengiriman form
form.addEventListener('submit',  async  (event)  =>  {
event.preventDefault();
 
const  updatedData  =  {
nama:  form.nama.value,
agama:  form.agama.value,
jenis__kelamin:  form.jenis_kelamin.value,
alamat:  form.alamat.value,
no_kk:  form.no_kk.value // Tetap mempertahankan No. KK yang lama
};
try  {
// Kirim update data warga
const  updateResponse  =  await  fetch(`http://127.0.0.1:8090/api/collections/warga/records/${wargaId}`,  {
method:  'PATCH',
headers:  {
'Content-Type':  'application/json'
},
body:  JSON.stringify(updatedData),
});
if (!updateResponse.ok) {
trow  new  Error('Gagal mengupdate data warga.');
}
alert('Data warga berhasil diperbarui!');

window.location.href  =  'list-warga.html';

}  catch (error) {

alert(error.message);

}
});

// Navigasi kembali ke daftar warga
backButton.addEventListener('click',  ()  =>  {
window.location.href  =  'list-warga.html';
});
}  catch (error) {
alert(error.message);
}
});

```

**Penjelasan:**

-   Data yang ada sebelumnya diambil dan diisi dalam form.
-   Setelah pengguna mengubah data dan menyimpannya, data baru dikirimkan menggunakan metode PATCH.

### 5. **Detail Kartu Keluarga**

![enter image description here](https://raw.githubusercontent.com/rizkia-as-actmp/dfe5/refs/heads/main/Screenshot%20from%202025-01-02%2016-23-36.png)

Halaman ini menampilkan detail lengkap dari Kartu Keluarga yang dipilih, termasuk daftar anggota keluarga yang terdaftar dalam Kartu Keluarga tersebut.

Kode untuk menampilkan detail Kartu Keluarga:
```javascript
const id = new URLSearchParams(window.location.search).get('id');
fetch(`http://127.0.0.1:8090/api/collections/kartu_keluarga/records/${id}`, {
  headers: { 'Authorization': `Bearer ${localStorage.getItem('token')}` }
})
  .then(response => response.json())
  .then(data => {
    document.getElementById('kepala_keluarga').innerText = data.kepala_keluarga;
    document.getElementById('alamat').innerText = data.alamat;

    const anggotaContainer = document.getElementById('anggota-list');
    data.anggota.forEach(anggota => {
      const item = document.createElement('div');
      item.innerHTML = `
        <h4>${anggota.nama}</h4>
        <p>${anggota.alamat}</p>
      `;
      anggotaContainer.appendChild(item);
    });
  });

```

**Penjelasan:**

-   Anggota keluarga ditampilkan secara dinamis dengan menggunakan data yang diterima dari API.

## Struktur Folder

Struktur folder aplikasi ini adalah sebagai berikut:

```shell
.
|____add-warga.html
|____list-warga.html
|____pocketbase
|____scripts
| |____create-kartu-keluarga.js
| |____login.js
| |____detail-kk.js
| |____edit-kartu-keluarga.js
| |____list-kk.js
| |____list.js
| |____add.js
| |____edit.js
|____edit-warga.html
|____detail-kartu-keluarga.html
|____styles
| |____list-kk.css
| |____create-kartu-keluarga.css
| |____login.css
| |____add.css
| |____edit-kk.css
| |____detail-kk.css
| |____list.css
|____pb_hooks
| |____on_add.pb.js
|____pb_migrations
|____edit-kartu-keluarga.html
|____index.html
|____list-kartu-keluarga.html
|____create-kartu-keluarga.html

               

```
