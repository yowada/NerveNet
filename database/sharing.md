### Sharing data using hearsayif-publish
###### hearsayif-publish is a nervenet api to write into the database
> Data must be written into the database using hearsayif-publish

1. First we need to prepare a file to share. The manual follows a format mentioned in the share.txt whose contents are  
```
<record>
<table>shbt_boxshare</table>
<id_box>414243</id_box>
<body>granbelm</body>
</record>
```
The format should have a root node **\<record>** with child node **\<table>** specifying which table to **publish** into. The remaining child nodes are the data that follow the schema of the table <br>

2. The following command is used to send messages in BS.<br>
```
cat share.txt | docker exec -i tsg /writable/sbin/hearsayif-publish
```

3. The output of the command should be **status=COMPLETE**<br>
![hearsayif-publish](/database/assets/images/hearsayif-publish.jpg)

> hearsayif-publish also sends push notifications to the remote nodes about DB has been updated. Based on this push notification event, remote nodes try to synchronize the database. Without using this API, all nodes synchronize their database periodically. It takes more than a minute.

