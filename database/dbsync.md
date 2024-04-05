### Introduction: Database Syncing in Nervenet mesh network

This document follows the steps in the manual for database syncing mentioned in [Database syncing](/database/assets/docs/DB-Sync_manual.docx)

#### 1. Standby at reciever

As the document mentions, we are going to migrate the state of BS to reciever mode.  
The current BS I am using for this step is BS3
The manual requires us to run a command  
```bash
docker exec -it tsg /writable/sbin/hearsayif-monitor shbt_boxshare
```
Where tsg is the container name<br>
shbt_boxshare is the table name<br>
Since the container name in my node is different I am going to run<br>

```bash
docker exec -it nervenet-tsg-sqlite /writable/sbin/hearsayif-monitor shbt_boxshare
```
The output of the above command is
<br>
![hearsay-ifmonitor output](/database/assets/images/hearsayif-monitor.jpg)<br>

#### 2. Send record on the reciever

First we need to prepare a file to share. The manual follows a format mentioned in the share.txt whose contents are  
```
<record>
<table>shbt_boxshare</table>
<id_box>414243</id_box>
<body>granbelm</body>
</record>
```
We are going to send with hearsayif-publish tool<br>
I am going to use BS2 for sending.<br>
The following command is used to send messages in BS.<br>
```
# replacing tsg with nervenet-tsg-sqlite
cat share.txt | docker exec -i tsg /writable/sbin/hearsayif-publish
```
The output of the command is<br>
![hearsayif-publish](/database/assets/images/hearsayif-publish.jpg)
> As we can see that the status is COMPLETE

We can confirm that the message has been sent<br>
Now we need to check the reciever BS.<br>
The state of the reciever BS is updated and it is<br>
![hearsayif_reciever](/database/assets/images/hearsayif-output.jpg)

#### Checking the record on the recieving BS

1. We are going to run sqlite to browse the records<br>
```
sqlite3 /var/tmp/hearsayd.sqlite3
```
2. To browse the records in the shbt_boxshare table, run the SQL command from the sqlite3 tool prompt
```
select body, hex(id_box), hex(id_node_update), hex(id_record), time_discard, time_update,  timestamp_sync  from  shbt_boxshare;
```
3. The output of the above command is<br>
```
granbelm|414243|02220001|0188A8A1230402220001|1686542712580|1686456312580|2023-06-11 04:05:34
```
![sqlite 3 output](/database/assets/images/sqlite3_BS.jpg)
4. This proves that the record has been transfered

### Writing record on the sender BS
In this step we directly write the record on the sender BS and check if the reciever BS is also updated.<br>

1. Connect to the sqlite database
```
sqlite3 /var/tmp/hearsayd.sqlite3
```

2. Write a record into the database. The rules for writing the properties associated with time records are<br>
- The properties for time discard and time update are in epoch time
- We can get the current epoch time by running the following command
```bash
date '+%s'
```
and multiplying it by 1000 (epoch in milliseconds)
- The time discard should be later than time update
- The following is a simple example
```
insert into shbt_boxshare  (body,  id_box,  id_node_update,  id_record,  time_discard, time_update ) VALUES ('white lily', 'ABC', x'021e', x'0123456789abcdef',1686683163000, 1686583163000);
```
3. Check the record using the following command
```
SELECT * FROM shbt_boxshare
```
![sender](/database/assets/images/sqlite3_write.jpg)

4. The sender DB has been updated

5. Now we can simply check it in the recieving BS using hearsayif-monitor tool
```
docker exec -it nervenet-tsg-sqlite /writable/sbin/hearsayif-monitor shbt_boxshare
```
The reciever db at standby mode has been updated<br>
![](/database/assets/images/hearsayif-monitor-direct.jpg)
