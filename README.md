# Elasticsearch workshop

## Spuštění v Dockeru

Možné potíže:
* max_map_count
  * v Linuxu může vyžadovat nastavení `sysctl -w vm.max_map_count=262144`
  * [Řešení pro WSL2](https://github.com/docker/for-win/issues/5202) (Docker Desktop for Windows) `wsl -d docker-desktop sysctl -w vm.max_map_count=262144` 
* dostatek místa na disku - maximální zaplnění disku na 85%, jinak se postupně automaticky degraduje funkčnost clusteru až do režimu read-only (na 95% zaplnění)
* dostatek RAM (aspoň 1GB na každý node)
* pokud narazíte na potíže, zkuste vypnout (CTRL-C) a zase nastartovat docker-compose


### Spuštění clusteru (pro > 8GB RAM):

```
cd elastic-cluster
docker-compose up
```

Docker-compose obsahuje:
* 3 uzly clusteru Elasticsearch
* Kibana
* Cerebro

### Spuštění jednoho nodu (pro <= 8GB RAM):

```
cd elastic-single
docker-compose up
```

Docker-compose obsahuje:
* 1 uzel Elasticsearch
* Kibana
* Cerebro


## Vytvoření indexu a naplnění dat pomocí HTTP REST API

https://www.ludekvesely.cz/serial-elasticsearch-5-pokrocile-fulltextove-vyhledavani/

* nastavit např. 1 shard a 1 repliku (tedy celkem bude index uložen na nodech 2x)
* naplnění indexu daty pomocí HTTP PUT
  * po jednom dokumentu
  * bulk
* ukázka alokace shardů a replik na nodech clusteru v Cerebru  

## Vytvoření indexu a naplnění dat importem v Kibaně

* Ukázkové datasety: Menu - Home - Add data - Sample data
  * Sample eCommerce orders
  * Sample web logs
* Import dat v CSV, NDJSON: Menu - Home - Upload file
  * Pozor na velikost
  * NDJSON musí mít atributy v klasických úvozovkách

## Vyhledávání

https://www.ludekvesely.cz/serial-elasticsearch-4-fulltextove-vyhledavani-v-cestine/

https://www.ludekvesely.cz/serial-elasticsearch-5-pokrocile-fulltextove-vyhledavani/

* [doinstalovat ICU folding](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-icu.html)
```
docker-compose exec es01 bin/elasticsearch-plugin install analysis-icu
docker-compose exec es02 bin/elasticsearch-plugin install analysis-icu
docker-compose exec es03 bin/elasticsearch-plugin install analysis-icu
```
* [doinstalovat Hunspell CS slovnik](https://stackoverflow.com/questions/37168208/hunspell-for-elasticsearch)

```
docker-compose exec es01 mkdir -p config/hunspell/cs_CZ
docker-compose exec es01 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.aff -o config/hunspell/cs_CZ/cs_CZ.aff
docker-compose exec es01 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.dic -o config/hunspell/cs_CZ/cs_CZ.dic
docker-compose exec es01 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/settings.yml -o config/hunspell/cs_CZ/settings.yml

docker-compose exec es02 mkdir -p config/hunspell/cs_CZ
docker-compose exec es02 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.aff -o config/hunspell/cs_CZ/cs_CZ.aff
docker-compose exec es02 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.dic -o config/hunspell/cs_CZ/cs_CZ.dic
docker-compose exec es02 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/settings.yml -o config/hunspell/cs_CZ/settings.yml

docker-compose exec es03 mkdir -p config/hunspell/cs_CZ
docker-compose exec es03 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.aff -o config/hunspell/cs_CZ/cs_CZ.aff
docker-compose exec es03 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/cs_CZ.dic -o config/hunspell/cs_CZ/cs_CZ.dic
docker-compose exec es03 curl https://raw.githubusercontent.com/ludekvesely/elasticsearch-cz/master/hunspell/cs_CZ/settings.yml -o config/hunspell/cs_CZ/settings.yml
```
* nezapomenout restartovat server(y)
* princip analyzérů a vyhledávání pomocí nich
* [vytvořit index s nastavenými fulltext analyzéry](PUT_products.txt)
* naplnit data z [NDJSON](data.ndjson)
* dotaz pro fulltextové vyhledáváná produktů
* dotaz pro vyhledávání podle ceny
* analytické dotazy využívající agregace

