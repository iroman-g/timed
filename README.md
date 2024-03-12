main.tf
```
resource "yandex_vpc_network" "develop" {
  name = var.vpc_name
}

resource "yandex_vpc_subnet" "develop" {
  name           = var.subnet_name
  zone           = var.default_zone
  network_id     = yandex_vpc_network.develop.id
  v4_cidr_blocks = var.default_cidr
}


data "yandex_compute_image" "ubuntu" {
  family = var.image_family
}

resource "yandex_compute_instance" "platform" {
  name        = var.vm_web_name
  platform_id = var.vm_web_platform_id
  resources {
    cores         = var.vm_web_cores
    memory        = var.vm_web_memory
    core_fraction = var.vm_web_core_fraction
  }


  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }
  
  scheduling_policy {
    preemptible = true
  }
  
  network_interface {
    subnet_id = yandex_vpc_subnet.develop.id
    nat       = true
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${var.vm_web_ssh_root_key}"
  }
}

#netology-develop-platform-db

resource "yandex_vpc_network" "develop-db" {
  name = var.db_vpc_name
}

resource "yandex_vpc_subnet" "develop-db" {
  name           = var.db_subnet_name
  zone           = var.db_default_zone
  network_id     = yandex_vpc_network.develop-db.id
  v4_cidr_blocks = var.db_default_cidr
}

resource "yandex_compute_instance" "platform-db" {
  name        = var.vm_db_name
  platform_id = var.vm_db_platform_id
  resources {
    cores         = var.vm_db_cores
    memory        = var.vm_db_memory
    core_fraction = var.vm_db_core_fraction
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }
  
  scheduling_policy {
    preemptible = true
  }
  
  network_interface {
    subnet_id = "${yandex_vpc_subnet.develop-db.id}.${var.db_default_zone}"
      nat       = true
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${var.vm_db_ssh_root_key}"
  }
}
```

error:
```
│ Error: Error while requesting API to create instance: server-request-id = ccc4072d-1364-402f-8cd7-f50754c268a3 server-trace-id = c6adb49fa656a011:45685d0e0813bff6:c6adb49fa656a011:1 client-request-id = 7720f901-c968-4dc4-bb42-2600a353ed25 client-trace-id = 785f11c1-d21d-4a04-9f40-23bfda263bde rpc error: code = NotFound desc = Subnet e2le0lqqihl92i8b0n6a.ru-central1-b not found
│ 
│   with yandex_compute_instance.platform-db,
│   on main.tf line 61, in resource "yandex_compute_instance" "platform-db":
│   61: resource "yandex_compute_instance" "platform-db" {
```
