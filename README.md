# Redis notes
Notes on Redis for learners like me


### Admin
```bash
redis-cli -h server.com -p 6390 PING # "PONG"

redis-cli --stat # shows RT stats

redis-cli --bigkeys # finds biggest keys

redis-cli --scan | head -10 # list of keys, print first 10

redis-cli --latency # monitors latency

redis-cli --rdb /tmp/dump.rdb # backup to specified RDB file

redis-cli ROLE # master/replica role

redis-cli INFO # server detailed info
```

### Get/set
```bash
redis-cli set test 2
redis-cli get test # "2"
```

### CSV output
```bash
redis-cli LPUSH mylist a b c d
redis-cli --csv LRANGE mylist 0 -1
# "d","c","b","a"
```

### Bitmaps 
```bash
redis-cli setbit key 10 1 # sets 10-th "key" bit to "1"
# (integer) 1

redis-cli getbit key 11 # gets 11-th "key" bit
# (integer) 1
```

### HyperLogLog
```bash
redis-cli pfadd hll a b c d d d
# (integer) 1
redis-cli pfcount hll # number of unique saved elements
# (integer) 4
```

### Pipelining
```bash
(printf "PING\r\nPING\r\nPING\r\n") | nc localhost 6379 # send all 3 commands in one batch
# +PONG
# +PONG
# +PONG
```

### Lua Functions (Redis >= 7)
```bash
redis-cli FUNCTION LOAD "#!lua name=mylib\nredis.register_function('knock', function() return 'hi' end)"
redis-cli FCALL knock 0
"hi"
```

### Pub/Sub
```bash
redis-cli SUBSCRIBE foo # subscribe to "foo"
redis-cli PUBLISH foo hi # publish "hi" to "foo"
```


### Transactions
```bash
# redis-cli 
127.0.0.1:6379> MULTI            -> start transaction
OK
127.0.0.1:6379> set a 1
QUEUED
127.0.0.1:6379> set b 2
QUEUED
127.0.0.1:6379> EXEC             -> commit transaction, use "DISCARD" to cancel
1) OK
2) OK
127.0.0.1:6379> 
```


### Watching keys (optimistic lock)
```bash
# redis-cli 
WATCH mykey              -> start watching this key
val = GET mykey
val = val + 1
MULTI
SET mykey $val           -> execute only if mykey is not changed since we watch it
EXEC
```

### Bulk data loading
```bash
echo "SET Key0 Value0" > data.txt
echo "SET Key1 Value1" >> data.txt
cat data.txt | redis-cli --pipe
```
