## Установка `clickhouse` и `vector`

Данный playbook:
- устанавливает `clichouse` и `vector` на соответствующие контейнеры CentOS 7 поднятые при помощи `docker-compose`
- запускает службы `clichouse-server` и `vector`
- создает базу `logs` в `clickhouse`. 

В каталоге `group_vars` задаются необходимые версии дистрибутивов.

Для работы playbook делаем следующее:
 - запускаем `docker-compose`
```bash
docker-compose up
```
 - запускаем `ansible-playbook`:
```bash
ansible-playbook -i inventory/prod.yml site.yml
```
