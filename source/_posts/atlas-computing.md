---
title: Computing Resources (DESY and IHEP)
date: 2018-06-05 23:13:35
tags: 
- ATLAS Basic
- Computing
categories: Tutorials
---

## DESY computer resources
### DESY hosts
Hosts working with Qsub:
`nafhh-atlas01.desy.de ~ nafhh-atlas02.desy.de`
Hosts working with HTCondor:
`naf-atlas11.desy.de ~ naf-atlas16.desy.de.`
### submit HTCondor jobs:
The DESY local computing system has been updated to [HTcondor](https://confluence.desy.de/pages/viewpage.action?pageId=67639562)
The following commands is the cheat list from qsub to HTcondor
<!--more-->

Function | QSub (SGE) | HTCondor
 --- |  --- | ---
Submit | qsub 123.job | echo "executable = 123.job" > 123.sub ; `condor_submit 123.sub` (more details below)
Show info | qstat | `condor_q`
Delete | qdel | `condor_rm` (`condor_rm [username]`: kill all your jobs)

While submitting jobs, it has to be changed from
`qsub -q short.q,default.q  -l os=sld6 -l site=hh -l h_vmem=8G -o ${base_dir}/out/${jobname}_"$ijob".out  -e  ${base_dir}/err/{jobname}_"$ijob".err  ${jobfilename}`
To
```
rm -fr  ${submitfilename}
echo "executable     = ${jobfilename}"  >  ${submitfilename}
echo "should_transfer_files   = Yes" >> ${submitfilename}
echo "when_to_transfer_output = ON_EXIT" >> ${submitfilename}
echo "input          = ${base_dir}/input/${jobname}_"$ijob".txt"  >> ${submitfilename}
echo "output         = ${base_dir}/out/${jobname}_"$ijob".out2" >> ${submitfilename}
echo "error          = ${base_dir}/err/${jobname}_"$ijob".err2" >> ${submitfilename}
echo "log            = ${base_dir}/log/${jobname}_"$ijob".log2" >> ${submitfilename}
echo "+RequestRuntime = 43200" >> ${submitfilename}
echo "universe       = vanilla" >> ${submitfilename}
echo "RequestMemory   = 4G" >> ${submitfilename}
echo "queue" >> ${submitfilename}
condor_submit ${submitfilename}
```

The example scripts are in this path: 
`/afs/desy.de/user/h/hans/public/condor_example`

### DESY HTCondor queue :
There are ~8000 cpus, HTCondor can distribute cpus for jobs itself, please always check http://bird.desy.de/stats/day.html
The Qsub (SGE) has already been closed: http://bird.desy.de/status/day.html
### DESY Tier3
Login https://amfora.desy.de to check AFS and NAF quota
AFS is usually 16G, NAF is 1T (default)
### DESY Tier2
The quota on `DESY-HH_LOCALGROUPDISK` will be 20T (default) after applying for atlas/de VO
The transferred files on this disk can be found under /pnfs path

## IHEP computer resources
http://afsapply.ihep.ac.cn:86/quick/
### IHEP hosts:
SLC6 hosts:
`lxslc6.ihep.ac.cn, atlasui01.ihep.ac.cn`
`atlasui04.ihep.ac.cn ~ atlasui06.ihep.ac.cn (IHEP inner hosts)`
SLC5 hosts:
`atlasui03.ihep.ac.cn, lxslc5.ihep.ac.cn`
### submit HTCondor jobs:
Initialize:
```
export PATH=/afs/ihep.ac.cn/soft/common/sysgroup/hep_job/bin5:$PATH  # SLC5
export PATH=/afs/ihep.ac.cn/soft/common/sysgroup/hep_job/bin:$PATH  # SLC6
```
Then use the following cheat table:

Function | Qsub | HTCondor
 --- |  --- | ---
Submit | qsub | `hep_sub`
Show info | qstat | `hep_q`
Delete | qdel | `hep_rm`

`hep_rm -a: kill all the jobs`
`hep_q -u yourIhepUserName: check your jobs status`

normally it is very similar as using Qsub, we just need use the command:
`hep_sub job.sh -o out -e err -mem 4800 -g atlas`

other optional arguments:
```
-os OPERATINGSYSTEM, --OperatingSystem OPERATINGSYSTEM
                        # set the system version of resource you want.
-prio PRIORITY, --priority PRIORITY
                        # set the inner job priority of your own jobs.
-np NUMBERPROCESS, --numberprocess NUMBERPROCESS
                        # set the total cores required by your job.
```
The example scripts are in this path (do not submit in /workfs or /afs): 
`/publicfs/atlas/atlasnew/higgs/hgg/ShuoHan/ATLAS/job1_data2016`

### IHEP HTCondor queue :
one universal queue sharing 4300 cpus, the working time is infinite, HTCondor can distribute cpus for jobs itself
### IHEP Tier3:

disk name | disk path | size | backup | Can submit HTCondor jobs
 --- |  --- | --- | --- | ---
afs | /afs/ihep.ac.cn/users/ | 500MB | no | no
workfs | /workfs/atlas/ | 10GB | yes | no
scratchfs | /scratchfs/atlas/ | 500GB | delete every 2 weeks | yes
publicfs atlasnew | /publicfs/atlas/atlasnew/higgs/hgg/ | 320TB shared | no | yes
publicfs codesbackup | /publicfs/atlas/codesbackup/ | 320TB shared | yes | yes
### IHEP Tier2
The quota on `BEIJING-LCG2_LOCALGROUPDISK` will be 8T after applying for atlas/cn VO
But those files (on disk /dpm) should be downloaded to Tier3 anyway by command "dpns-ls -l" and "rfcp" , they cannot be directly readed

### IHEP VPN
#### solution 1: using SOCKS5 (this method can also be used to link CERN VPN from IHEP)
Try the following commands:

```
ssh -qfTnN -D 127.0.0.1:1080 username@lxslc6.ihep.ac.cn
curl --socks5 127.0.0.1:1080 http://cip.cc
```

Then you'll find something like:

```
IP	: 202.122.33.191
地址	: 中国  北京
运营商	: 科技网

数据二	: 北京市 | 高能物理所

URL	: http://www.cip.cc/202.122.33.191
```

Then change the Internet-Advance setting (Mac), as shown in the plot:

<div style="width: 60%">![Mac VPN setting](../../../../../shhan/images/mac-vpn.jpg)</div>

#### solution 2 using IHEP VPN service (more convienent)
Directly login with IHEP account in  http://testvpn.ihep.ac.cn, and follow the instructions.
