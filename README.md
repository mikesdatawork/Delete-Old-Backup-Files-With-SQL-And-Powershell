![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Delete Old Backup Files With SQL And Powershell
**Post Date: July 17, 2017**





## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


<p>If you're a DBA like me you'll probably have a local database instance on your workstation so that you can run a variety of tests quickly on whatever project you're working on. In some cases you might even have a quick backup script so you can save some of your databases safely to your drive.
You'll probably need to run a process that deletes the old database files based on a certain date. Here's some quick SQL Logic which drops the old files from your backup folder based on a retention date. It's based on a Powershell script, and the script will automatically add your backup path and retention date based on the supplied variables.
In this example the backup_path is C:SQLBACKUPS</p>

![Powershell and SQL Backup Deletion]( https://mikesdatawork.files.wordpress.com/2017/07/delete-old-backup-files-with-powershell-mikes-data-work.png "Powershell SQL Backup Deletion")
 
Here's the quick view before the Powershell script runs from xp_cmdshell, and after…

![See Powershell and SQL Delete Backups]( https://mikesdatawork.files.wordpress.com/2017/07/image0021.png "Delete Old Backups With Powershell and SQL")
 


## SQL-Logic
```SQL
use master;
set nocount on
 
declare @backup_path        varchar(255)    = 'C:\SQLBACKUPS\'
declare @retention      varchar(3)  = '0'
declare @config0        int
declare @config1        int
declare @config2        int
declare @removeoldfiles     varchar(555)
set @config0        = (select cast([value_in_use] as int) from sys.configurations where [configuration_id]  = '518')
set @config1        = (select cast([value_in_use] as int) from sys.configurations where [configuration_id]  = '16390')
set @config2        = (select cast([value_in_use] as int) from sys.configurations where [configuration_id]  = '1579')
if  @config0        = 0 begin exec master..sp_configure 'show advanced options', '1'    reconfigure end;
if  @config1        = 0 begin exec master..sp_configure 'xp_cmdshell', '1'          reconfigure end;
if  @config2        = 0 begin exec master..sp_configure 'backup compression default', '1'   reconfigure end;
set @removeoldfiles     = 
    'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -command "&{Get-ChildItem ''' 
    + @backup_path  + ''' -Recurse | Where {$_.creationtime -lt (Get-Date).AddDays(-' 
    + @retention    + ')} | Remove-Item -Force}"'
 
-- check files before deletion
exec    master..xp_dirtree  @backup_path, 1, 1
 
-- run file deletion with powershell
exec    master..xp_cmdshell @removeoldfiles;
 
-- check files after deletion
exec    master..xp_dirtree  @backup_path, 1, 1

```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

  
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

