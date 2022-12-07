# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению

1. (Необязательно) Изучите, что такое [clickhouse](https://www.youtube.com/watch?v=fjTNS2zkeBs) и [vector](https://www.youtube.com/watch?v=CgEhyffisLY)
2. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.
4. Подготовьте хосты в соответствии с группами из предподготовленного playbook.

## Основная часть

1. Приготовьте свой собственный inventory файл `prod.yml`.

[Link](./playbook/inventory/prod.yml)

2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev).

[Link](./playbook/site.yml)

3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.

Vector ставил по аналогии с Clickhouse из RPM пакета. 

4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, установить vector.

в [vars.yml](./playbook/group_vars/vector/vars.yml) добавлена переменная `vector_version` 

5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

```bash
root@vkvm:/home/vk/DZ8.2/playbook# ansible-lint site.yml 
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
```
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.

```bash
root@vkvm:/home/vk/DZ8.2/playbook# ansible-playbook --check -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] **********************************************************************************

TASK [Gathering Facts] *************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib - 1] **************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib - 2] **************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *************************************************************************
fatal: [clickhouse-01]: FAILED! => {"changed": false, "msg": "No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system", "rc": 127, "results": ["No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system"]}

PLAY RECAP *************************************************************************************************
clickhouse-01              : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=1    ignored=0   
```

7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

```bash
root@vkvm:/home/vk/DZ8.2/playbook# ansible-playbook --diff -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] **********************************************************************************

TASK [Gathering Facts] *************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib - 1] **************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib - 2] **************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *************************************************************************
changed: [clickhouse-01]

TASK [Clickhouse-Server Start] *****************************************************************************
[WARNING]: Consider using 'become', 'become_method', and 'become_user' rather than running sudo
changed: [clickhouse-01]

TASK [Create database] *************************************************************************************
changed: [clickhouse-01]

RUNNING HANDLER [Start clickhouse service] *****************************************************************
changed: [clickhouse-01]

PLAY [Install vector] **************************************************************************************

TASK [Gathering Facts] *************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] **********************************************************************************
changed: [vector-01]

TASK [Install vector packages] *****************************************************************************
changed: [vector-01]

RUNNING HANDLER [Start vector service] *********************************************************************
fatal: [vector-01]: FAILED! => {"changed": false, "msg": "Could not find the requested service vector: host"}

NO MORE HOSTS LEFT *****************************************************************************************

PLAY RECAP *************************************************************************************************
clickhouse-01              : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
vector-01                  : ok=3    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
```
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

```bash
root@vkvm:/home/vk/DZ8.2/playbook# ansible-playbook --diff -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] **********************************************************************************

TASK [Gathering Facts] *************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib - 1] **************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib - 2] **************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *************************************************************************
ok: [clickhouse-01]

TASK [Clickhouse-Server Start] *****************************************************************************
[WARNING]: Consider using 'become', 'become_method', and 'become_user' rather than running sudo
changed: [clickhouse-01]

TASK [Create database] *************************************************************************************
ok: [clickhouse-01]

PLAY [Install vector] **************************************************************************************

TASK [Gathering Facts] *************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] **********************************************************************************
ok: [vector-01]

TASK [Install vector packages] *****************************************************************************
ok: [vector-01]

PLAY RECAP *************************************************************************************************
clickhouse-01              : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

[Link](./playbook/Info.md)

10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
