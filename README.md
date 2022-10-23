# Домашнее задание к занятию "08.04 Работа с roles"
## Задача 1
```
---
clickhouse:
  hosts:
      centos_cv:
        ansible_host: my_ip
        ansible_user: root
        ansible_ssh_private_key_file: ~/.ssh/pomortsev
 ```
 ## Задача 2,3,4
 
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
 ```
 ## Задача 5
 
 Ошибок было много
 - Не создавалась база, поскольку служба не стартовала исправил принудительным вызовом handlers
 ```
     - name: Flush handlers
       meta: flush_handlers
 ```
 - Не устанавливался Vector требовал библиотек, которых, я так понял, нет в Centos7 установил 8ой
 - Но 8й на скачанные пакеты, жаловался, что они не подписаны. прописал в playbook `disable_gpg_check: yes` чтобы он их не проверял
 
 ## Задача 6
 
 `> ansible-playbook -i inventory/prod.yml site.yml --check`
```
PLAY [Install Clickhouse] **********************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************
ok: [centos_cv]

TASK [Get clickhouse distrib] ******************************************************************************************************************************
ok: [centos_cv] => (item=clickhouse-client)
ok: [centos_cv] => (item=clickhouse-server)
failed: [centos_cv] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:admin_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************
ok: [centos_cv]

TASK [Install clickhouse packages] *************************************************************************************************************************
ok: [centos_cv]

TASK [Flush handlers] **************************************************************************************************************************************

TASK [Create database] *************************************************************************************************************************************
skipping: [centos_cv]

TASK [Get vector distrib] **********************************************************************************************************************************
ok: [centos_cv]

TASK [Install vector packages] *****************************************************************************************************************************
ok: [centos_cv]

TASK [Start vector service] ********************************************************************************************************************************
changed: [centos_cv]

PLAY RECAP *************************************************************************************************************************************************
centos_cv                  : ok=6    changed=1    unreachable=0    failed=0    skipped=1    rescued=1    ignored=0   
```
## Задание 6,7,8

Чтобы поверить идемпотентность, решил поднять новый серевер. Все прошло без проблем.
