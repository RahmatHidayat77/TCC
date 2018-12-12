# TCC - Infrastructure as a Service

Infrastructure as code (IaC) adalah proses mengelola infrastructure (networks, virtual machines, load balancers, connection topology, dan sebagainya) melalui code/script, kita tidak perlu konfigurasi melalui perangkat keras atau aplikasi yang interaktif.
Kita cukup menuliskan konfigurasi yang kita inginkan melalui suatu script. Lalu script tadi tinggal kita jalankan melalui terminal. Jadi jika ada aplikasi yang ingin kita deploy, kita bisa mempersiapkan environment yang dibutuhkan melalui Infrastructure as code. Jika ingin merubah environment yang sudah ada, maka tinggal menggantinya melalui script tadi lalu di running ulang.<br>

Beberapa tools yang bisa digunakan sebagai Infrastructure as a Service yaitu :
- [Ansible](https://www.ansible.com/)
- [Puppet](https://puppet.com/)
- [Terraform](https://www.terraform.io/)
- [CHEF](https://www.chef.io/chef/)
- [Saltstack](https://www.saltstack.com/)
- [CFEngine](https://cfengine.com/)

Disini saya hanya akan menggunakan tool Terraform untuk contohnya. Pertama tuliskan dulu script configurasinya.<br>
Contoh script configurasi di Terraform, yang mana 'resource'nya menggunakan image & container docker :
```
provider "docker" {
  host = "tcp://docker:2345/"
}
resource "docker_image" "nginx" {
  name = "nginx:1.11-alpine"
}
resource "docker_container" "nginx-server" {
  name = "nginx-server"
  image = "${docker_image.nginx.latest}"
  ports {
    internal = 80
  }
  volumes {
    container_path  = "/usr/share/nginx/html"
    host_path = "/home/scrapbook/tutorial/www"
    read_only = true
  }
}
```
Dari script diatas terdiri dari 2 komponen utama yaitu provider & resource, dimana :<br>
Provider = bertanggung jawab menangani infrastructure & mengelola lifecycle dari resource.<br>
Resource = merupakan komponen dari infrastrukutur yang akan dibuat. Ex: container, image. <br>

Setelah itu ketikkan perintah untuk membuat excecution plan:<br>

`terraform plan -out config.tfplan`

argumen `-out` digunakan untuk menyimpan excecution plan.<br>

Untuk menjalankan execution plan ketikkan perintah : `terraform aplly`<br>
Setelah di apply akan keluar informasi konfigurasinya.

Lalu gunakan Docker CLI untuk melihat informasi container yang baru di deploy: `docker ps`

Gunakan `terraform show`, untuk menginspect container yang sudah di deploy.

Jika ingin merubah script config, maka ulangi langkah-langkah seperti pada penjelasan diatas.
