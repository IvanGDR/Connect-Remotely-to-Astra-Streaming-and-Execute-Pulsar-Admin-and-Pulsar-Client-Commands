# Connect Remotely to Astra Streaming and execute pulsar-admin and pulsar-client commands


Download pulsar binaries to /opt/pulsar directory, previously created
```
$ opt/pulsar > sudo wget https://github.com/apache/pulsar/archive/refs/tags/v2.10.0.zip
```
```
--2022-08-14 10:03:15--  https://github.com/apache/pulsar/archive/refs/tags/v2.10.0.zip
Resolving github.com (github.com)... 192.30.255.112
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
...
Saving to: ‘v2.10.0.zip’

v2.10.0.zip             [        <=>         ]  58.73M  11.3MB/s    in 5.2s

2022-08-14 10:03:20 (11.3 MB/s) - ‘v2.10.0.zip’ saved [61587741]

```
Verify file has been downloaded
```
$ opt/pulsar$ ls -la
```
```
-rw-r--r--  1 root      root      61587741 Aug 14 10:03 v2.10.0.zip
```

Unzip downloaded file
```
$ opt/pulsar > sudo unzip v2.10.0.zip
```
```
inflating: pulsar-2.10.0/tiered-storage/src/main/resources/findbugsExclude.xml
   creating: pulsar-2.10.0/wireshark/
inflating: pulsar-2.10.0/wireshark/README.md
inflating: pulsar-2.10.0/wireshark/pulsar.lua
....
```

back one level and change ownership to folder and files recursively
```
$ /opt > sudo chown -R automaton:automaton pulsar 
```

go to unziped folder and list content:
```
$ /opt/pulsar/apache-pulsar-2.10.0 > ls -la
```
```
-rw-r--r--  1 automaton automaton 32333 Jan 22  2020 LICENSE
-rw-r--r--  1 automaton automaton  6612 Jan 22  2020 NOTICE
-rw-r--r--  1 automaton automaton  1269 Jan 22  2020 README
drwxr-xr-x  3 automaton automaton  4096 Jun  7 17:46 bin
drwxr-xr-x  5 automaton automaton  4096 Jun 10 05:00 conf
drwxr-xr-x  2 automaton automaton  4096 Jun  7 17:51 connectors
drwxrwxr-x  4 automaton automaton  4096 Jun  7 17:44 data
drwxrwxr-x  3 automaton automaton  4096 Jun  7 16:54 examples
drwxrwxr-x  4 automaton automaton  4096 Jun  7 16:54 instances
drwxrwxr-x  3 automaton automaton 24576 Jun  7 16:54 lib
drwxr-xr-x  2 automaton automaton  4096 Jan 22  2020 licenses
drwxrwxr-x  2 automaton automaton 28672 Aug  2 00:34 logs
```


### Connect to Astra Streaming using Pulsar CLI


The minimun requirement to connect remotely to Astra Streaming and start using Pulsar Admin and Pulsar CLI is to have a tenant already in place.

In my case I have created a tenant called "ivantenant", after this go to the connect tab and download the client.conf file.


<p align="center">
<img width="900" height="250" src="https://user-images.githubusercontent.com/67383481/184938117-6ced728b-9139-4976-85b7-c63df79d5ce5.png">
</p>


My client.conf file looks like this:

```
webServiceUrl=https://pulsar-gcp-useast1.api.streaming.datastax.com

brokerServiceUrl=pulsar+ssl://pulsar-gcp-useast1.streaming.datastax.com:6651

authPlugin=org.apache.pulsar.client.impl.auth.AuthenticationToken

authParams=token:eyJhbGciOi.....

tlsAllowInsecureConnection=false

tlsEnableHostnameVerification=true

useKeyStoreTls=false

tlsTrustStoreType=JKS

tlsTrustStorePath=

tlsTrustStorePassword=
```

This downloaded client.conf file should replace the file with similar name in your downloaded and unzipped pulsar binaries directory, therefore go to:

```
$ cd /opt/pulsar/apache-pulsar-2.10.0/conf
```

Rename the client.conf file that comes with the binaries by default
```
$ /opt/pulsar/apache-pulsar-2.10.0/conf > sudo mv client.conf client.conf.backuppulsar210
```
Create a new client.conf file and paste the the content of the downloaded conf.file obtained from the Astra Pulsar intance 
```
$ /opt/pulsar/apache-pulsar-2.10.0/conf > cat client.conf
```
```
webServiceUrl=https://pulsar-gcp-useast1.api.streaming.datastax.com

brokerServiceUrl=pulsar+ssl://pulsar-gcp-useast1.streaming.datastax.com:6651

authPlugin=org.apache.pulsar.client.impl.auth.AuthenticationToken

authParams=token:eyJhbGciOi.....

tlsAllowInsecureConnection=false

tlsEnableHostnameVerification=true

useKeyStoreTls=false

tlsTrustStoreType=JKS

tlsTrustStorePath=

tlsTrustStorePassword=
```

### Using pulsar-admin and pulsar-client commands

It is not neccessary to start any of the pulsar components as zookeeper, bookkeeper or broker etc.


for example, executing straight away this command to list your topics from your Astra Streaming Instance:
```
$ /opt/pulsar/apache-pulsar-2.10.0 > ./bin/pulsar-admin topics list ivantenant/ivannamespace
```
```
persistent://ivantenant/ivannamespace/ivantopic
```

The above command is just a tiny hint of the options to interact with your Astra Streaming instance these are much more wider now.

For a full reference of options available within Astra Streaming visit:

[DataStax Astra Streaming Documentation](https://docs.datastax.com/en/astra-streaming/docs/astream-quick-start.html#use-pulsar-tools)

And for all the optios offered by Apache Pulsar that potentially can be used within Astra Streaming visit:

[Apache Pulsar-Admin command list](https://pulsar.apache.org/tools/pulsar-admin/2.10.0-SNAPSHOT/)


[Apache Pulsar-Client command list](https://pulsar.apache.org/docs/next/reference-cli-tools#pulsar-client)


For commands that are not available to push to Astra Streaming, this will be received:
```
$ /opt/pulsar/apache-pulsar-2.10.0 > ./bin/pulsar-admin clusters list
```
```
HTTP 401 Unauthorized
Reason: HTTP 401 Unauthorized
```
