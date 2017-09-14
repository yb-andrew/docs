---
title: HMGET
---

## SYNOPSIS
<b>`HMGET key field [field ...]`</b><br>
This command is to fetch one or more values for the given fields of the hash that is associated with the given `key`.

<li>For every given `field`, (null) is returned if either `key` or `field` does not exist.</li>
<li>If `key` is associated with a non-hash data, an error is raised.</li>

## RETURN VALUE
Returns list of string values of the fields in the same order that was requested.

## EXAMPLES
```
$ HMSET yugahash area1 "Africa" area2 "America"
"OK"
$ HMGET yugahash area1 area2 area_none
1) "Africa"
2) "America"
3) (null)
```

## SEE ALSO
[`hdel`](../hdel/), [`hexists`](../hexists/), [`hget`](../hget/), [`hgetall`](../hgetall/), [`hkeys`](../hkeys/), [`hlen`](../hlen/), [`hmset`](../hmset/), [`hset`](../hset/), [`hstrlen`](../hstrlen/), [`hvals`](../hvals/)