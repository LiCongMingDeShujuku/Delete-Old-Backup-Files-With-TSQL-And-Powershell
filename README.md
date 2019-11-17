![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 使用TSQL和Powershell删除旧的备份文档
#### Delete Old Backup Files With TSQL And Powershell
**发布-日期: 2017年07月17日 (评论)**

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
如果你是像我一样的DBA，可能在你的工作站上也会有一个本地数据库实例，这样你就可以在你正在进行的任何项目上快速运行各种测试。 在某些情况下，你甚至可能有快速备份脚本，这样你就可以将一些数据库安全地保存到驱动器中。

你可能需要运行一个根据特定日期删除旧数据库文件的程序。 这是一些快速SQL逻辑，它根据保留日期从备份文件夹中删除旧文件。 基于Powershell脚本，它将根据提供的变量自动添加备份路径和保留日期。

这个例子中的备份路径是：C:SQLBACKUPS

## English
If you’re a DBA like me you’ll probably have a local database instance on your workstation so that you can run a variety of tests quickly on whatever project you’re working on. In some cases you might even have a quick backup script so you can save some of your databases safely to your drive.

You’ll probably need to run a process that deletes the old database files based on a certain date. Here’s some quick SQL Logic which drops the old files from your backup folder based on a retention date. It’s based on a Powershell script, and the script will automatically add your backup path and retention date based on the supplied variables.

In this example the backup_path is C:SQLBACKUPS

![#](images/delete-old-backup-files-with-tsql-and-powershell-a.png?raw=true "#")

快速浏览一下Powershell脚本从xp_cmdshell开始运行的前后对比图。
Here’s the quick view before the Powershell script runs from xp_cmdshell, and after…

![#](images/delete-old-backup-files-with-tsql-and-powershell-b.png?raw=true "#")

---
## Logic
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
 
-- 	check files before deletion
-- 	删除前检查文件
exec    master..xp_dirtree  @backup_path, 1, 1
 
-- 	run file deletion with powershell
-- 	使用Powershell进行文件删除
exec    master..xp_cmdshell @removeoldfiles;
 
-- 	check files after deletion
-- 	删除后检查文件
exec    master..xp_dirtree  @backup_path, 1, 1


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

