docker pull ubuntu
docker pull postgres
touch Dockerfile
ls Dockerfile
vim Dockerfile (Dockerfile inside)
docker build -t my . (my this isname of my image) building our new image
docker images
docker run -d my
docker ps   we look at whether the container works if so we go further
docker exec -it container_id bash  Go inside your container
psql -h localhos -p 5432 -U postgres -W  Go to your local host
\l
CREATE DATABASE kate; (kate this is the name of the new database)
\l we can see that our database has appeared
\ñ name_base  switching between databases
CREATE USER name WITH PASSWORD 'password'
CREATR TABLE name_table (ID int primary ket,Name text); creating a table with fields ID,Name
\d  name_table 
insert into name_table values (1, 'Text');  inserting data into the table
\q
exit 
docker exec -t container_id pg_dumpall -c -U postgres > dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql   making a dump

3) perform operations for inserting and extracting data from the database without entering the container
The function should work docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
but for some reason this does not work, although everything works for the dump

Vim docker-compose.yml the file inside
docker-compose up –d 








