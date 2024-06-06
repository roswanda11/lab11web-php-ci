<table>
  <tr>
    <th colspan="2">DATA MAHASISWA</th>
  </tr>
  <tr>
    <td>Nama</td>
    <td>Roswanda Nuraini</td>
  </tr>
  <tr>
    <td>NIM</td>
    <td>312210328</td>
  </tr>
  <tr>
    <td>Kelas</td>
    <td>TI.22.A3</td>
  </tr>
</table>

# <p align="center">Praktikum 6: AJAX</p>

# Langkah-langkah Praktikum

### Persiapan

Buka Kembali project sebelumnya, kemudian kita tambahkan Pustaka yang dibutuhkan pada project tersebut.

## Menambahkan Pustaka jQuery.

Kita akan menggunakan pustaka jQuery untuk mempermudah proses AJAX. Download pustaka jQuery versi terbaru dari https://jquery.com dan ekstrak filenya.

Salin file jquery-3.6.0.min.js ke folder public/assets/js.

# Membuat Model.

Pada modul sebelumnya sudah dibuat ArtikelModel, pada modul ini kita akan memanfaatkan model tersebut agar dapat diakses melalui AJAX.

# Membuat AJAX Controller

        <?php
        namespace App\Controllers;
        use CodeIgniter\Controller;
        use CodeIgniter\HTTP\Request;
        use CodeIgniter\HTTP\Response;
        use App\Models\ArtikelModel;
        class AjaxController extends Controller
        {
        public function index()
        {
        return view('ajax/index');
        }
        public function getData()
        {
        $model = new ArtikelModel();
        $data = $model->findAll();
        // Kirim data dalam format JSON
        return $this->response->setJSON($data);
        }
        public function delete($id)
        {
        $model = new ArtikelModel();
        $data = $model->delete($id);
        $data = [
        'status' => 'OK'
        ];
        // Kirim data dalam format JSON
        return $this->response->setJSON($data);
        }
        }

![ajax1](https://github.com/roswanda11/lab11web-php-ci/assets/115516632/8116d4cf-0d5e-4adc-bb00-4faa68a1ba91)

# Membuat View

          <?= $this->include('template/header'); ?>
          
          <h1>Data Artikel</h1>
          
          <table class="table-data" id="artikelTable">
            <thead>
              <tr>
                <th>ID</th>
                <th>Judul</th>
                <th>Status</th>
                <th>Aksi</th>
              </tr>
            </thead>
            <tbody></tbody>
          </table>
          
          <script src="<?= base_url('assets/js/jquery-3.6.0.min.js') ?>"></script>
          <script>
            $(document).ready(function() {
              // Function to display a loading message while data is fetched
              function showLoadingMessage() {
                $('#artikelTable tbody').html('<tr><td colspan="4">Loading
          data...</td></tr>');                                    
              }
          
              // Buat fungsi load data
              function loadData() {
                showLoadingMessage(); // Display loading message initially
          
                // Lakukan request AJAX ke URL getData
                $.ajax({
                    url: "<?= base_url('ajax/getData') ?>",
                    method: "GET",
                    dataType: "json",
                    success: function(data) {
          
                      // Tampilkan data yang diterima dari server
                      var tableBody = "";
                      for (var i = 0; i < data.length; i++) {
                          var row = data[i];
                          tableBody += '<tr>';
                          tableBody += '<td>' + row.id + '</td>';
                          tableBody += '<td>' + row.judul + '</td>';
          
                        // Add a placeholder for the "Status" column (modify
          as needed)
          
                        tableBody += '<td><span class="status">---
          </span></td>';
          
                        tableBody += '<td>';
          
                        // Replace with your desired actions (e.g., edit,
          delete)
          
            tableBody += '<a href="<?= base_url('artikel/edit/')
          
          ?>' + row.id + '" class="btn btn-primary">Edit</a>';
          
                        tableBody += ' <a href="#" class="btn btn-danger
          
          btn-delete" data-id="' + row.id + '">Delete</a>';
          
            tableBody += '</td>';
          
                        tableBody += '</tr>';
                      }
                      $('#artikelTable tbody').html(tableBody);
                  }
              });
          }
          loadData();
          
              // Implement actions for buttons (e.g., delete confirmation)
              $(document).on('click', '.btn-delete', function(e) {
                e.preventDefault();
                var id = $(this).data('id');
          
                // Add confirmation dialog or handle deletion logic here
                
                // hapus data;
                if (confirm('Apakah Anda yakin ingin menghapus artikel ini?'))
          
          {
                  $.ajax({
                      url: "<?= base_url('artikel/delete/') ?>" + id,
                      method: "DELETE",
                      success: function(data) {
                      loadData(); // Reload Datatables to reflect changes
                      },
                    error: function(jqXHR, textStatus, errorThrown) {
                          alert('Error deleting article: ' + textStatus +
          
          errorThrown);
                      }
                  });
              }
              console.log('Delete button clicked for ID:', id);
              });
            });
          </script>
          
          <?= $this->include('template/footer'); ?>

![ajax view](https://github.com/roswanda11/lab11web-php-ci/assets/115516632/4332d8aa-22f9-4816-a84d-9b47123ece73)
