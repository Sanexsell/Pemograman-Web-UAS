<?php
session_start();
require_once "koneksi.php";

if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}

// Mengambil data barang
$query = mysqli_query($conn, "SELECT * FROM barang ORDER BY id_barang DESC");
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Data Barang - Admin Panel</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.datatables.net/1.10.22/css/dataTables.bootstrap4.min.css">
    
    <style>
        body { font-family: 'Poppins', sans-serif; background-color: #f4f6f9; overflow-x: hidden;}
        .sidebar { background: #1e3c72; min-height: 100vh; color: #fff; box-shadow: 4px 0 10px rgba(0,0,0,0.1); }
        .sidebar a { color: #cfd8dc; text-decoration: none; display: block; padding: 15px 25px; transition: 0.3s; font-weight: 500;}
        .sidebar a:hover { background: rgba(255,255,255,0.1); color: #fff; padding-left: 30px;}
        .sidebar .active { background: rgba(255,255,255,0.15); color: #fff; border-left: 4px solid #00c6ff; }
        .navbar-custom { background: #fff; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .content-area { padding: 30px; }
    </style>
</head>
<body>
    <div class="d-flex">
        
        <div class="sidebar" style="width: 260px;">
            <h4 class="text-center py-4 border-bottom border-secondary m-0 font-weight-bold">
                <i class="fas fa-cubes mr-2 text-info"></i>Admin Panel
            </h4>
            <div class="mt-3">
                <a href="dashboard.php"><i class="fas fa-tachometer-alt mr-3"></i> Dashboard</a>
                <a href="user.php"><i class="fas fa-users mr-3"></i> Manajemen User</a>
                <a href="barang.php" class="active"><i class="fas fa-box mr-3"></i> Data Barang (UAS)</a>
            </div>
        </div>

        <div class="flex-grow-1">
            
            <nav class="navbar navbar-expand navbar-light navbar-custom px-4 py-3">
                <ul class="navbar-nav ml-auto align-items-center">
                    <li class="nav-item mr-4">
                        <span class="font-weight-bold text-dark">
                            <i class="fas fa-user-circle text-primary mr-1"></i> <?= htmlspecialchars($_SESSION['username']); ?>
                        </span>
                    </li>
                    <li class="nav-item">
                        <a href="logout.php" class="btn btn-danger btn-sm rounded-pill px-4 shadow-sm" onclick="return confirm('Apakah Anda yakin ingin logout?')">
                            <i class="fas fa-sign-out-alt mr-1"></i> Logout
                        </a>
                    </li>
                </ul>
            </nav>

            <div class="content-area">
                <div class="d-flex justify-content-between align-items-center mb-4">
                    <h2 class="font-weight-bold m-0">Data Master Barang</h2>
                    <a href="barang_tambah.php" class="btn btn-primary rounded-pill shadow-sm px-4">
                        <i class="fas fa-plus mr-1"></i> Tambah Barang Baru
                    </a>
                </div>
                
                <div class="card border-0 shadow-sm rounded-lg">
                    <div class="card-body">
                        <table id="tabelBarang" class="table table-hover border-0">
                           <thead class="bg-light">
                                <tr>
                                    <th width="5%" class="text-center">No</th>
                                    <th>Kode Barang</th>
                                    <th>Nama Barang</th>
                                    <th>Kategori</th>
                                    <th>Harga</th>
                                    <th class="text-center">Stok</th>
                                    <th width="10%" class="text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody>
                                <?php
                                $no = 1;
                                while ($row = mysqli_fetch_assoc($query)) { ?>
                                <tr>
                                    <td class="text-center"><?= $no++; ?></td>
                                    <td><span class="badge badge-secondary"><?= htmlspecialchars($row['kode_barang']); ?></span></td>
                                    <td class="font-weight-bold"><?= htmlspecialchars($row['nama_barang']); ?></td>
                                    <td><?= htmlspecialchars($row['kategori']); ?></td>
                                    <td>Rp <?= number_format($row['harga'], 0, ',', '.'); ?></td>
                                    <td class="text-center">
                                        <span class="badge <?= ($row['stok'] > 10) ? 'badge-success' : 'badge-danger'; ?> badge-pill px-3 py-2">
                                            <?= htmlspecialchars($row['stok']); ?>
                                        </span>
                                    </td>
                                    
                                    <td class="align-middle">
                                        <div class="d-flex flex-column align-items-center">
                                            <a href="#" class="btn btn-success btn-sm rounded-pill shadow-sm mb-2" data-toggle="modal" data-target="#editModal<?= $row['id_barang']; ?>" style="width: 90px;">
                                                <i class="fas fa-edit"></i> Edit
                                            </a>
                                    
                                            <a href="barang_delete.php?id=<?= $row['id_barang']; ?>" class="btn btn-danger btn-sm rounded-pill shadow-sm" onclick="return confirm('Yakin ingin menghapus barang ini?')" style="width: 90px;">
                                                <i class="fas fa-trash"></i> Hapus
                                            </a>
                                        </div>
                                    </td>
                                </tr>
                                </tr> <div class="modal fade" id="editModal<?= $row['id_barang']; ?>" tabindex="-1" role="dialog">
                                <div class="modal-dialog">
                                    <div class="modal-content border-0 shadow-lg">
                                        <div class="modal-header border-bottom-0">
                                            <h5 class="modal-title font-weight-bold">
                                                <i class="fas fa-edit text-success mr-2"></i>Update Data Barang
                                            </h5>
                                            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                                <span aria-hidden="true">&times;</span>
                                            </button>
                                        </div>
                                        
                                        <div class="modal-body bg-light">
                                            <form action="action/ubah_barang.php" method="post">
                                                <input type="hidden" name="id_barang" value="<?= $row['id_barang']; ?>">
                                                
                                                <div class="form-group">
                                                    <label>Kode Barang</label>
                                                    <input type="text" name="kode_barang" class="form-control rounded" value="<?= htmlspecialchars($row['kode_barang']); ?>" required readonly>
                                                </div>
                                                
                                                <div class="form-group">
                                                    <label>Nama Barang</label>
                                                    <input type="text" name="nama_barang" class="form-control rounded" value="<?= htmlspecialchars($row['nama_barang']); ?>" required>
                                                </div>
                                                
                                                <div class="form-group">
                                                    <label>Kategori</label>
                                                    <select name="kategori" class="form-control rounded" required>
                                                        <option value="Elektronik" <?= ($row['kategori'] == 'Elektronik') ? 'selected' : ''; ?>>Elektronik</option>
                                                        <option value="Furniture" <?= ($row['kategori'] == 'Furniture') ? 'selected' : ''; ?>>Furniture</option>
                                                        <option value="Pakaian" <?= ($row['kategori'] == 'Pakaian') ? 'selected' : ''; ?>>Pakaian</option>
                                                        <option value="Makanan" <?= ($row['kategori'] == 'Makanan') ? 'selected' : ''; ?>>Makanan</option>
                                                    </select>
                                                </div>
                                                
                                                <div class="row">
                                                    <div class="col-md-6 form-group">
                                                        <label>Harga (Rp)</label>
                                                        <input type="number" name="harga" class="form-control rounded" value="<?= $row['harga']; ?>" required>
                                                    </div>
                                                    <div class="col-md-6 form-group">
                                                        <label>Stok</label>
                                                        <input type="number" name="stok" class="form-control rounded" value="<?= $row['stok']; ?>" required>
                                                    </div>
                                                </div>
                                                
                                                <div class="modal-footer border-top-0 px-0 pb-0 mt-3">
                                                    <button type="button" class="btn btn-secondary rounded-pill px-4" data-dismiss="modal">Batal</button>
                                                    <button type="submit" name="update_barang" class="btn btn-success rounded-pill px-4">Simpan Perubahan</button>
                                                </div>
                                            </form>
                                        </div>
                                    </div>
                                </div>
                                </div>
                                <?php } ?>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            
        </div>
    </div>

   <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.bundle.min.js"></script>
    
    <script src="https://cdn.datatables.net/1.10.22/js/jquery.dataTables.min.js"></script>
    <script src="https://cdn.datatables.net/1.10.22/js/dataTables.bootstrap4.min.js"></script>
    <script>
        $(document).ready(function() {
            $('#tabelBarang').DataTable({
                "ordering": true,
                "info": true,
                "autoWidth": false,
                "language": {
                    "search": "Cari Barang:",
                    "lengthMenu": "Tampilkan _MENU_ data per halaman",
                    "zeroRecords": "Barang tidak ditemukan",
                    "info": "Menampilkan halaman _PAGE_ dari _PAGES_",
                    "infoEmpty": "Tidak ada data yang tersedia",
                    "paginate": { "next": "Selanjutnya", "previous": "Sebelumnya" }
                }
            });
        });
    </script>
</body>
</html>
<?php
session_start();
require_once "koneksi.php";

// Proteksi halaman agar hanya admin yang sudah login yang bisa akses
if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}

// Mengecek apakah ada ID yang dikirim dari tombol hapus
if (!isset($_GET['id']) || empty($_GET['id'])) {
    header("Location: barang.php");
    exit();
}

// Menangkap ID barang
$id = (int) $_GET['id'];

// Mengeksekusi query hapus menggunakan prepared statement agar aman
$stmt = mysqli_prepare($conn, "DELETE FROM barang WHERE id_barang=?");
mysqli_stmt_bind_param($stmt, "i", $id);

// Mengecek apakah proses hapus berhasil
if (mysqli_stmt_execute($stmt)) {
    echo "<script>alert('Data barang berhasil dihapus!'); window.location='barang.php';</script>";
} else {
    echo "<script>alert('Gagal menghapus data barang!'); window.location='barang.php';</script>";
}
?><?php
session_start();
require_once "koneksi.php";

if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}

if (isset($_POST['simpan'])) {
    $kode_barang = $_POST['kode_barang'];
    $nama_barang = $_POST['nama_barang'];
    $kategori = $_POST['kategori'];
    $harga = $_POST['harga'];
    $stok = $_POST['stok'];

    $query = "INSERT INTO barang (kode_barang, nama_barang, kategori, harga, stok) 
              VALUES ('$kode_barang', '$nama_barang', '$kategori', '$harga', '$stok')";
    
    if (mysqli_query($conn, $query)) {
        echo "<script>alert('Data barang berhasil ditambahkan!'); window.location='barang.php';</script>";
    } else {
        echo "<script>alert('Gagal menambahkan data! Kode barang mungkin sudah ada.'); window.location='barang_tambah.php';</script>";
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Tambah Data Barang</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <style> body { font-family: 'Poppins', sans-serif; background-color: #f4f6f9; } </style>
</head>
<body class="d-flex align-items-center justify-content-center" style="height: 100vh;">

    <div class="card shadow-sm border-0 rounded-lg" style="width: 500px;">
        <div class="card-header bg-white border-0 pt-4 pb-0">
            <h4 class="font-weight-bold text-center">Tambah Master Barang</h4>
        </div>
        <div class="card-body p-4">
            <form action="" method="post">
                <div class="form-group">
                    <label>Kode Barang</label>
                    <input type="text" name="kode_barang" class="form-control rounded" placeholder="Contoh: BRG-003" required>
                </div>
                <div class="form-group">
                    <label>Nama Barang</label>
                    <input type="text" name="nama_barang" class="form-control rounded" required>
                </div>
                <div class="form-group">
                    <label>Kategori</label>
                    <select name="kategori" class="form-control rounded" required>
                        <option value="">-- Pilih Kategori --</option>
                        <option value="Elektronik">Elektronik</option>
                        <option value="Furniture">Furniture</option>
                        <option value="Pakaian">Pakaian</option>
                        <option value="Makanan">Makanan</option>
                    </select>
                </div>
                <div class="row">
                    <div class="col-md-6 form-group">
                        <label>Harga (Rp)</label>
                        <input type="number" name="harga" class="form-control rounded" required>
                    </div>
                    <div class="col-md-6 form-group">
                        <label>Stok Awal</label>
                        <input type="number" name="stok" class="form-control rounded" required>
                    </div>
                </div>
                
                <div class="mt-4 text-center">
                    <a href="barang.php" class="btn btn-light rounded-pill px-4 mr-2">Batal</a>
                    <button type="submit" name="simpan" class="btn btn-primary rounded-pill px-4">Simpan Barang</button>
                </div>
            </form>
        </div>
    </div>

</body>
</html>-- phpMyAdmin SQL Dump
-- version 5.2.1
-- https://www.phpmyadmin.net/
--
-- Host: 127.0.0.1
-- Waktu pembuatan: 03 Jul 2026 pada 01.26
-- Versi server: 10.4.32-MariaDB
-- Versi PHP: 8.0.30

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- Database: `db_toko`
--

-- --------------------------------------------------------

--
-- Struktur dari tabel `barang`
--

CREATE TABLE `barang` (
  `id_barang` int(11) NOT NULL,
  `kode_barang` varchar(20) NOT NULL,
  `nama_barang` varchar(100) NOT NULL,
  `kategori` varchar(50) NOT NULL,
  `harga` int(11) NOT NULL,
  `stok` int(11) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

--
-- Dumping data untuk tabel `barang`
--

INSERT INTO `barang` (`id_barang`, `kode_barang`, `nama_barang`, `kategori`, `harga`, `stok`) VALUES
(1, 'BRG-001', 'Laptop ASUS ROG', 'Elektronik', 15000000, 10),
(2, 'BRG-002', 'Meja Kerja Minimalis', 'Furniture', 1200000, 25),
(3, 'BRG-003', 'Laptop MSI', 'Elektronik', 30000000, 10),
(4, 'BRG-004', 'Kursi PC GAMING', 'Furniture', 1500000, 25);

-- --------------------------------------------------------

--
-- Struktur dari tabel `users`
--

CREATE TABLE `users` (
  `id` int(11) NOT NULL,
  `username` varchar(50) NOT NULL,
  `password` varchar(255) NOT NULL,
  `nama_lengkap` varchar(100) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

--
-- Dumping data untuk tabel `users`
--

INSERT INTO `users` (`id`, `username`, `password`, `nama_lengkap`) VALUES
(2, 'exel', 'admin123', 'adminexel123');

--
-- Indexes for dumped tables
--

--
-- Indeks untuk tabel `barang`
--
ALTER TABLE `barang`
  ADD PRIMARY KEY (`id_barang`),
  ADD UNIQUE KEY `kode_barang` (`kode_barang`);

--
-- Indeks untuk tabel `users`
--
ALTER TABLE `users`
  ADD PRIMARY KEY (`id`),
  ADD UNIQUE KEY `username` (`username`);

--
-- AUTO_INCREMENT untuk tabel yang dibuang
--

--
-- AUTO_INCREMENT untuk tabel `barang`
--
ALTER TABLE `barang`
  MODIFY `id_barang` int(11) NOT NULL AUTO_INCREMENT, AUTO_INCREMENT=7;

--
-- AUTO_INCREMENT untuk tabel `users`
--
ALTER TABLE `users`
  MODIFY `id` int(11) NOT NULL AUTO_INCREMENT, AUTO_INCREMENT=6;
COMMIT;

/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
<?php
// Dua baris ini akan memaksa browser menampilkan pesan jika ada error
error_reporting(E_ALL);
ini_set('display_errors', 1);

session_start();

// Perbaikan penulisan $_SESSION dan kurung kurawal
if (isset($_SESSION['login']) && $_SESSION['login'] === true) {
    header("Location: dashboard.php");
    exit();
} else {
    header("Location: login.php");
    exit();
}
?><?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "db_toko";

// Tambahkan tanda sama dengan (=) dan garis bawah (_) yang hilang
$conn = mysqli_connect($host, $user, $pass, $db);

if (!$conn) {
    die("Koneksi database gagal: " . mysqli_connect_error());
}
?><?php
error_reporting(E_ALL);
ini_set('display_errors', 1);

session_start();
require_once "koneksi.php";

$error = "";
if (isset($_POST['login'])) {
    $username = mysqli_real_escape_string($conn, $_POST['username']);
    $password = mysqli_real_escape_string($conn, $_POST['password']);
    
    // Cek user di database [cite: 45-46]
    $query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
    $result = mysqli_query($conn, $query);
    
    if (mysqli_num_rows($result) == 1) { // [cite: 48]
        // Jika data ditemukan, set session login [cite: 49-52]
        $_SESSION['login'] = true;
        $_SESSION['username'] = $username;
        header("Location: dashboard.php"); // 
        exit();
    } else {
        $error = "Username atau Password salah!"; // [cite: 55]
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login - Modern CRUD</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            height: 100vh;
            overflow: hidden;
        }
        .login-container {
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .login-card {
            background: rgba(255, 255, 255, 0.95);
            border: none;
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease;
            width: 100%;
            max-width: 430px;
        }
        .login-card:hover {
            transform: translateY(-5px);
        }
        .brand-icon {
            background: linear-gradient(135deg, #00c6ff 0%, #0072ff 100%);
            width: 70px;
            height: 70px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 30px;
            margin: 0 auto 20px;
            box-shadow: 0 5px 15px rgba(0, 114, 255, 0.4);
        }
        .form-control {
            background: #f4f6f9;
            border: 2px solid transparent;
            border-radius: 12px;
            height: 50px;
            padding-left: 45px;
            font-weight: 400;
            transition: all 0.3s ease;
        }
        .form-control:focus {
            background: #fff;
            border-color: #0072ff;
            box-shadow: none;
        }
        .input-group-icon {
            position: absolute;
            left: 18px;
            top: 16px;
            color: #a0a5b5;
            transition: all 0.3s ease;
            z-index: 10;
        }
        .form-group:focus-within .input-group-icon {
            color: #0072ff;
        }
        .btn-login {
            background: linear-gradient(135deg, #0072ff 0%, #00c6ff 100%);
            border: none;
            border-radius: 12px;
            height: 50px;
            font-weight: 600;
            font-size: 16px;
            letter-spacing: 0.5px;
            box-shadow: 0 5px 15px rgba(0, 114, 255, 0.3);
            transition: all 0.3s ease;
        }
        .btn-login:hover {
            background: linear-gradient(135deg, #005cff 0%, #00b3ff 100%);
            transform: scale(1.02);
            box-shadow: 0 7px 20px rgba(0, 114, 255, 0.4);
        }
        .alert-custom {
            background-color: #ffeef0;
            border-left: 4px solid #ff4d4d;
            color: #d93838;
            border-radius: 8px;
            font-size: 14px;
            animation: shake 0.4s ease-in-out;
        }
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-6px); }
            75% { transform: translateX(6px); }
        }
    </style>
</head>
<body>
    
    <div class="container login-container">
        <div class="card login-card p-4">
            <div class="card-body">
                
                <div class="brand-icon">
                    <i class="fas fa-cubes"></i>
                </div>
                
                <h3 class="text-center font-weight-bold text-dark mb-1">Welcome Back</h3>
                <p class="text-center text-muted small mb-4">Silakan masuk ke sistem manajemen toko</p>
                
                <?php if ($error != ""): ?>
                    <div class="alert alert-custom p-3 text-center mb-3">
                        <i class="fas fa-exclamation-circle mr-2"></i> <?= $error; ?>
                    </div>
                <?php endif; ?>
                
                <form action="login.php" method="post">
                    <div class="form-group position-relative mb-3">
                        <i class="fas fa-user input-group-icon"></i>
                        <input type="text" name="username" class="form-control" placeholder="Masukkan Username" required autocomplete="off"> 
                    </div>
                    
                    <div class="form-group position-relative mb-4">
                        <i class="fas fa-lock input-group-icon"></i>
                        <input type="password" name="password" class="form-control" placeholder="Masukkan Password" required> 
                    </div>
                    
                    <button type="submit" name="login" class="btn btn-primary btn-block btn-login text-white">
                        Sign In <i class="fas fa-arrow-right ml-2"></i>
                    </button>
                </form>
                
            </div>
        </div>
    </div>

</body>
</html><?php
session_start();
session_unset();
session_destroy();
header("Location: login.php");
exit();
?><?php
session_start();
require_once "koneksi.php";

if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}

// Mengambil data user
$query = mysqli_query($conn, "SELECT * FROM users ORDER BY id DESC");
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Manajemen User - Admin Panel</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.datatables.net/1.10.22/css/dataTables.bootstrap4.min.css">
    
    <style>
        body { font-family: 'Poppins', sans-serif; background-color: #f4f6f9; overflow-x: hidden;}
        .sidebar { background: #1e3c72; min-height: 100vh; color: #fff; box-shadow: 4px 0 10px rgba(0,0,0,0.1); }
        .sidebar a { color: #cfd8dc; text-decoration: none; display: block; padding: 15px 25px; transition: 0.3s; font-weight: 500;}
        .sidebar a:hover { background: rgba(255,255,255,0.1); color: #fff; padding-left: 30px;}
        .sidebar .active { background: rgba(255,255,255,0.15); color: #fff; border-left: 4px solid #00c6ff; }
        .navbar-custom { background: #fff; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .content-area { padding: 30px; }
    </style>
</head>
<body>
    <div class="d-flex">
        
        <div class="sidebar" style="width: 260px;">
            <h4 class="text-center py-4 border-bottom border-secondary m-0 font-weight-bold">
                <i class="fas fa-cubes mr-2 text-info"></i>Admin Panel
            </h4>
            <div class="mt-3">
                <a href="dashboard.php"><i class="fas fa-tachometer-alt mr-3"></i> Dashboard</a>
                <a href="user.php" class="active"><i class="fas fa-users mr-3"></i> Manajemen User</a>
                <a href="barang.php"><i class="fas fa-box mr-3"></i> Data Barang (UAS)</a>
            </div>
        </div>

        <div class="flex-grow-1">
            
            <nav class="navbar navbar-expand navbar-light navbar-custom px-4 py-3">
                <ul class="navbar-nav ml-auto align-items-center">
                    <li class="nav-item mr-4">
                        <span class="font-weight-bold text-dark">
                            <i class="fas fa-user-circle text-primary mr-1"></i> <?= htmlspecialchars($_SESSION['username']); ?>
                        </span>
                    </li>
                    <li class="nav-item">
                        <a href="logout.php" class="btn btn-danger btn-sm rounded-pill px-4 shadow-sm" onclick="return confirm('Apakah Anda yakin ingin logout?')">
                            <i class="fas fa-sign-out-alt mr-1"></i> Logout
                        </a>
                    </li>
                </ul>
            </nav>

            <div class="content-area">
                <div class="d-flex justify-content-between align-items-center mb-4">
                    <h2 class="font-weight-bold m-0">Data Manajemen User</h2>
                    <a href="user_tambah.php" class="btn btn-primary rounded-pill shadow-sm px-4">
                        <i class="fas fa-user-plus mr-1"></i> Tambah User Baru
                    </a>
                </div>
                
                <div class="card border-0 shadow-sm rounded-lg">
                    <div class="card-body">
                        <table id="tabelUser" class="table table-hover border-0">
                            <thead class="bg-light">
                                <tr>
                                    <th width="10%" class="text-center">No</th>
                                    <th>Username</th>
                                    <th width="20%" class="text-center">Status Akun</th>
                                    <th width="15%" class="text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody>
                                <?php
                                $no = 1;
                                while ($row = mysqli_fetch_assoc($query)) { ?>
                                <tr>
                                    <td class="text-center align-middle"><?= $no++; ?></td>
                                    <td class="align-middle font-weight-bold">
                                        <i class="fas fa-user text-secondary mr-2"></i><?= htmlspecialchars($row['username']); ?>
                                    </td>
                                    <td class="text-center align-middle">
                                        <span class="badge badge-success badge-pill px-3 py-2"><i class="fas fa-check-circle mr-1"></i> Aktif</span>
                                    </td>
                                    
                                    <td class="align-middle">
                                        <div class="d-flex flex-column align-items-center">
                                            <a href="#" class="btn btn-success btn-sm rounded-pill shadow-sm mb-2" data-toggle="modal" data-target="#editUserModal<?= $row['id']; ?>" style="width: 90px;">
                                                <i class="fas fa-edit"></i> Edit
                                            </a>

                                            <a href="user_delete.php?id=<?= $row['id']; ?>" class="btn btn-danger btn-sm rounded-pill shadow-sm" onclick="return confirm('Yakin ingin menghapus user ini?')" style="width: 90px;">
                                                <i class="fas fa-trash"></i> Hapus
                                            </a>
                                        </div>
                                    </td>
                                </tr>

                                <div class="modal fade" id="editUserModal<?= $row['id']; ?>" tabindex="-1" role="dialog">
                                    <div class="modal-dialog">
                                        <div class="modal-content border-0 shadow-lg">
                                            <div class="modal-header border-bottom-0">
                                                <h5 class="modal-title font-weight-bold">
                                                    <i class="fas fa-user-edit text-success mr-2"></i>Update Data User
                                                </h5>
                                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                                    <span aria-hidden="true">&times;</span>
                                                </button>
                                            </div>
                                            
                                            <div class="modal-body bg-light">
                                                <form action="action/ubah_user.php" method="post">
                                                    <input type="hidden" name="id" value="<?= $row['id']; ?>">
                                                    
                                                    <div class="form-group">
                                                        <label>Username</label>
                                                        <input type="text" name="username" class="form-control rounded" value="<?= htmlspecialchars($row['username']); ?>" required>
                                                    </div>
                                                    
                                                    <div class="form-group">
                                                        <label>Password Baru (Kosongkan jika tidak ingin diubah)</label>
                                                        <input type="password" name="password" class="form-control rounded" placeholder="Ketik password baru...">
                                                    </div>
                                                    
                                                    <div class="modal-footer border-top-0 px-0 pb-0 mt-3">
                                                        <button type="button" class="btn btn-secondary rounded-pill px-4" data-dismiss="modal">Batal</button>
                                                        <button type="submit" name="update_user" class="btn btn-success rounded-pill px-4">Simpan Perubahan</button>
                                                    </div>
                                                </form>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <?php } ?>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            
        </div>
    </div>

    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.bundle.min.js"></script>
    <script src="https://cdn.datatables.net/1.10.22/js/jquery.dataTables.min.js"></script>
    <script src="https://cdn.datatables.net/1.10.22/js/dataTables.bootstrap4.min.js"></script>
    <script>
        $(document).ready(function() {
            $('#tabelUser').DataTable({
                "ordering": true,
                "info": true,
                "autoWidth": false,
                "language": {
                    "search": "Cari User:",
                    "lengthMenu": "Tampilkan _MENU_ user per halaman",
                    "zeroRecords": "User tidak ditemukan",
                    "info": "Menampilkan halaman _PAGE_ dari _PAGES_",
                    "infoEmpty": "Tidak ada data yang tersedia",
                    "paginate": { "next": "Selanjutnya", "previous": "Sebelumnya" }
                }
            });
        });
    </script>
</body>
</html><?php
session_start();
require_once "koneksi.php";
if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}<?php
session_start();
require_once "koneksi.php";

if (!isset($_SESSION['login'])) {
    header("Location: login.php");
    exit();
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Tambah User - Admin Panel</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { background-color: #f4f6f9; font-family: 'Poppins', sans-serif; }
        .card-form { max-width: 500px; margin: 50px auto; }
    </style>
</head>
<body>
    <div class="container">
        <div class="card card-form border-0 shadow-lg rounded-lg">
            <div class="card-header bg-primary text-white text-center py-3 border-0">
                <h5 class="m-0 font-weight-bold"><i class="fas fa-user-plus mr-2"></i>Tambah User Baru</h5>
            </div>
            <div class="card-body p-4">
                <form action="action/tambah_user_proses.php" method="post">
                    <div class="form-group">
                        <label>Username</label>
                        <input type="text" name="username" class="form-control rounded" required placeholder="Masukkan username...">
                    </div>
                    <div class="form-group">
                        <label>Password</label>
                        <input type="password" name="password" class="form-control rounded" required placeholder="Masukkan password...">
                    </div>
                    <div class="mt-4">
                        <button type="submit" name="tambah_user" class="btn btn-primary btn-block rounded-pill">Simpan Data</button>
                        <a href="user.php" class="btn btn-secondary btn-block rounded-pill">Kembali</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</body>
</html>
if (!isset($_GET['id']) || empty($_GET['id'])) {
    header("Location: user.php");
    exit();
}
$id = (int) $_GET['id'];
$stmt = mysqli_prepare($conn, "DELETE FROM users WHERE id=?");
mysqli_stmt_bind_param($stmt, "i", $id);

if (mysqli_stmt_execute($stmt)) {
    echo "<script>alert('Data user berhasil dihapus'); window.location='user.php';</script>";
} else {
    echo "<script>alert('Gagal menghapus data user'); window.location='user.php';</script>";
}
?>
