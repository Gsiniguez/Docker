Using a data container 
Alternatively you can create a container holding the data.

unix> docker create --name arangodb-persist arangodb true
And use this data container in your ArangoDB container.

unix> docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 --volumes-from arangodb-persist -p 8529:8529 arangodb
If want to save a few bytes you can alternatively use busybox or alpine for creating the volume only containers. Please note that you need to provide the used volumes in this case. For example

unix> docker run -d --name arangodb-persist -v /var/lib/arangodb3 busybox true

----------------------------------------------------------------------------------------------------------------------------------------------
Choosing an authentication method 
The ArangoDB image provides several authentication methods which can be specified via environment variables (-e) when using docker run

ARANGO_RANDOM_ROOT_PASSWORD=1

Generate a random root password when starting. The password will be printed to stdout (may be inspected later using docker logs)

ARANGO_NO_AUTH=1

Disable authentication. Useful for testing.

WARNING Doing so in production will expose all your data. Make sure that ArangoDB is not directly accessible from the internet!

ARANGO_ROOT_PASSWORD=somepassword

Specify your own root password.

----------------------------------------------------------------------------------------------------------------------------------------------
Limiting resource utilization 
arangod checks the following environment variables, which can be used to restrict how much memory and how many CPU cores it should use:

ARANGODB_OVERRIDE_DETECTED_TOTAL_MEMORY (introduced in v3.6.3)

This variable can be used to override the automatic detection of the total amount of RAM present on the system. One can specify a decimal number (in bytes). Furthermore, if G or g is appended, the value is multiplied by 2^30. If M or m is appended, the value is multiplied by 2^20. If K or k is appended, the value is multiplied by 2^10. That is, 64G means 64 gigabytes.

The total amount of RAM detected is logged as an INFO message at server start. If the variable is set, the overridden value is shown. Various default sizes are calculated based on this value (e.g. RocksDB buffer cache size).

Setting this option can in particular be useful in two cases:

If arangod is running in a container and its cgroup has a RAM limitation, then one should specify this limitation in this environment variable, since it is currently not automatically detected.
If arangod is running alongside other services on the same machine and thus sharing the RAM with them, one should limit the amount of memory using this environment variable.
ARANGODB_OVERRIDE_DETECTED_NUMBER_OF_CORES (introduced in v3.7.1)

This variable can be used to override the automatic detection of the number of CPU cores present on the system.

The number of CPU cores detected is logged as an INFO message at server start. If the variable is set, the overridden value is shown. Various default values for threading are calculated based on this value.

Setting this option is useful if arangod is running in a container or alongside other services on the same machine and shall not use all available CPUs.