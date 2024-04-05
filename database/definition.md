Some problem when creating table definition

### UNIQUE

In order to implement a field to be unique, write "unique" constrain in the dbtype field. E.g.
```xml
<column id="0xff08" label="some_labels" name="some_column name"
     type="int" size="8"
     dbtype="BIGINT NOT NULL UNIQUE" />
```
UNIQUE  constraint is guaranteed within the same host, though, if the distributed nodes are disconnected and the same value is inserted, the synchronization is never finished because the two records with the same value in a unique constraint column never synchronize. Because of this reason, a unique constraint is not useful in the case of distributed database usage I think.
