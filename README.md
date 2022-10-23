# Домашнее задание к занятию "08.04 Работа с roles"

## Описание lighthouse-role
Установку Nginx сделал отдельной ролью

В `handlers` перенес перезапуск nginx
```
    - name: Restart Nginx
      become: true
      service: name=nginx state=restarted
```
 в `vars` добавил путь до рабочей директории
 ```
 document_root: /var/www/lighthous
 ```
 в `templates` добавил конфигурацию для nginx
 
 ```
 server {
    listen      80;
    server_name localhost;

    access_log   /var/log/nginx/lighthouse_access.log  main;

    location / {
      root    {{ document_root }};
      index index.html;
    }

}
```
в `tasks` перенес все таски

```
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
        src: lighthouse.conf.j2
        dest: /etc/nginx/conf.d/lighthouse.conf
      notify: Restart Nginx
```
## Описание vector-role
В `defaults` внес версию вектора
```
vector_version: "0.23.3"
```
В handlers перенес перезапуск службы вектора

```
    - name: Restart Vector
      become: true
      service: name=vector state=restarted
```
в `tasks` перенес все таски
```
    - name: Get vector distrib
      become: true
      ansible.builtin.get_url:
        url: https://packages.timber.io/vector/{{ vector_version }}/vector-{{ vector_version }}-1.{{ ansible_architecture }}.rpm
        dest: "./vector-{{ vector_version }}.rpm"

    - name: Install vector packages
      become: true
      ansible.builtin.yum:
        name: "./vector-{{ vector_version }}.rpm"
        disable_gpg_check: yes
      notify: Restart Vector
```
## ссылки на роли 
[Vector](https://github.com/omega-pasha/vector-role)

[Nginx](https://github.com/omega-pasha/nginx-role)

[Lighthouse](https://github.com/omega-pasha/lighthouse-role)

[Clickhouse](https://github.com/omega-pasha/clickhouse-role)
