## Установка `clickhouse` и `vector`

Данный playbook устанавливает `clichouse` и `vector` на соответствующие контейнеры `CentOS7` поднятые при помощи `docker-compose`, запускает службу `clichouse-server` и `vector`, а также создает базу `logs` в `clichouse`. 

В каталоге `group_vars` задаются необходимые версии дистрибутивов.

Для работы playbook необходимо:
 - запустить `docker-compose`
```shell
docker-compose up
```
 - запустить `ansible-playbook`:
```shell
ansible-playbook -i inventory/prod.yml site.yml
```