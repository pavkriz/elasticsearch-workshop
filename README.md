# Elasticsearch workshop

## Spuštění v Dockeru

Spuštění cluteru:

`docker-compose up`

Docker-compose obsahuje:
* 3 uzly clusteru
* Kibana
* Cerebro

Možné potíže:
* v Linuxu může vyžadovat nastavení `sysctl -w vm.max_map_count=262144`
* dostatek místa na disku - maximální zaplnění disku na 85%, jinak se postupně automaticky degraduje funkčnost clusteru až do režimu read-only (na 95% zaplnění)
* dostatek RAM (aspoň 1GB na každý node)
* pokud narazíte na potíže, zkuste vypnout (CTRL-C) a zase nastartovat docker-compose

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

https://www.ludekvesely.cz/serial-elasticsearch-5-pokrocile-fulltextove-vyhledavani/

* princip analyzérů a vyhledávání pomocí nich
* dotaz pro fulltextové vyhledáváná produktů
* dotaz pro vyhledávání podle ceny
* analytické dotazy využívající agregace

