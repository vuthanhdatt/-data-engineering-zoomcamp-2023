## Week 1:

Below are my answers for data-engineering-zoomcamp [week 1 homework](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/cohorts/2023/week_1_docker_sql/homework.md)
****

Q1: Which tag has the following text? - *Write the image ID to the file*

Run `docker build --help` we'll see the option `--iidfile string` has *Write the image ID to the file* description

```
vuthanhdatt@ubuntu:~/data-engineering-zoomcamp-2023$ docker build --help
Usage:  docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile
...
-f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm                Always remove intermediate containers
      --iidfile string          Write the image ID to the file
```

Answer: --iidfile string 
***
Q2: How many python packages/modules are installed in python:3.9 image?

Run `docker run -it python:3.9 bash`, we will get into container with python 3.9 image with bash entrypoint. From here we can use `pip list` to list all installed python modules.

```
vuthanhdatt@ubuntu:~/data-engineering-zoomcamp-2023$ docker run -it python:3.9 bash
root@94cca1e295aa:/# pip list
Package    Version
---------- -------
pip        22.0.4
setuptools 58.1.0
wheel      0.38.4
```
Answer: 3
***

Q3: How many taxi trips were totally made on January 15?

```sql
SELECT COUNT(*)
FROM green_trip_data
WHERE DATE(lpep_pickup_datetime) = '2019-01-15' AND DATE(lpep_dropoff_datetime) = '2019-01-15';
-------
 count 
-------
 20530
(1 row)
```
Answer: 20530
***

Q4: Which was the day with the largest trip distance? Use the pick up time for your calculations.

```sql
SELECT DATE(lpep_pickup_datetime)
FROM green_trip_data
WHERE trip_distance = (SELECT MAX(trip_distance) FROM green_trip_data);
------------
    date    
------------
 2019-01-15
(1 row)
```
Answer: 2019-01-15
***

Q5: In 2019-01-01 how many trips had 2 and 3 passengers?

```sql
SELECT passenger_count, COUNT(*)
FROM green_trip_data
WHERE DATE(lpep_pickup_datetime) = '2019-01-01' 
GROUP BY passenger_count;
-------------------------
 passenger_count | count 
-----------------+-------
               0 |    21
               1 | 12415
               2 |  1282
               3 |   254
               4 |   129
               5 |   616
               6 |   273
(7 rows)
```
Answer: 2: 1282 ; 3: 254
***

Q6: For the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip? We want the name of the zone, not the id.

```sql
SELECT zdo.zone
FROM green_trip_data t
JOIN zones zpu
ON t.pulocationid = zpu.locationid
JOIN zones zdo
ON t.dolocationid = zdo.locationid
WHERE zpu.zone = 'Astoria'  AND tip_amount = (
      SELECT max(tip_amount) 
      FROM green_trip_data t
      JOIN zones zpu
      ON t.pulocationid = zpu.locationid
      JOIN zones zdo
      ON t.dolocationid = zdo.locationid
      WHERE zpu.zone = 'Astoria');
-------------------------------
             zone              
-------------------------------
 Long Island City/Queens Plaza
(1 row)
```
Answer: Long Island City/Queens Plaza