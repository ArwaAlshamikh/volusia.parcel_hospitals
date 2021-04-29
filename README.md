# volusia.parcel_hospitals
The problem I picked is how to create parcel_hospitals table and how to find the neareset hospital to a random parcel and the hospital name. 

The zipped file has one file which is the exracted data from parcel_hospital table.

Load table (download zip file in repository, extract to c:\temp\cs540)

COPY volusia.parcel_hospitals from 'C:\temp\cs540\parcel_hospital.txt' WITH (FORMAT 'csv', DELIMITER E'\t', NULL '', HEADER);

To find more about the problem and the way I solved it see the PDF I have uploaded. 

https://github.com/ArwaAlshamikh/volusia.parcel_hospitals/blob/main/Student%20presentation%20(hospitals).pdf
