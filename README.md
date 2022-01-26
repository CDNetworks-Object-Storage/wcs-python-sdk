# Python SDK for wcs

## Overview

This Python SDK is applied to Python 2.X. For Python 3.X, please go to [wcs-python3-sdk](https://github.com/CDNetworks-Object-Storage/wcs-python3-sdk). 

***Note: wcs-python-sdk can be also used as a Command Line Tool.***

To use as a SDK, you can

- Upload
- Resource Management
- Advanced Resource Management
- Persistent Processing
- Query for Operation Status
- Query for Live Recording Files

To use as a Command Line Tool, you can 

- Upload
- Multipart Upload
- Resource Management
- Delete Object by Prefix

## Install

Pip install recommended.

- Install

```
Python2：pip install wcs-python-sdk
```

- Upgrade

```
Python2：pip install -U wcs-python-sdk
```

## Initialize

Before using the SDK, you need to obtain ***AccessKey*** and ***SecretKey*** for signature authorization.

And then, you have to get these info: 

1. account: Apply for CDNetworks cloud storage service, get the account. 

2. AK/SK: Log in to CDNetworks SI portal, and you can get the ***AccessKey*** and ***SecretKey*** in Security Console - AK/SK Management 

3. puturl&mgrurl: Log in to SI portal and view the Upload Domain (puturl) and Manage Domain (mgrurl) in Bucket Overview -> Bucket Settings

After obtaining the above info, executing ```wcscmd --configure``` to initialize.

The updated config will be saved in ***.wcscfg*** file under***$HOME*** directory. And you can print the config added after initialization by command ```wcscmd --dump-config```

The configuration parameters in ***.wcscfg*** file are:

```
access_key  # Access key of user
block_size  # Block size in multipart upload, default is 4194304，unit is B
bput_retries  #For multipart upload, the request retries of bput
bput_size  # Chunk size in multipart upload, default is 524288, unit is B
callbackBody  # when upload finished, the data to callbackurl
callbackUrl  # when upload finished, POST request to this address
concurrency  # Block concurrency, upload in order if this value is 0
connection_retries  # number of retries when request connection
connection_timeout  # Timeout when request connection
contentDetect  #After uploading, detect content
detectNotifyRule  # Rules of notification in content detection
detectNotifyURL  # Address for receiving result, public network URL
force  # If force to execute, default is 0 – not force
ishttps  # If request in https
limit  # this para is for List API, define the items listed
marker # For List API, mark the point in last list as the start point
mkblk_retries  # for multipart upload, retries number of mkblk
mkfile_retries   # for multipart upload, retries number of mkfile
mode   # For List API, define the sorting method of list
notifyurl  # URL for receiving result in asynchronous processing
output  #Save descriptor of task as specify file, format：<bucket>:<key>
overwrite   # When upload, if overwrite if filename is existing
persistentNotifyUrl   # Address to receive pre-processing result
persistentOps  # After uploading, the pre-processing command
prefix  # The para prefix when list resource
put_url   # the upload domain of users
returnBody  #The data return to upload end when uploading is done
returnUrl #Storage will POST request to this address when uploading is done
secret_key  # secret key of user
separate  # If separately notify the processing commands?
tmp_record_folder  # upload progress record directory for multipart upload
upload_id   # task id of breakpoint resume in multipart upload
```

## How to use as a Command Line Tool

In Windows OS, you need to add “python” to execute command line, e.g.`Python wcscmd --help`

#### Command Help

```
wcscmd --help
Commands:
List objects
	wcscmd list wcs://BUCKET RESFILE
List buckets
	wcscmd listbucket
Download file
	wcscmd get URL
Delete a file
	wcscmd del wcs://BUCKET/OBJECT
Move a file from src bucket to des bucket
	wcscmd mv wcs://srcBUCKET/srcOBJECT wcs://dstBUCKET/desOBJECT
Copy a file from src bucket to des bucket
	wcscmd cp wcs://srcBUCKET/srcOBJECT wcs://dstBUCKET/desOBJECT
Set deadline of file
	wcscmd setdeadline wcs://BUCKET/OBJECT deadline
Get file info
	wcscmd stat wcs://BUCKET/OBJECT
Upload a local file to WCS
	wcscmd put wcs://BUCKET/OBJECT LOCALFILE
Multipart upload a local file to WCS
	wcscmd multiput wcs://BUCKET/OBJECT LOCALFILE
Delete multiple files according to prefix
	wcscmd deletePrefix wcs://BUCKET PREFIX
Fops audio/video processing
	wcscmd fops wcs://BUCKET/OBJECT fopsparm
Get fops task results
	wcscmd fopsStatus  persistentId
Get fmgr task results
	wcscmd fmgrStatus  persistentId
```

#### wcscmd Upload

The upload policy can be defined by editting .wcscfg, as well as have temporary configurations by the option in command line.

```
wcscmd put wcs://BCUKET/OBJECT localPath  --overwrite 1
```

#### wcscmd Multipart Upload

The upload policy can be defined by editting ***.wcscfg***, as well as have temporary configurations by the `option` in command line.
If breakpoint resume function is required, you need to add an option `--upload-id`, in this case, the priority of this upload-id is higher than upload-id in .wcscfg.

```
wcscmd multiput wcs://BCUKET/OBJECT localPath --upload-id 3IL3ce3kR6kDf4sihxh0LcWUpzTYEKFf
```

#### wcscmd List Bucket

```
wcscmd listbucket
```

#### wcscmd List Object of a Bucket

E.g. In the below example, the result of list will be saved in ***result*** file of current directory.

```
wcscmd list wcs://BCUKET ./result --limit 4  --marker 2
```

#### wcscmd Download an Object

Use parameter `filename` to name the object you want to download if you want to rename it, or the name will be be kept same with that in object storage.
Note: The URL must be enclosed by quotes “url”.

```
wcscmd get [URL] [filename]
```

#### wcscmd Get the Info of Object

```
wcscmd stat wcs://BCUKET/OBJECT
```

#### wcscmd Set the Expiration of Object

The unit of expiration is DAY.

- 0 means delete it as soon as possible
- -1 means cancel the expiration, and it will be stored permanently 

Note: When setting, -1 must be enclosed by quotes.

```
wcscmd setdeadline wcs://BCUKET/OBJECT 3
wcscmd setdeadline wcs://BCUKET/OBJECT '"-1"'
```

#### wcscmd Delete Object

```
wcscmd del wcs://BCUKET/OBJECT
```

#### wcscmd Delete Object by Prefix

```
wcscmd deletePrefix wcs://BCUKET test-prefix
```

#### wcscmd Move Object

```
wcscmd mv wcs://SRCBUCKET/SRCOBJECT wcs://DSTBUCKET/DSTOBJECT
```

#### wcscmd Copy Object

```
wcscmd cp wcs://SRCBUCKET/SRCOBJECT wcs://DSTBUCKET/DSTOBJECT
```

## Generate Etag

wcs-python-sdk provides the tool to generate etag value, you can have this through command line.

```
/usr/bin/wcs_etag_cal -h
usage: WCS-Python-SDK [-h] {etag} ...
positional arguments:

{etag}

etag      etag [file…]
optional arguments:

-h, --help  show this help message and exit
/usr/bin/wcs_etag_cal etag filepath1 filepath2

[filepath1, filepath2]

FrA377uGHSxcTM62-rjsjvoKqRVS FiUsqBkZ6e8KaAA9Uu6q3qLPgmDW
```



### Common Issues

1、The error will occur when using this tool: `pkg_resources.DistributionNotFound: [modulename]`

2、Solution for this error: `pip install --upgrade setuptools`

## How to use as a Python SDK

### Import and Initialize

```
import os
from os.path import expanduser
from wcs.commons.config import Config
from wcs.services.client import Client
config_file = os.path.join(expanduser("~"), “.wcscfg”)

cfg = Config(config_file) //Add config file

cli = Client(cfg) //Initialize the client # Note:Clients should be create separately in different threads when uploading with multithreads.
```



#### Upload

The upload policy can be defined by ***.wcscfg*** file.

```
key = ''
bucket = ''
filepath = ''
cli.simple_upload(filepath, bucket, key)
```

#### Multipart Upload

1. The upload policy can be defined by ***.wcscfg*** file, as well as have temporary configurations by the option in command line.
2. If breakpoint resume function is required, you need to add an option `--upload-id`, in this case, the priority of this `upload-id` is higher than upload-id in .wcscfg.

```
key = ''
bucket = ''
filepath = ''
upload_id = ''
cli.multipart_upload(filepath, bucket, key，upload_id)	//use connection resuming on break-point
cli.multipart_upload(filepath, bucket, key)		//not to use connection resuming on break-point
```

Besides, current upload record is in tmp_record_folder directory, it will generate directories named with upload id. And it will generate multiple objects in directory ***tmp_record_folder/upload id***. Each object will be named with its offset, and it will record the upload result.

#### Advanced Upload

1. Advanced upload will help to choose upload or multipartupload automatically. Object larger than `multi_size` will be uploaded by multipart, otherwise, will be uploaded normally. Default `multi_size` is 20Mb.
2. `upload id` is needed when using Connection resuming on break-point,the priority this `upload-id` is higher than `upload-id` in .wcscfg.
```
key = ''
bucket = ''
filepath = ''
upload_id = ''
cli.smart_upload(filepath, bucket, key, upload_id, multi_size=20)
```

#### Upload by Stream Address

The upload policy can be defined by ***.wcscfg*** file, and the stream address must be provided.

```
key = ''
bucket = ''
stream = ''
cli.stream_upload(stream, bucket, key)
```

#### List Bucket

```
cli.list_buckets()
Notes: No need to do BASE64 encoding in the para prefix input. 
```

#### List Object

There are four optional parameters (limit, mode, prefix, marker). You can also set the parameters in ***.wcscfg*** file.

```
cli.bucket_list(bucket,limit=10)
```

#### Get the Storage Volume

```
startdate = 'yyyy-mm-dd'
enddate = 'yyyy-mm-dd'
bucket = ''
cli.bucket_stat(bucket, startdate, enddate)
```

#### Get the Object Info

```
key = ''
bucket = ''
cli.stat(bucket, key)
```

#### Delete Object (synchronous)

```
key = ''
bucket = ''
cli.delete(bucket, key)
```

#### Move Object (synchronous)

```
srcbucket = ''
srckey = ''
dstbucket = ''
dstkey = ''
cli.move(srcbucket, srckey, dstbucket, dstkey)
```

#### Copy Object (synchronous)

```
srcbucket = ''
srckey = ''
dstbucket = ''
dstkey = ''
cli.copy(srcbucket, srckey, dstbucket, dstkey)
```

#### Set the Expiration of Object

```
bucket = ''
key = ''
deadline = 3
cli.setdeadline(bucket, key, deadline)
```

#### Move Object (asynchronous)

```
srcbucket = 'srcbucket'
srckey = '1.doc'
dstbucket = 'dstbucket'
dstkey = '2.doc'
resource = urlsafe_base64_encode('%s:%s' % (srcbucket,srckey))
fops = 'resource/%s/bucket/%s/key/%s' % (resource,urlsafe_base64_encode(dstbucket), urlsafe_base64_encode(dstkey))
cli.fmgr_move(fops)
```

#### Copy Object (asynchronous)

```
srcbucket = 'srcbucket'
srckey = '1.doc'
dstbucket = 'dstbucket'
dstkey = '2.doc'
resource = urlsafe_base64_encode('%s:%s' % (srcbucket,srckey))
fops = 'resource/%s/bucket/%s/key/%s' % (resource,urlsafe_base64_encode(dstbucket), urlsafe_base64_encode(dstkey))
cli.fmgr_copy(fops)
```

#### Fetch Object

```
url = 'http://a20170704-weihb.w.wcsapi.biz.matocloud.com/1.doc'
key = '1.doc'
bucket = 'test'
fetchurl = urlsafe_base64_encode(url)
enbucket = urlsafe_base64_encode(bucket)
enkey = urlsafe_base64_encode(key)
fops = 'fetchURL/%s/bucket/%s/key/%s' % (fetchurl, enbucket, enkey)
cli.fmgr_fetch(fops)
```

#### Delete Object (asynchronous)

```
key = '1.doc'
bucket = 'test'
enbucket = urlsafe_base64_encode(bucket)
enkey = urlsafe_base64_encode(key)
fops = 'bucket/%s/key/%s' % (enbucket, enkey)
cli.fmgr_delete(fops)
```

#### Delete Object by Prefix

```
prefix = 'test'
bucket = 'bucket'
enbucket = urlsafe_base64_encode(bucket)
enprefix = urlsafe_base64_encode(prefix)
fops = 'bucket/%s/prefix/%s' % (enbucket, enprefix)
cli.prefix_delete(fops)
```

#### Delete M3U8

```
bucket = ''
key = ''
enbucket = urlsafe_base64_encode(bucket)
enkey = urlsafe_base64_encode(key)
fops = 'bucket/%s/key/%s' % (enbucket, enkey)
cli.m3u8_delete(fops)
```

#### Query for Advanced Resource Management

```
persistentId = ''
cli.fmgr_status(persistentId)
```

#### Audio/Video Processing

```
bucket = 'test'
key = 'test.mp4'
fops = 'vframe/jpg/offset/1'
cli.ops_execute(fops,bucket,key)
```

#### Query for Recording Files of Live

Request parameters:

| Parameter   | Required | Description                                                  |
| ----------- | -------- | ------------------------------------------------------------ |
| channelname | yes      | Streaming name of Live                                       |
| startTime   | yes      | Specify the start time of Live, the fromat is YYYYMMDDmmhhss |
| endTime     | yes      | Specify the end time of Live, the fromat is YYYYMMDDmmhhss   |
| bucket      | yes      | Specify Bucket                                               |

example

```cli.wslive_list(channelname,startTime,startTime, bucket,start,limit)```

## crc64 calculation
There are three methods to calculate crc64 value of a file.
### method 1
Calculating with wcscmd
```wcscmd crc64 ./test-1k```
### method 2
```
from wcs.commons.util import file_crc64
filepath = 'xxxx'
crc64Value = file_crc64(filepath)

from wcs.commons.util import file_crc64
fileStream = 'xxxx' 
crc64Value = file_crc64(fileStream,is_path=False)

from wcs.commons.util import crc64
crc64Value = crc64(stream)
```
### method 3
```
usage: WCS Python SDK [-h] {crc64} ...
positional arguments:
    {crc64}
    crc64     crc64 [file...]
optional arguments:
    -h, --help  show this help message and exit

/usr/bin/wcs_crc64_cal crc64 filepath1 filepath2
[filepath1, filepath2]
1798452899179748974 5299837023984967047
```
