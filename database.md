
# Datatypes

## timestamp

Timestamps should be taken from application as Uint4 and stored as unsigned integers of size 4 bytes.

## micro_ts

This datatype is a unix epoch timestamp but with microseconds, can be stored as `decimal` OR `float` datatype. 
Just like normal timestamps first part (before decimal) is 10 digits long and second part that is after decimal is 4 digits long.
So a `decimal(14,4)` or `float(14,4)` is appropriate SQL column type.

## `Table` peers

Column | Datatype | Len/Size | Nullable | Default | Attributes | Notes
--- | --- | --- | --- | --- | --- | ---
ip_address | string | 45 | no | | | This is NOT a unique identifier
port | uint | 2 bytes | no | | | 
connected | uint | 1 byte | no | 0 | | "1" indicates connected, "0" for not connected
last_connect | micro_ts | decimal(14,4) | no | | | Store micro timestamp of last connection
last_disconnect | micro_ts | decimal(14,4) | yes | NULL | | Store micro timestamp when disconnects
**ip_port** | **unique** | | | | `ip_address` + `port` | 

## `Table` ip_tables

Column | Datatype | Len/Size | Nullable | Default | Attributes | Notes
--- | --- | --- | --- | --- | --- | ---
list | enum | "network", "rpc", "ws" | no | network | | This is a UNIQUE identifier in conjunction with `ip_address`
ip_address | string | 45 | no | | | This is a UNIQUE identifier in conjunction with `list`. This can ACCEPT WILDCARD "%" char
type | enum | "white", "black" | no | black | | Maintains 2 lists
added_on | timestamp | uint4 | no | | | When added to list
until | timestamp | uint4 | yes | NULL | | If NULL or non-positive integer then it is kept on list for indefinite period of time
**list_ip** | **unique** | | | | `list` + `ip_address` | 


## `Table` ip_violations

Column | Datatype | Len/Size | Nullable | Default | Attributes | Notes
--- | --- | --- | --- | --- | --- | ---
list | enum | "network", "rpc", "ws" | no | network | | This is a UNIQUE identifier in conjunction with `ip_address`
ip_address | string | 45 | no | | | This is a UNIQUE identifier in conjunction with `list`. NO wildcards, DEFINITE IP addresses only
count | uint | 1 byte | no | 1 | | +1 for every violation
last_on | timestamp | uint4 | no | | | Timestamp of last violation
**list_ip** | **unique** | | | | `list` + `ip_address` | 
