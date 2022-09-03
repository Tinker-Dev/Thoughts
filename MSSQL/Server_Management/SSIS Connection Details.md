# SSIS Connection Details

## Version 1

```
--Combine Package Configurations and Connection Strings
IF OBJECT_ID('tempdb..#SSISConnections') IS NOT NULL DROP TABLE #SSISConnections
IF OBJECT_ID('tempdb..#SSISPackageConfigs') IS NOT NULL DROP TABLE #SSISPackageConfigs
 
 
--Get Connection Managers
;WITH XMLNAMESPACES ('www.microsoft.com/SqlServer/Dts' AS DTS )
SELECT
       PackageXML.id AS PackageID,
       PackageXML.name AS PackageName,
       --packagedata,
       o.c.value('.', 'varchar(max)') PackageConnectionManagerName,
       t.c.value('.', 'varchar(max)') PackageConnectionType,
       cs.c.value('.', 'varchar(max)') PackageConnectionString,
       d.c.value('.', 'varchar(max)') ConnectionStringID
INTO #SSISConnections
FROM ( SELECT id, name, CAST(CAST( packagedata AS VARBINARY(MAX) ) AS XML) AS packagedata FROM msdb..sysssispackages) PackageXML
       CROSS APPLY PackageXML.packagedata.nodes('//DTS:ConnectionManager') cm(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name="ObjectName"]') o(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name="CreationName"]') t(c)
              CROSS APPLY cm.c.nodes('DTS:ObjectData/DTS:ConnectionManager/DTS:Property[@DTS:Name="ConnectionString"]') cs(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name = "DTSID"]') d(c)
 
--Get Package Configurations
;WITH XMLNAMESPACES ('www.microsoft.com/SqlServer/Dts' AS DTS )
SELECT
       PackageXML.id AS PackageID,
       PackageXML.name AS PackageName,
       --packagedata,
       o.c.value('.', 'varchar(max)') ConfigurationObjectName,
       s.c.value('.', 'varchar(max)') ConfigurationString,
       v.c.value('.', 'varchar(max)') ConfigurationVariable
INTO #SSISPackageConfigs
FROM ( SELECT  id, name, CAST(CAST( packagedata AS VARBINARY(MAX) ) AS XML) AS packagedata FROM msdb..sysssispackages) PackageXML
       CROSS APPLY PackageXML.packagedata.nodes('//DTS:Configuration') cm(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name="ObjectName"]') o(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name="ConfigurationString"]') s(c)
              CROSS APPLY cm.c.nodes('DTS:Property[@DTS:Name="ConfigurationVariable"]') v(c)
 
IF OBJECT_ID('tempdb..#SSISDetails') IS NOT NULL DROP TABLE #SSISDetails
SELECT
         j.name AS JobName
         ,j.description AS JobDescription
         ,j.enabled AS JobEnabled
         ,js.step_id AS JobStepId
         ,js.step_name AS JobStepName
         ,js.subsystem AS JobSystem
         ,LTRIM(RTRIM(REPLACE(SUBSTRING(command, PATINDEX('%/SERVER%', command), PATINDEX('% /%', SUBSTRING(command, PATINDEX('%/SERVER%', command), LEN(command) + 1))), '/Server', ''))) AS PackageServer
         ,sc.PackageName AS SSISPackage
         ,sc.PackageConnectionManagerName
         ,sc.PackageConnectionType
         ,sc.PackageConnectionString
         ,spc.ConfigurationObjectName
         ,spc.ConfigurationString
         ,spc.ConfigurationVariable
INTO #SSISDetails
FROM #SSISConnections AS sc
LEFT JOIN #SSISPackageConfigs AS spc ON spc.PackageID = sc.PackageID
                                                            AND sc.PackageConnectionManagerName = spc.ConfigurationObjectName
LEFT JOIN msdb.dbo.sysjobsteps AS js ON js.subsystem IN ('SSIS', 'CmdExec')
                                                              AND sc.PackageName = REPLACE(REPLACE(LTRIM(RTRIM(REPLACE(SUBSTRING(js.command, PATINDEX('%/SQL%', js.command), PATINDEX('% /%', SUBSTRING(js.command, PATINDEX('%/SQL%', js.command), LEN(js.command) + 1))), '/SQL', ''))),'"',''), '\', '')
LEFT JOIN msdb.dbo.sysjobs AS j ON j.job_id = js.job_id
ORDER BY j.name, js.step_id, sc.PackageName, sc.PackageConnectionManagerName
                                                                    
 
SELECT
*
FROM #SSISDetails AS sd
WHERE sd.PackageServer != 'YOUR SERVER NAME HERE'
ORDER BY sd.SSISPackage, sd.JobName, JobStepId, sd.PackageConnectionManagerName
 
 
IF OBJECT_ID('tempdb..#SSISConnections') IS NOT NULL DROP TABLE #SSISConnections
IF OBJECT_ID('tempdb..#SSISPackageConfigs') IS NOT NULL DROP TABLE #SSISPackageConfigs
IF OBJECT_ID('tempdb..#SSISDetails') IS NOT NULL DROP TABLE #SSISDetails

```

  

