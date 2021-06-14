---
title: Computing/Hardware Logbook
date: 2018-01-01 19:51:36
tags: 
- Hardware
- Computing
categories: Tutorials
---
# ADCoS Shift
## ADCoS Shift links
ADCoS links summary: https://adcos.web.cern.ch/adcos/For_ADCoS_Shifters/
ADCVCR control room: https://twiki.cern.ch/twiki/bin/view/AtlasComputing/ADCVirtualControlRoom
Jabber services: https://www.jabberes.org/servers/
Room: adcvcr
Server: conference.chat.uio.no
Password: point1

<!--more-->

## ADCoS Shift tasks
Most links are in this [Links Summary](https://adcos.web.cern.ch/adcos/For_ADCoS_Shifters/)
Always follow [Official Twiki](https://twiki.cern.ch/twiki/bin/viewauth/AtlasComputing/ADCoS).

- Check Known problems, previous GGUS/ELog, AGIS Calendar
- DDM Dashboard for transfer/stagging/delete problem.
  * Usually GGUS
  * JIRA for insufficient space on LOCALGROUPDISK
  * Not a issue for space not enough on SCRATCHDISK
- Central Services
  * Usually ELog
  * very urgent GGUS if Kibana is down.
- Check Squid down-time (GGUS)
- T0 -> Tape (Elog, is_open=false), T0 -> Grid (ELog)
- Problematic tasks (JIRA)
- [High transfer rate](https://bigpanda.cern.ch/dash/production/?cloudview=region) and [High transfer number](https://bigpanda.cern.ch/jobs/?jobtype=production) (ELog)

<!--more-->
# Test-beam
## EUTelescrope
Home Page: http://eutelescope.web.cern.ch

Setup on DESY NAF disk:
```
setupATLAS
lsetup "root 6.10.04-x86_64-slc6-gcc62-opt"
export ILCSOFT=/nfs/dust/atlas/user/hans/EUTelescrope_desy
cd $ILCSOFT
git clone -b dev-desynaf https://github.com/eutelescope/ilcinstall $ILCSOFT/ilcinstall
cd $ILCSOFT/ilcinstall
./ilcsoft-install -i examples/eutelescope/release-standalone.cfg
```
