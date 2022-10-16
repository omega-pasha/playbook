# Домашнее задание к занятию "08.03 Использование Yandex Cloud"
## Подготовка
Развернул сервера при помощи Terraform. ниже конфиг для lighthouse
```
resource "yandex_compute_instance" "srvlh" {
  name                      = "srvlighthouse"
  zone                      = "ru-central1-a"
  allow_stopping_for_update = true
  platform_id = "standard-v3"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = var.centos7
      type     = "network-nvme"
      size     = "10"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.default.id
    nat       = true
  }

  metadata = {
     user-data = "${file("./meta.txt")}"
  }
}
```
 ## Задача 1,2,3
Дописал в плейбуку установку nginx и pull lighthouse из гита
```
 - name: Install Nginx
  hosts: centos_lh
  handlers:
    - name: Start Nginx
      become: true
      command: nginx
    - name: Reload Nginx
      become: true
      command: nginx -s reload 
  tasks:
    - name: Install epel-release
      yum:
        name: epel-release
        state: latest
      become: true
    - name: install Nginx
      yum:
        name: nginx
        state: latest
        update_cache: yes
      become: true
      notify: Start Nginx
    - name: Copy nginx config
      become: true
      template:
        src: files/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        mode: 0755
      notify: Reload Nginx
- name: Install lighthouse
  hosts: centos_lh
  handlers:
    - name: Reload Nginx
      become: true
      command: nginx -s reload
  tasks:
    - name: Install git
      yum:
        name: "git"
        state: present
        update_cache: yes
      become: true
    - name: clone from git
      ansible.builtin.git:
        repo: https://github.com/VKCOM/lighthouse.git
        dest: "{{ document_root }}"
        single_branch: yes
        version: master
      become: true
    - name: Copy lighthouse config
      become: true
      template:
        src: files/lighthouse.conf.j2
        dest: /etc/nginx/conf.d/default.conf
        mode: 0644
      notify: Reload Nginx
 ```
 ## Задача 4
 ```
 ---
clickhouse:
  hosts:
    centos_ch:
      ansible_host: 62.84.127.212
      ansible_user: pavel
      ansible_ssh_private_key_file: ~/.ssh/pomortest
lighthouse:
  hosts:
    centos_lh:
      ansible_host: 51.250.93.32
      ansible_user: pavel
      ansible_ssh_private_key_file: ~/.ssh/pomortest
vector:
  hosts:
    centos_vec:
      ansible_host: 51.250.1.224
      ansible_user: pavel
      ansible_ssh_private_key_file: ~/.ssh/pomortest
 ```
 ## Задача 8 
 Чтобы проверить идемпотентность, я сделал `terraform destroy` и заново сделал 3 сервера. Все установилось без проблем
 
 https://disk.yandex.ru/i/t4dEmBEJWLNzsQ
 
 https://disk.yandex.ru/i/LN_0P1S94EtziQ
 
 
