---
sidebar_label: Recon API 
---

# Recon API

The Recon API v1 offers a collection of HTTP endpoints designed to provide insights into the current state of an Ozone cluster, facilitating monitoring, management, and troubleshooting. These endpoints allow administrators to access critical cluster metadata, container status, key management, and more.

Endpoints that are marked as admin only can only be accessed by Kerberos users specified in the **ozone.administrators** or **ozone.recon.administrators** configurations of a secure cluster. See ['Securing Ozone'](https://ozone.apache.org/docs/edge/security/secureozone.html) for more information. To restrict access to these endpoints, set the following configurations:


[put table here]


Access an interactive version of the API, complete with detailed descriptions and example requests, powered by Swagger ['here'](https://ozone.apache.org/docs/edge/interface/swaggerreconapi.html)

## Containers (admin only)
## GET /api/v1/containers
### Parameters

- prevKey (optional)
Only returns the containers with ID greater than the given prevKey. Example: prevKey=1
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns all the ContainerMetadata objects.

``` bash
{
      "data": {
        "totalCount": 3,
        "containers": [
          {
            "ContainerID": 1,
            "NumberOfKeys": 834
          },
          {
            "ContainerID": 2,
            "NumberOfKeys": 833
          },
          {
            "ContainerID": 3,
            "NumberOfKeys": 833
          }
        ]
      }
    }
```


## GET /api/v1/containers/:id/keys
### Parameters

- prevKey (optional)
Only returns the keys that are present after the given prevKey key prefix. Example: prevKey=/vol1/bucket1/key1
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns all the KeyMetadata objects for the given ContainerID.

[put code image 2 here]

## GET /api/v1/containers/missing
### Parameters

- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns the MissingContainerMetadata objects for all the missing containers.

[put code image 3 here]

## GET /api/v1/containers/:id/replicaHistory
### Parameters

No parameters.

### Returns

Returns all the ContainerHistory objects for the given ContainerID.

[put code image 4 here]

## GET /api/v1/containers/unhealthy
### Parameters

- batchNum (optional)
The batch number (like “page number”) of results to return. Passing 1, will return records 1 to limit. 2 will return limit + 1 to 2 * limit, etc.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns the UnhealthyContainerMetadata objects for the containers in the given state. Possible unhealthy container states are `MISSING, MIS_REPLICATED,UNDER_REPLICATED, OVER_REPLICATED`. The response structure is same as `/containers/unhealthy`.

[put code image 5 here]

## GET /api/v1/containers/mismatch
### Returns

Returns the list of mis-matched containers between OM and SCM

- Containers are present in OM, but not in SCM.
- Containers are present in SCM, but not in OM.

[put code image 6 here]

## GET /api/v1/containers/mismatch/deleted
### Parameters

- prevKey (optional)
Returns the set of deleted containers in SCM which are present in OM to find out list of keys mapped to such DELETED state containers after the given prevKey (ContainerId). Example: prevKey=5, skip containers till it seeks correctly to the previous containerId.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns the set of deleted containers in SCM which are present in OM to find out list of keys mapped to such DELETED state containers.

[put code image 7 here]

## GET /api/v1/keys/open
### Parameters

- prevKey (optional)
Returns the set of keys/files which are open and present after the given prevKey id. Example: prevKey=/vol1/bucket1/key1, this will skip keys till it seeks correctly to the given prevKey.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns set of keys/files which are open.

[put code image 8 here]

## GET /api/v1/keys/deletePending
### Parameters

- prevKey (optional)
Returns the set of keys/files pending for deletion that are present after the given prevKey id. Example: prevKey=/vol1/bucket1/key1, this will skip keys till it seeks correctly to the given prevKey.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns set of keys/files pending for deletion.

[put code image 9 here]

## GET /api/v1/keys/deletePending/dirs
### Parameters

- prevKey (optional)
Returns the set of directories pending for deletion that are present after the given prevKey id. Example: prevKey=/vol1/bucket1/bucket1/dir1, this will skip directories till it seeks correctly to the given prevKey.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

## Returns

Returns set of directories pending for deletion.

[put code image 10 here]

# Blocks Metadata (admin only)
## GET /api/v1/blocks/deletePending
### Parameters

- prevKey (optional)
Only returns the list of blocks pending for deletion, that are present after the given block id (prevKey). Example: prevKey=4, this will skip deletedBlocks table key to skip records before prevKey.
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

## Returns

Returns list of blocks pending for deletion.

[put code image 11 here]

# Namespace Metadata (admin only)
## GET /api/v1/namespace/summary
### Parameters

- path
The path request in string without any protocol prefix.

### Returns

Returns a basic summary of the path, including entity type and aggregate count of objects under the path.

`status` is `OK` if path exists, `PATH_NOT_FOUND` otherwise.

Example: /api/v1/namespace/summary?path=/

[put code image 12 here]

Example: /api/v1/namespace/summary?path=/volume1

[put code image 13 here]

Example: /api/v1/namespace/summary?path=/volume1/bucket1

[put code image 14 here]

Example: /api/v1/namespace/summary?path=/volume1/bucket1/dir

[put code image 15 here]

Example: /api/v1/namespace/summary?path=/volume1/bucket1/dir/nestedDir

[put code image 16 here]

If any `num` field is `-1`, the path request is not applicable to such an entity type.

## GET /api/v1/namespace/usage
### Parameters

- path
The path request in string without any protocol prefix.
- files (optional)
A boolean with a default value of `false`. If set to `true`, computes namespace usage for keys under the path.
- replica (optional)
A boolean with a default value of `false`. If set to `true`, computes namespace usage with replicated size of keys.

### Returns

Returns the namespace usage of all sub-paths under the `path`. Normalizes path fields, returns total size of keys directly under the path as `sizeDirectKey`, and returns `size`/`sizeWithReplica` in number of bytes.

`status` is `OK` if path exists, `PATH_NOT_FOUND` otherwise.

Example: /api/v1/namespace/usage?path=/vol1/bucket1&files=true&replica=true

[put code image 17 here]

If `files` is set to `false`, sub-path `/vol1/bucket1/key1-1` is omitted. If `replica` is set to `false`, `sizeWithReplica` returns `-1`. If the path’s entity type cannot have direct keys (Root, Volume), `sizeDirectKey` returns `-1`.

# GET /api/v1/namespace/quota
### Parameters

- path
The path request in string without any protocol prefix.

### Returns

Returns the quota allowed and used under the path. Only volumes and buckets have quota. Other types are not applicable to the quota request.

`status` is `OK` if the request is valid, `PATH_NOT_FOUND` if path doesn’t exist, `TYPE_NOT_APPLICABLE` if path exists, but the path’s entity type is not applicable to the request.

Example: /api/v1/namespace/quota?path=/vol

[put code image 18 here]

If quota is not set, `allowed` returns `-1`. More on [Quota in Ozone] ['(https://ci-hadoop.apache.org/view/Hadoop%20Ozone/job/ozone-doc-master/lastSuccessfulBuild/artifact/hadoop-hdds/docs/public/feature/quota.html)'](https://ci-hadoop.apache.org/view/Hadoop%20Ozone/job/ozone-doc-master/lastSuccessfulBuild/artifact/hadoop-hdds/docs/public/feature/quota.html)

# GET /api/v1/namespace/dist
### Parameters

- path
The path request in string without any protocol prefix.

### Returns

Returns the file size distribution of all keys under the path.

`status` is `OK` if the request is valid, `PATH_NOT_FOUND` if path doesn’t exist, `TYPE_NOT_APPLICABLE` if path exists, but the path is a key, which does not have a file size distribution.

Example: /api/v1/namespace/dist?path=/

[put code image 19 here]

Recon keeps track of all keys with size from `1 KB` to `1 PB`. For keys smaller than `1 KB`, map to the first bin (index); for keys larger than `1 PB`, map to the last bin (index).

Each index of `dist` is mapped to a file size range (e.g. `1 MB` - `2 MB`).

# ClusterState
## GET /api/v1/clusterState
### Parameters

No parameters.

### Returns

Returns a summary of the current state of the Ozone cluster.

[put code image 20 here]

# Volumes (admin only)
## GET /api/v1/volumes
### Parameters

- prevKey (optional)
Only returns the volume after the given prevKey. Example: prevKey=vol1
- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns all the volumes in the cluster.

[put code image 21 here]

# Buckets (admin only)
## GET /api/v1/buckets
### Parameters

- volume (optional)
The volume in string without any protocol prefix.

- prevKey (optional)
Only returns the bucket after the given prevKey. prevKey is ignored if volume is not specified. Example: prevKey=bucket1

- limit (optional)
Only returns the limited number of results. The default limit is 1000.

### Returns

Returns all the buckets in the cluster if volume is not specified or it is an empty string. If `volume` is specified, it returns only the buckets under `volume`.

[put code image 22 here]

# Datanodes
## GET /api/v1/datanodes
### Parameters

No parameters.

### Returns

Returns all the datanodes in the cluster.

[put code image 23 here]

## PUT /api/v1/datanodes/remove
### Parameters

- uuids (List of node uuids in JSON array format).

[put code image 24 here]

### Returns

Returns the list of datanodes which are removed successfully and list of datanodes which were not found.

[put code image 25 here]

# Pipelines
## GET /api/v1/pipelines
### Parameters

No parameters.

### Returns

Returns all the pipelines in the cluster.

[put code image 26 here]

# Tasks
## GET /api/v1/task/status
### Parameters

No parameters.

### Returns

Returns the status of all the Recon tasks.

[put code image 27 here]

# Utilization
## GET /api/v1/utilization/fileCount
### Parameters

- volume (optional)
Filters the results based on the given volume name.
- bucket (optional)
Filters the results based on the given bucket name.
- fileSize (optional)
Filters the results based on the given fileSize.

### Returns

Returns the file counts within different file ranges with `fileSize` in the response object being the upper cap for file size range.

[put code image 28 here]

## GET /api/v1/utilization/containerCount
### Parameters

- containerSize (optional)
Filters the results based on the given container size. The smallest container size being tracked for count is 512 MB (512000000 bytes).

### Returns

Returns the container counts within different container size ranges, with `containerSize` representing the size range and `count` representing the number of containers within that range.

[put code image 29 here]

# Metrics
## GET /api/v1/metrics/:api
### Parameters

Refer to ['Prometheus HTTP API Reference'](https://prometheus.io/docs/prometheus/latest/querying/api/) for complete documentation on querying.

### Returns

This is a proxy endpoint for Prometheus and returns the same response as the prometheus endpoint. Example: /api/v1/metrics/query?query=ratis_leader_election_electionCount

[put code image 30 here]

