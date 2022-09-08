NoSql Databases (MongoDB, Cassandra, ElasticSearch)



### Table of Contents </br>
[Основные термины ElasticSearch](#es_decription) </br>
[Основные операции с ElasticSearch](#es_operations) </br>
[Конфигурационный файл ElasticSearch](#es_config_file)</br>
[Изменение информации о «здоровье кластера» одиночного экземпляра ElasticSearch](#es_Health_set_to_Green_standalone_server)</br>


---

### Основные термины ElasticSearch <a name=es_decription></a></br>
</br>

### Основные операции с ElasticSearch <a name=es_operations></a></br>

Получение всей информацию о состоянии кластера</br>
```
curl -XGET 'http://localhost:9200/_cluster/stats?human&pretty'
```
Компактный вывод информации о «здоровье» кластера</br>
```
curl http://localhost:9200/_cluster/health/
```

Получение индексов</br>
```
curl 'http://localhost:9200/_cat/indicies?v'
```


delete indicies</br>
elasticsearch-curator</br>

### Конфигурационный файл ElasticSearch <a name=es_config_file></a></br>



### Изменение информации о «здоровье кластера» одиночного экземпляра ElasticSearch <a name=es_Health_set_to_Green_standalone_server></a></br>
Для одиночного экземпляра ElasticSearch информация о «здоровье кластера» будет отображаться как YELLOW из-за отстутствия реплик.
Для переключения из состояни YELLOW в состояние Green необходимо выполнить следующий запрос </br>
```
$ curl -XPUT 'http://localhost:9200/_settings' -H 'Content-Type: application/json' -d '{ "index" : { "number_of_replicas" : 0 } }'

{"acknowledged":true}
```

