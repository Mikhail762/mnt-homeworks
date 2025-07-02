#######MASTER#######
Добавить в файл `/var/lib/docker/volumes/cts_postgres_data/_data/pg_hba.conf`
```
host    replication    postgres    REPLICA_IP/32    md5
host    replication    postgres    MASTER_DOCKER_NET/24    md5
```
Добавить в файл `/opt/express/.cts/postgres/postgresql.conf`
```
listen_addresses = '*'

wal_level = hot_standby
archive_mode = on
archive_command = 'cd .'
max_wal_senders = 8
hot_standby = on
```
Рестарт контейнера - практически не аффектит на юзеров.
```
docker restart cts-postgres-1
```

#######REPLICA######
docker stop cts-postgres-1

Добавить в файл `/var/lib/docker/volumes/cts_postgres_data/_data/pg_hba.conf`
```
host    replication    postgres    MASTER_ВНУТРЕННИЙ_IP/32    md5
```

Добавляем в `/opt/express/.cts/postgres/postgresql.conf`
```
listen_addresses = '*'
wal_level = hot_standby
archive_mode = on
archive_command = 'cd .'
max_wal_senders = 8
hot_standby = on
```

Запустить временный контейнер postgres, внутри удалить содержимое папки `/var/lib/postgresql/data/`, после чего выйти из контейнера
```
docker run -it -v cts_postgres_data:/var/lib/postgresql/data registry.public.express/postgres:14.8 /bin/bash

cd /var/lib/postgresql/data/

rm -rf *

su postgres

pg_basebackup -P -R -X stream -c fast -h 95.217.4.87 -U postgres -D /var/lib/postgresql/data/

exit
exit
```
После выхода их контейнера он будет в статусе Exited(0), можно его удалить.


Рестарт основного контейнера postgres на реплике.
```
docker restart cts-postgres-1
```


Таким образом мы настроили репликацию постгрес и ожидаем переключения EXPRESS.
Когда необходимо сделать переключение сначала выполняем остановку сервисов на фронте и беке.
BACK/FRONT
```
cd /opt/express/ && DPL_PULL_POLICY=never dpl --dc stop
```

РЕПЛИКУ переводим в автономный режим
```
docker exec -it cts-postgres-1 bash

su postgres

pg_ctl promote -D /var/lib/postgresql/data/
```
Теперь реплика автономно работает и может принмать запросы на внесение изменений в базу.
