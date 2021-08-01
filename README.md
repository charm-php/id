A very fast UUID generator
==========================

After looking at other libraries, they seem very complex for the most common use case:
generating UUIDs.

This single class has a single purpose; generate compliant unique ID as fast as possible,
while being compliant. There is no functionality to analyze the IDs.

Usage:
------

```
use Charm\UUID;

// Generate a UUID v1 (uses /etc/machine-id or Windows registry key HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\MachineGuid)
echo UUID::v1();            // 22777a66-f316-11eb-98fd-1d3af91e59cf

// Generate a UUID v4 
echo UUID::v4();            // 2c107f61-58e3-42d5-a70b-ddf124fcd64f

// Generate a Snowflake ID (63 bit time series unique  integer)
echo UUID::snowflake();     // 262583374772080897

// Generate an instaflake ID (63 bit time series unique integer)
echo UUID::instaflake();    // 262583374770223362

// Generate an sonyflake ID (63 bit time series unique integer)
echo UUID::sonyflake();     // 32975755856302828
```

Configuration:
--------------

```
/**
 * Set the epoch for snowflake, instaflake and sonyflake
 */
UUID::$startTime = strtotime('2010-01-01 00:00:00');

/**
 * Set the machine id/shard id for UUID v1, snowflake, instaflake and sonyflake.
 *
 * The size of the number depends on which generator you plan to use. This only matters
 * when you reach the limit, which for snowflake is 31.
 */
UUID::$shardId = 12;

/**
 * Set the datacenter id for snowflake
 */
UUID::$datacenterId = 22;
```

Comparison:
-----------

* UUID v1 provides an 128 bit RFC4122 compliant UUID version 1 variant 1. It is designed to
  be globally unique for any datacenter and computer, always. It uses 48 bits to uniquely 
  identify the computer it runs on, and a 60 bit timestamp in 100 nanosecond increments 
  since 15th October 1582. The probability for a collision is extremely small.

* UUID v4 provides an 128 bit RFC4122 compliant UUID v4 variant 1 (also aka GUID). It is 
  built from a very large 122 bit random number. The generator uses a random_bytes() which
  consumes random numbers which are considered cryptographically secure.

* Snowflake is a 64 bit integer, which is suitable for storing in databases and is able to
  guarantee unique IDs across data centers and servers - provided the UUID::$datacenterId
  and UUID::$shardId is configured. The value increases over time.

* Instaflake and sonyflake are variations of Snowflake, with different priorities with regard
  timestamp resolution and number of shards.


Snowflake and variants are usually sufficient for any internal ID scheme up to Twitter or
Instagram scale. UUIDs are by many considered to be too large to be used extensively 
internally in a database - but are very useful to generate unique IDs for APIs or for allowing
external clients to generate IDs offline.
