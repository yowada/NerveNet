### Creating a custom table in the nervenet database
1. In order to create a custom table i.e. if your data cannot be trasffered using shbt-boxshare, we can create the table in docker conatiner **nervenet-tsg-sqlite**
2. Access the nervenet-tsg-sqlite containter using the following command
```
docker exec -it nervenet-tsg-sqlite /bin/sh
```
3. Create a custom table using xml definition in the location **"/writable/personal/tables.d/"**.<br>
Other table definition can be found in **/writable/refconf/tables.d/** directory
5. After saving the file, you can exit the nervenet-tsg-sqlite container.
```
exit
```
6. Then you can restart the nervenet-tsg-sqlite container
```
docker restart nervenet-tsg-sqlite
```
7. Then view the existing tables in the file [**/var/tmp/hearsayd.sqlite3**](#viewing-tables-in-the-nervenet-database)
