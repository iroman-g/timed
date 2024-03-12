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

resource "yandex_vpc_network" "develop_db" {
  name = var.db_vpc_name
}

resource "yandex_vpc_subnet" "develop_db" {
  name           = var.db_subnet_name
  zone           = var.db_default_zone
  network_id     = yandex_vpc_network.develop_db.id
  v4_cidr_blocks = var.db_default_cidr
}

data "yandex_compute_image" "db_ubuntu" {
  family = var.db_image_family
}

resource "yandex_compute_instance" "platform_db" {
  name        = var.vm_db_name
  platform_id = var.vm_db_platform_id
  resources {
    cores         = var.db_cores
    memory        = var.db_memory
    core_fraction = var.db_core_fraction
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
    subnet_id = yandex_vpc_subnet.develop_db.id
      nat       = true
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${var.db_ssh_root_key}"
  }
}
```

vms_platform.tf
```
###cloud vars

#netology-develop-platform-web

variable "vm_web_name" {
  type    = string
  default = "netology-develop-platform-web"
}

variable "image_family" {
  description = "Family of the Yandex Compute image"
  type        = string
  default     = "ubuntu-2004-lts"
}

variable "vm_web_platform_id" {
  description = "ID of the Yandex Compute platform"
  type        = string
  default     = "standard-v2"
}

variable "vm_web_cores" {
  description = "Number of CPU cores for the VM"
  type        = number
  default     = 2
}

variable "vm_web_memory" {
  description = "RAM for the VM"
  type        = number
  default     = 1
}

variable "vm_web_core_fraction" {
  description = "Core fraction for VM"
  type        = number
  default     = 5
}

variable "vm_web_ssh_root_key" {
  description = "SSH root key for VM access"
  type        = string
}

variable "token" {
  type        = string
  default     = "y0"
  description = "OAuth-token; https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token"
}

variable "cloud_id" {
  type        = string
  default     = "b1"
  description = "https://cloud.yandex.ru/docs/resource-manager/operations/cloud/get-id"
}

variable "folder_id" {
  type        = string
  default     = "b1"
  description = "https://cloud.yandex.ru/docs/resource-manager/operations/folder/get-id"
}

variable "vpc_name" {
  type        = string
  default     = "develop"
  description = "VPC network & subnet name"
}

variable "subnet_name" {
  type    = string
  default = "develop"
}

variable "default_zone" {
  type        = string
  default     = "ru-central1-a"
  description = "https://cloud.yandex.ru/docs/overview/concepts/geo-scope"
}
variable "default_cidr" {
  type        = list(string)
  default     = ["10.0.1.0/24"]
  description = "https://cloud.yandex.ru/docs/vpc/operations/subnet-create"
}

###ssh vars

variable "vms_ssh_root_key" {
  type        = string
  default     = "ssh-ed25519 AAAA"
  description = "ssh-keygen -t ed25519"
}

#netology-develop-platform-db


variable "vm_db_name" {
  type    = string
  default = "netology-develop-platform-web"
}

variable "db_image_family" {
  description = "Family of the Yandex Compute image"
  type        = string
  default     = "ubuntu-2004-lts"
}

variable "vm_db_platform_id" {
  description = "ID of the Yandex Compute platform"
  type        = string
  default     = "standard-v2"
}

variable "db_cores" {
  description = "Number of CPU cores for the VM"
  type        = number
  default     = 2
}

variable "db_memory" {
  description = "RAM for the VM"
  type        = number
  default     = 1
}

variable "db_core_fraction" {
  description = "Core fraction for VM"
  type        = number
  default     = 20
}

variable "db_ssh_root_key" {
  description = "SSH root key for VM access"
  type        = string
}

variable "db_token" {
  type        = string
  default     = "y0"
  description = "OAuth-token; https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token"
}

variable "db_cloud_id" {
  type        = string
  default     = "b1"
  description = "https://cloud.yandex.ru/docs/resource-manager/operations/cloud/get-id"
}

variable "db_folder_id" {
  type        = string
  default     = "b1"
  description = "https://cloud.yandex.ru/docs/resource-manager/operations/folder/get-id"
}

variable "db_vpc_name" {
  type        = string
  default     = "develop_db"
  description = "VPC network & subnet name"
}

variable "db_subnet_name" {
  type    = string
  default = "develop_db"
}

variable "db_default_zone" {
  type        = string
  default     = "ru-central1-b"
  description = "https://cloud.yandex.ru/docs/overview/concepts/geo-scope"
}
variable "db_default_cidr" {
  type        = list(string)
  default     = ["10.0.1.0/24"]
  description = "https://cloud.yandex.ru/docs/vpc/operations/subnet-create"
}


```



error:
```
│ Error: Error while requesting API to create instance: server-request-id = 3785ea90-b43b-4956-b4c3-26d1311ede4a server-trace-id = f48f811f433b14e2:b05116fc9b13af78:f48f811f433b14e2:1 client-request-id = 98af5450-8c62-43d2-bbd0-f6aee3294421 client-trace-id = ddde57a0-9416-4a9d-b20f-f0cb1c2b94c4 rpc error: code = InvalidArgument desc = Illegal argument subnet_id.zone_id
│ 
│   with yandex_compute_instance.platform_db,
│   on main.tf line 65, in resource "yandex_compute_instance" "platform_db":
│   65: resource "yandex_compute_instance" "platform_db" {
```
