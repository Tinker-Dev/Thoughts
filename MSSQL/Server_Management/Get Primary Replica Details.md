# Get Primary Replica Details

## Version 1

This script will check to make sure that you are running it against the primary replica and if you are it will return with some details about the setup.  

  

```
IF SERVERPROPERTY('IsHadrEnabled') = 1
BEGIN
    WITH AGStatus
    AS
     (SELECT g.name AS AGname
             ,r.replica_server_name
             ,CASE
                  WHEN (s.primary_replica = r.replica_server_name) THEN
                      1
                  ELSE
                      ''
              END AS IsPrimaryServer
             ,r.secondary_role_allow_connections_desc AS ReadableSecondary
             ,r.[availability_mode] AS Synchronous
             ,r.failover_mode_desc
             ,s.synchronization_health_desc
      FROM master.sys.availability_groups AS g
          INNER JOIN master.sys.availability_replicas AS r
              ON g.group_id = r.group_id
          INNER JOIN master.sys.dm_hadr_availability_group_states AS s
              ON g.group_id = s.group_id)
    SELECT ags.AGname
           ,ags.replica_server_name
           ,ags.IsPrimaryServer
           ,ags.Synchronous
           ,ags.ReadableSecondary
           ,ags.failover_mode_desc
           ,ags.synchronization_health_desc
    FROM AGStatus AS ags
    --WHERE
    --ags.IsPrimaryServer = 1
    --AND ags.Synchronous = 1
    ORDER BY ags.AGname ASC
             ,ags.IsPrimaryServer DESC;
END

```

  

## Version 2

```
IF SERVERPROPERTY ('IsHadrEnabled') = 1
BEGIN
SELECT
   AGC.name -- Availability Group
 , RCS.replica_server_name -- SQL cluster node name
 , ARS.role_desc  -- Replica Role
 , AGL.dns_name  -- Listener Name
FROM
 sys.availability_groups_cluster AS AGC
  INNER JOIN sys.dm_hadr_availability_replica_cluster_states AS RCS
   ON
    RCS.group_id = AGC.group_id
  INNER JOIN sys.dm_hadr_availability_replica_states AS ARS
   ON
    ARS.replica_id = RCS.replica_id
  INNER JOIN sys.availability_group_listeners AS AGL
   ON
    AGL.group_id = ARS.group_id
WHERE
 ARS.role_desc = 'PRIMARY'
END

```