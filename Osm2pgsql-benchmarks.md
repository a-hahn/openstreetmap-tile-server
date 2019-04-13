# Europe import on Docker

This is a sample benchmark following [OSM Benchmark Examples](https://wiki.openstreetmap.org/wiki/Osm2pgsql/benchmarks)

* Import done in August 2018

* Hardware
    * Thomas Krenn Server Single CPU (2016)
    * Intel Xeon E3-1220 v5 4-Core 3,0GHz 8MB 8GT/s
    * 64 GB (4x 16GB) ECC DDR4 2133 RAM 2 Rank
    * 1000 GB SATA III SSD 2,5"
    
* Total import size **483GB** (fails on 500GB drive)

## Tuning

Using *postgresql.conf* from the sample

```
shared_buffers = 14GB
work_mem = 1GB                          
maintenance_work_mem = 8GB

effective_io_concurrency = 500          
max_worker_processes = 8                
max_parallel_workers_per_gather = 2     
max_parallel_workers = 8                

checkpoint_timeout = 1h         
max_wal_size = 5GB
min_wal_size = 1GB
checkpoint_completion_target = 0.9      

random_page_cost = 1.1                  
min_parallel_table_scan_size = 8MB
min_parallel_index_scan_size = 512kB
effective_cache_size = 22GB

```

This additional config file will override the defaults when copied to the same folder as the *.osm.pbf files.
(Postgres will parse any files that end with *.conf) and mounted as a volume for */etc/postgresql/10/main/conf.d*

```
sudo docker run -v /home/ubuntu/osm:/etc/postgresql/10/main/conf.d -v /home/ubuntu/osm/europe-latest.osm.pbf:/data.osm.pbf -v openstreetmap-data:/var/lib/postgresql/10/main openstreetmap-a/hahn-tile-server import
```

## Tuning t.b.d

Not yet checked the other tuning options as in this example

```
 osm2pgsql -c -E 3857 -p planet_osm_hstore -d gisdb -H /var/run/postgresql/ -C 28000 -G --hstore  --style /projects/openstreetmap-carto-de/hstore-only.style --tag-transform-script /projects/openstreetmap-carto-de/openstreetmap-carto.lua --number-processes 8  --tablespace-main-data ssd2 --tablespace-main-index ssd1 --tablespace-slim-data ssd2 --tablespace-slim-index ssd1 --flat-nodes /projects/nodecache/node.cache  --slim  /projects/downloads/planet-latest.osm.pbf
```

**NOTE** This is just an example from the referenced benchmark esp. *C 28000* might have an siginificant impact on import speed.
Europe import took a total of ~ **18 hours**

## Console output

```
ubuntu@tk1:~$ sudo docker run -v /home/ubuntu/osm:/etc/postgresql/10/main/conf.d -v /home/ubuntu/osm/europe-latest.osm.pbf:/data.osm.pbf -v openstreetmap-data:/var/lib/postgresql/10/main openstreetmap-a/hahn-tile-server import
 * Starting PostgreSQL 10 database server
   ...done.
createuser: creation of new role failed: ERROR:  role "renderer" already exists
createdb: database creation failed: ERROR:  database "gis" already exists
ERROR:  extension "postgis" already exists
ERROR:  extension "hstore" already exists
ALTER TABLE
ALTER TABLE
osm2pgsql version 0.96.0 (64 bit id space)

Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua
Using projection SRS 3857 (Spherical Mercator)
Setting up table: planet_osm_point
Setting up table: planet_osm_line
Setting up table: planet_osm_polygon
Setting up table: planet_osm_roads
Allocating memory for dense node cache
Allocating dense node cache in one big chunk
Allocating memory for sparse node cache
Sharing dense sparse
Node-cache: cache=2048MB, maxblocks=32768*65536, allocation method=11
Mid: pgsql, cache=2048
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels

Reading in file: /data.osm.pbf
Using PBF parser.
Processing: Node(2127685k 257.0k/s) Way(258523k 5.74k/s) Relation(4078970 218.52/s)  parse time: 71989s
Node stats: total(2127685951), max(5830570214) in 8280s
Way stats: total(258523395), max(616760012) in 45043s
Relation stats: total(4079049), max(8529566) in 18666s
Committing transaction for planet_osm_point
Committing transaction for planet_osm_line
Committing transaction for planet_osm_polygon
Committing transaction for planet_osm_roads
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels
Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels
Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels
Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels
Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua

Going over pending ways...
        186158479 ways are pending

Using 4 helper-processes
Finished processing 186158479 ways in 20618 s

186158479 Pending ways took 20618s at a rate of 9028.93/s
Committing transaction for planet_osm_point
Committing transaction for planet_osm_line
Committing transaction for planet_osm_polygon
Committing transaction for planet_osm_roads
Committing transaction for planet_osm_point
Committing transaction for planet_osm_line
Committing transaction for planet_osm_polygon
Committing transaction for planet_osm_roads
Committing transaction for planet_osm_point
Committing transaction for planet_osm_line
Committing transaction for planet_osm_polygon
Committing transaction for planet_osm_roads
Committing transaction for planet_osm_point
Committing transaction for planet_osm_line
Committing transaction for planet_osm_polygon
Committing transaction for planet_osm_roads

Going over pending relations...
        0 relations are pending

Using 4 helper-processes
Finished processing 0 relations in 0 s

Committing transaction for planet_osm_point
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_line
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_polygon
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_roads
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_point
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_line
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_polygon
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_roads
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_point
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_line
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_polygon
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_roads
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_point
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_line
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_polygon
WARNING:  there is no transaction in progress
Committing transaction for planet_osm_roads
WARNING:  there is no transaction in progress
node cache: stored: 182725423(8.59%), storage efficiency: 68.07% (dense blocks: 13891, sparse nodes: 77321641), hit rate: 10.21%
Sorting data and creating indexes for planet_osm_roads
Sorting data and creating indexes for planet_osm_polygon
Sorting data and creating indexes for planet_osm_line
Sorting data and creating indexes for planet_osm_point
NOTICE:  Self-intersection at or near point 587312.94433636917 6271092.4624617016
Copying planet_osm_roads to cluster by geometry finished
Creating geometry index on planet_osm_roads
Creating osm_id index on planet_osm_roads
Creating indexes on planet_osm_roads finished
All indexes on planet_osm_roads created in 288s
Completed planet_osm_roads
Stopping table: planet_osm_nodes
Stopped table: planet_osm_nodes in 0s
Stopping table: planet_osm_ways
Building index on table: planet_osm_ways
Copying planet_osm_point to cluster by geometry finished
Creating geometry index on planet_osm_point
NOTICE:  Self-intersection at or near point 1659220.8248663542 7993093.1167870089
Copying planet_osm_line to cluster by geometry finished
Creating geometry index on planet_osm_line
Creating osm_id index on planet_osm_point
Creating indexes on planet_osm_point finished
All indexes on planet_osm_point created in 2387s
Completed planet_osm_point
Stopping table: planet_osm_rels
Building index on table: planet_osm_rels
NOTICE:  Self-intersection at or near point -506086.5913292435 5280209.9148441646
Stopped table: planet_osm_rels in 147s
Creating osm_id index on planet_osm_line
Creating indexes on planet_osm_line finished
All indexes on planet_osm_line created in 3423s
Completed planet_osm_line
NOTICE:  Self-intersection at or near point 530333.30102692614 5918886.5543764178
NOTICE:  Self-intersection at or near point 587312.91705696541 6271092.4234429011
Copying planet_osm_polygon to cluster by geometry finished
Creating geometry index on planet_osm_polygon
Creating osm_id index on planet_osm_polygon
Creating indexes on planet_osm_polygon finished
All indexes on planet_osm_polygon created in 13969s
Completed planet_osm_polygon
Stopped table: planet_osm_ways in 15772s

Osm2pgsql took 108673s overall

```

## Console Output AMD Threadripper 16 core 128 GB

```
ubuntu@ls01:~/osm$ sudo docker run --shm-size 14g -e THREADS=24 -v /home/ubuntu/osm/europe-latest.osm.pbf:/data.osm.pbf -v openstreetmap-test-data:/var/li
b/postgresql/10/main a-hahn/openstreetmap-tile-server import
[sudo] password for ubuntu:
 * Starting PostgreSQL 10 database server
   ...done.
createuser: creation of new role failed: ERROR:  role "renderer" already exists
createdb: database creation failed: ERROR:  database "gis" already exists
ERROR:  extension "postgis" already exists
ERROR:  extension "hstore" already exists
ALTER TABLE
ALTER TABLE
osm2pgsql version 0.96.0 (64 bit id space)

Allocating memory for dense node cache
Allocating dense node cache in one big chunk
Allocating memory for sparse node cache
Sharing dense sparse
Node-cache: cache=2048MB, maxblocks=32768*65536, allocation method=11
Mid: pgsql, cache=2048
Setting up table: planet_osm_nodes
Setting up table: planet_osm_ways
Setting up table: planet_osm_rels
Using lua based tag processing pipeline with script /home/renderer/src/openstreetmap-carto/openstreetmap-carto.lua
Using projection SRS 3857 (Spherical Mercator)
Setting up table: planet_osm_point
Setting up table: planet_osm_line
Setting up table: planet_osm_polygon
Setting up table: planet_osm_roads

Reading in file: /data.osm.pbf
Using PBF parser.
Processing: Node(2127685k 376.5k/s) Way(258523k 7.53k/s) Relation(4078630 552.59/s)  parse time: 47355s
Node stats: total(2127685951), max(5830570214) in 5651s
Way stats: total(258523395), max(616760012) in 34323s
Relation stats: total(4079049), max(8529566) in 7381s
Sorting data and creating indexes for planet_osm_point
Sorting data and creating indexes for planet_osm_polygon
Sorting data and creating indexes for planet_osm_roads
Sorting data and creating indexes for planet_osm_line
Stopping table: planet_osm_nodes
Stopping table: planet_osm_ways
Stopping table: planet_osm_rels
Stopped table: planet_osm_nodes in 0s
Building index on table: planet_osm_rels
Building index on table: planet_osm_ways
Copying planet_osm_roads to cluster by geometry finished
Creating geometry index on planet_osm_roads
Stopped table: planet_osm_rels in 702s
Creating osm_id index on planet_osm_roads
Creating indexes on planet_osm_roads finished
All indexes on planet_osm_roads created in 849s
Completed planet_osm_roads
Copying planet_osm_point to cluster by geometry finished
Creating geometry index on planet_osm_point
Copying planet_osm_line to cluster by geometry finished
Creating geometry index on planet_osm_line
Creating osm_id index on planet_osm_point
Creating indexes on planet_osm_point finished
All indexes on planet_osm_point created in 3313s
Completed planet_osm_point
Creating osm_id index on planet_osm_line
Creating indexes on planet_osm_line finished
All indexes on planet_osm_line created in 4462s
Completed planet_osm_line
Copying planet_osm_polygon to cluster by geometry finished
Creating geometry index on planet_osm_polygon
Creating osm_id index on planet_osm_polygon
Creating indexes on planet_osm_polygon finished
All indexes on planet_osm_polygon created in 16500s
Completed planet_osm_polygon
Stopped table: planet_osm_ways in 18584s

Osm2pgsql took 65941s overall
node cache: stored: 182725423(8.59%), storage efficiency: 68.07% (dense blocks: 13891, sparse nodes: 77321641), hit rate: 10.20%
ubuntu@ls01:~/osm$ 
```
