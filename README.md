# Laboratory work number 8

## Downloading ready-made images 

1. ```docker pull ubuntu```

2. ```docker pull postgres```

## Based on postgresql:latest, create a new image and run it in a container where the database password is extracted from a file, a database with Your name and a user named name_user is created, and recovery from the dump is performed

1. ```touch Dockerfile```

2. ```ls Dockerfile```

3. ```vim Dockerfile``` (Dockerfile inside)

4. ```docker build -t my . ``` (my this isname of my image) building our new image

5. ```docker images```

6. ```docker run -d my```

7. ```docker ps``` we look at whether the container works if so we go further

8. ```docker exec -it container_id bash``` Go inside your container

9. ```psql -h localhos -p 5432 -U postgres -W``` Go to your local host

10. ```\l```

11. ```CREATE DATABASE kate; ``` kate this is the name of the new database

12. ```\l ``` we can see that our database has appeared

13. ```\c name_base ``` switching between databases

14. ```CREATE USER name WITH PASSWORD 'password'```

15. ```CREATR TABLE name_table (ID int primary ket,Name text); ``` creating a table with fields ID,Name

16. ```\d  name_table ```

17. ```insert into name_table values (1, 'Text');``` inserting data into the table

18. ```\q```

19. ```exit```

20. ```docker exec -t container_id pg_dumpall -c -U postgres > dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql ``` making a dump


## Perform operations for inserting and extracting data from the  database without entering the container

The function should work ```docker exec [OPTIONS] CONTAINER COMMAND [ARG...] ```but for some reason this does not work, although everything works for the dump

## Customization docker-compose

1. ```nano docker-compose.yml ```

2. ```Vim docker-compose.yml ``` the file inside

3. ```docker-compose up –d ```








