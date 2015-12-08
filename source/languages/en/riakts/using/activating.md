---
title: Activating Your Riak TS Table
project: riakts
version: 1.0.0+
document: guide
toc: true
index: true
audience: beginner
---

[planning]: http://docs.basho.com/riakts/1.0.0/using/planning
[writing]: http://docs.basho.com/riakts/1.0.0/using/writingdata

Once you have [planned out your table][planning] you can create it using `riak-admin`.

>**Note:** You will need to have access to `sudo` and `su` with the below commands.


##Creating Your Table

Remember the [example table][planning]?

```sql
CREATE TABLE GeoCheckin
(
   myfamily    varchar   not null,
   myseries    varchar   not null,
   time        timestamp not null,
   weather     varchar   not null,
   temperature double,
   PRIMARY KEY (
     (myfamily, myseries, quantum(time, 15, 'm')),
     myfamily, myseries, time
   )
)
```

To create the example table, first run:

```bash
sudo su riak
```

This will put you in a shell as the riak user. Then run:

```sh
riak-admin bucket-type create GeoCheckin '{"props":{"n_val":1, "table_def": "CREATE TABLE GeoCheckin (myfamily varchar not null, myseries varchar not null, time timestamp not null, weather varchar not null, temperature double, PRIMARY KEY ((myfamily, myseries, quantum(time, 15, 'm')), myfamily, myseries, time))"}}'
```

>Please note that the `bucket-type` name must equal the table name and may ONLY be ASCII.

Finally, exit the riak user shell by running `exit`.


##Activating Your Table

You activate your table just like you would activate a bucket type:

```sh
riak-admin bucket-type activate »TABLE NAME«
```

For the example `GeoCheckin` table:

```sh
riak-admin bucket-type activate GeoCheckin
```


##Viewing Table Scheme

To view the table scheme use the following command:

```sh
riak-admin bucket-type status »TABLE NAME«
```

For the example `GeoCheckin` table:

```sh
riak-admin bucket-type status GeoCheckin
```


##Verify Activation

You can verify that your table was properly created by looking at the `ddl` section of the `riak-admin bucket-type status` response. For example:

```sh
$riak-admin bucket-type status GeoCheckin
GeoCheckin is active
...
ddl: {ddl_v1,<<"GeoCheckin">>,
             [{riak_field_v1,<<"myfamily">>,1,binary,false},
              {riak_field_v1,<<"myseries">>,2,binary,false},
              {riak_field_v1,<<"time">>,3,timestamp,false},
              {riak_field_v1,<<"weather">>,4,binary,false},
              {riak_field_v1,<<"temperature">>,5,double,true}],
             {key_v1,[{hash_fn_v1,riak_ql_quanta,quantum,
                                  {param_v1,[<<"myfamily">>]},
                      {param_v1,[<<"myseries">>]}]},
                      [{param_v1,[<<"time">>]},15,m],
                                  timestamp},
             {key_v1,[{param_v1,[<<"time">>]},
                      {param_v1,[<<"myfamily">>]},
                      {param_v1,[<<"myseries">>]}]}}
```

##Next Steps

Now that you've created and activated your Riak TS table, you can [write data][writing] to it.