# volusia.parcel_hospitals
The problem I picked is how to create parcel_hospitals table and how to find the neareset hospital to a random parcel and the hospital name. 

-- The hospitals in the county have luc of 5800, 7300
select * from volusia.parcel where luc in '8500','7300';

--Add the geometry column to the parcel table 
SELECT AddGeometryColumn ('volusia','parcel','geom',2236,'MULTIPOLYGON',2);

update volusia.parcel a set geom = p.geom from volusia.gis_parcels p where a.parid=p.altkey;

--Create gis_hospital  
select parid, geom into volusia.gis_hospitals from volusia.parcel where luc in '8500','7300';

--Create parcel_hospitals and add the data from parcel and owner tables
drop table if exists volusia.parcel_hospitals;

select p.parid, p.luc, p.luc_desc, o.own1 as hospital_name into volusia.parcel_hospitals
from volusia.owner o join volusia.parcel p on o.parid=p.parid where p.luc in ('8500','7300');

--Add hospitals name into gis_hospitals 
alter table volusia.gis_hospitals add column h_name text;

update volusia.gis_hospitals g set h_name = h.hospital_name 
from volusia.parcel_hospitals h where g.parid=h.parid;
 
--The closest hospital to a random parcel 

select p.parid, p.geom, h.hospital_name, ST_Distance(p.geom, (select p2.geom from volusia.parcel p2 where p2.parid=4841709))/5280 as hdistance  
from volusia.parcel p join volusia.parcel_hospitals h on p.parid=h.parid 
where p.luc in ('8500','7300') 
order by p.geom <-> (select p2.geom from volusia.parcel p2 where p2.parid=4841709) limit 1;

--Add hdistance to the parcel table to find the distance from each parcel to the closest hospital 
alter table volusia.parcel add column hdistance double precision;

--Create Index
create index idx_parcel_luc on volusia.parcel (luc);
create index idx_parcel on volusia.parcel (parid);

CREATE INDEX parcel_geom_idx
  ON volusia.parcel
  USING GIST (geom);

vacuum analyze volusia.parcel;

#Showing the hospital data in QGIS as a layer...

![image](https://user-images.githubusercontent.com/82989740/117295910-aa604b00-ae31-11eb-9786-8625c3ae1209.png)



The zipped file has two files which are the extracted data from the parcel_hospitals table and the (hospital_distance, parid) from gis_hospital table.

Load table (download zip file in repository, extract to c:\temp\cs540)

COPY volusia.parcel_hospitals from 'C:\temp\cs540\parcel_hospital.txt' WITH (FORMAT 'csv', DELIMITER E'\t', NULL '', HEADER);

To find more about the problem and the way I solved it see the PDF I have uploaded. 

https://github.com/ArwaAlshamikh/volusia.parcel_hospitals/blob/main/Student%20presentation%20(hospitals).pdf
