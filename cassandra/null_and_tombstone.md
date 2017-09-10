# Null and tombstone

Explicitly inserting null and updating value to null will create tombstone.

We create a table and insert some records.

```
create table profile (id integer, name text, primary key (id));
insert into profile (id) values (1);
insert into profile (id, name) values (2, null);
insert into profile (id, name) values (3, 'franz');
```

Now we turn on tracing mode and select each record.

```
tracing on;
```

```
select * from profile where id = 1;

 id | name
----+------
  1 | null

(1 rows)

Tracing session: ccfd4b40-95e9-11e7-b0cc-6d2c86545d91

 activity                                                          | timestamp                  | source     | source_elapsed
-------------------------------------------------------------------+----------------------------+------------+----------------
                                                Execute CQL3 query | 2017-09-10 05:35:05.716000 | 172.17.0.2 |              0
 Parsing select * from profile where id = 1; [SharedPool-Worker-1] | 2017-09-10 05:35:05.716000 | 172.17.0.2 |            466
                         Preparing statement [SharedPool-Worker-1] | 2017-09-10 05:35:05.717000 | 172.17.0.2 |            874
 Executing single-partition query on profile [SharedPool-Worker-3] | 2017-09-10 05:35:05.719000 | 172.17.0.2 |           2895
                Acquiring sstable references [SharedPool-Worker-3] | 2017-09-10 05:35:05.719000 | 172.17.0.2 |           3064
                   Merging memtable contents [SharedPool-Worker-3] | 2017-09-10 05:35:05.719000 | 172.17.0.2 |           3161
           Read 1 live and 0 tombstone cells [SharedPool-Worker-3] | 2017-09-10 05:35:05.719000 | 172.17.0.2 |           3586
                                                  Request complete | 2017-09-10 05:35:05.720809 | 172.17.0.2 |           4809
```

```
select * from profile where id = 2;

 id | name
----+------
  2 | null

(1 rows)

Tracing session: e780b560-95e9-11e7-b0cc-6d2c86545d91

 activity                                                          | timestamp                  | source     | source_elapsed
-------------------------------------------------------------------+----------------------------+------------+----------------
                                                Execute CQL3 query | 2017-09-10 05:35:50.198000 | 172.17.0.2 |              0
 Parsing select * from profile where id = 2; [SharedPool-Worker-1] | 2017-09-10 05:35:50.198000 | 172.17.0.2 |            305
                         Preparing statement [SharedPool-Worker-1] | 2017-09-10 05:35:50.198000 | 172.17.0.2 |            698
 Executing single-partition query on profile [SharedPool-Worker-2] | 2017-09-10 05:35:50.199000 | 172.17.0.2 |           1305
                Acquiring sstable references [SharedPool-Worker-2] | 2017-09-10 05:35:50.199000 | 172.17.0.2 |           1410
                   Merging memtable contents [SharedPool-Worker-2] | 2017-09-10 05:35:50.199000 | 172.17.0.2 |           1488
           Read 1 live and 1 tombstone cells [SharedPool-Worker-2] | 2017-09-10 05:35:50.199000 | 172.17.0.2 |           1874
                                                  Request complete | 2017-09-10 05:35:50.200295 | 172.17.0.2 |           2295
```

```
select * from profile where id = 3;

 id | name
----+-------
  3 | franz

(1 rows)

Tracing session: f142e0a0-95e9-11e7-b0cc-6d2c86545d91

 activity                                                          | timestamp                  | source     | source_elapsed
-------------------------------------------------------------------+----------------------------+------------+----------------
                                                Execute CQL3 query | 2017-09-10 05:36:06.570000 | 172.17.0.2 |              0
 Parsing select * from profile where id = 3; [SharedPool-Worker-1] | 2017-09-10 05:36:06.570000 | 172.17.0.2 |            386
                         Preparing statement [SharedPool-Worker-1] | 2017-09-10 05:36:06.570000 | 172.17.0.2 |            623
 Executing single-partition query on profile [SharedPool-Worker-2] | 2017-09-10 05:36:06.571000 | 172.17.0.2 |           1560
                Acquiring sstable references [SharedPool-Worker-2] | 2017-09-10 05:36:06.572000 | 172.17.0.2 |           1726
                   Merging memtable contents [SharedPool-Worker-2] | 2017-09-10 05:36:06.572000 | 172.17.0.2 |           1826
           Read 1 live and 0 tombstone cells [SharedPool-Worker-2] | 2017-09-10 05:36:06.572000 | 172.17.0.2 |           2386
                                                  Request complete | 2017-09-10 05:36:06.572703 | 172.17.0.2 |           2703
```

You can see there is 1 tombstone for record 2, because we insert a null value explicity. We should avoid creating tombstone for record creation.

So now, we try to set the name of record 3 to null.

```
update profile set name = null where id = 3;

Tracing session: 6c1dd5f0-95ea-11e7-b0cc-6d2c86545d91

 activity                                                                   | timestamp                  | source     | source_elapsed
----------------------------------------------------------------------------+----------------------------+------------+----------------
                                                         Execute CQL3 query | 2017-09-10 05:39:32.687000 | 172.17.0.2 |              0
 Parsing update profile set name = null where id = 3; [SharedPool-Worker-1] | 2017-09-10 05:39:32.688000 | 172.17.0.2 |            684
                                  Preparing statement [SharedPool-Worker-1] | 2017-09-10 05:39:32.688000 | 172.17.0.2 |           1143
                    Determining replicas for mutation [SharedPool-Worker-1] | 2017-09-10 05:39:32.690000 | 172.17.0.2 |           2430
                               Appending to commitlog [SharedPool-Worker-2] | 2017-09-10 05:39:32.690000 | 172.17.0.2 |           3207
                           Adding to profile memtable [SharedPool-Worker-2] | 2017-09-10 05:39:32.693000 | 172.17.0.2 |           5230
                                                           Request complete | 2017-09-10 05:39:32.693782 | 172.17.0.2 |           6782


cqlsh:myapp> select * from profile where id = 3;

 id | name
----+------
  3 | null

(1 rows)

Tracing session: 72b8d090-95ea-11e7-b0cc-6d2c86545d91

 activity                                                          | timestamp                  | source     | source_elapsed
-------------------------------------------------------------------+----------------------------+------------+----------------
                                                Execute CQL3 query | 2017-09-10 05:39:43.769000 | 172.17.0.2 |              0
 Parsing select * from profile where id = 3; [SharedPool-Worker-1] | 2017-09-10 05:39:43.769000 | 172.17.0.2 |            215
                         Preparing statement [SharedPool-Worker-1] | 2017-09-10 05:39:43.769000 | 172.17.0.2 |            394
 Executing single-partition query on profile [SharedPool-Worker-4] | 2017-09-10 05:39:43.770000 | 172.17.0.2 |           1008
                Acquiring sstable references [SharedPool-Worker-4] | 2017-09-10 05:39:43.770000 | 172.17.0.2 |           1081
                   Merging memtable contents [SharedPool-Worker-4] | 2017-09-10 05:39:43.770000 | 172.17.0.2 |           1165
           Read 1 live and 1 tombstone cells [SharedPool-Worker-4] | 2017-09-10 05:39:43.770000 | 172.17.0.2 |           1520
                                                  Request complete | 2017-09-10 05:39:43.771284 | 172.17.0.2 |           2284
```

A tombstone is created when updating non-null value to null. This tombstone is necessary because it will be sent to replica to indicate a deletion occurs.