## Version 2

```
USE [SSISDB];
GO

IF OBJECT_ID('tempdb..#WorkingTable') IS NOT NULL DROP TABLE #WorkingTable

SELECT @@SERVERNAME AS ServerName
	   ,prj.name AS 'ProjectName'
	   ,op.object_name AS 'SSISPackageName'
	   ,prj.deployed_by_name 'deployed_by'
	   ,op.parameter_name AS 'ParmaterName'
	   ,CAST(op.design_default_value AS varchar(1000)) AS 'ConnectionString'	   
INTO #WorkingTable
FROM catalog.object_parameters op
JOIN catalog.projects prj
	ON op.project_id = prj.project_id
WHERE op.parameter_name LIKE '%ConnectionString%'
ORDER BY ProjectName,SSISPackageName

SELECT ServerName
	  ,ProjectName
	  ,SSISPackageName
	  ,deployed_by
	  ,CASE 
		WHEN ConnectionString LIKE '%Provider=SQLNCLI11%'
		 THEN REPLACE(REPLACE(LEFT(ConnectionString,CHARINDEX(';',ConnectionString)),'Data Source=',''),';','')
        WHEN ConnectionString LIKE '%Provider=IBMDADB2%'
		 THEN REPLACE(REPLACE(LEFT(ConnectionString,CHARINDEX(';',ConnectionString)),'Data Source=',''),';','')
		WHEN ConnectionString LIKE '%Extended Properties="EXCEL%'
		 THEN ''
        WHEN ConnectionString LIKE '%Application Name=SSIS%'
		 THEN REPLACE(REPLACE(LEFT(ConnectionString,CHARINDEX(';',ConnectionString)),'Data Source=',''),';','')		   
		ELSE NULL
		END AS ServerConnection
	,CASE 
		WHEN ConnectionString LIKE '%Provider=SQLNCLI11%'
		 THEN REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(LEFT(ConnectionString,CHARINDEX('SQLNCLI11',ConnectionString)),'Data Source=',''),'Initial Catalog=',''),';Provider=S',''),REPLACE(REPLACE(LEFT(ConnectionString,CHARINDEX(';',ConnectionString)),'Data Source=',''),';',''),''),';','')
		WHEN ConnectionString LIKE '%Provider=IBMDADB2%'
		 THEN 'DB2 Connection'
        WHEN ConnectionString LIKE '%Extended Properties="EXCEL%'
		 THEN 'EXCEL Connection'
		WHEN ConnectionString LIKE '%Application Name=SSIS%'
		 THEN 'Direct SSIS Connection'	  
		ELSE NULL
		END AS DatabaseConnection
	,ConnectionString

FROM #WorkingTable
WHERE ConnectionString NOT LIKE 'SmtpServer%'

```