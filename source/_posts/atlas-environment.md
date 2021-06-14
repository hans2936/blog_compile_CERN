---
title: ATLAS environment
date: 2018-06-05 23:04:55
tags: 
- ATLAS Basic
- Athena
categories: Tutorials
---

## Setup ATLAS Environment:

```
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
alias setupATLAS='source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh'
setupATLAS
```
Then, setup Root:
```
lsetup root
```
<!--more-->

## Setup GitLab
Get a ssh Key here : https://gitlab.cern.ch/help/ssh/README
Add the ssh Key here : https://gitlab.cern.ch/profile/keys
Start a new project here : https://gitlab.cern.ch/dashboard/projects
Basic commands here : https://gitlab.cern.ch/help/gitlab-basics/README.md
Some basic commands:
```
git clone SSH_PATH
git checkout BRANCH_OR_TAG
git pull
git add --all
git commit -am "something"
git push origin BRANCH
```

## Certificat, VO

"New Grid User Certificate" in https://ca.cern.ch/ca/

Setup certificate on hosts:
```
openssl pkcs12 -in usercert.p12 -nokeys -clcerts -out ~/.globus/usercert.pem
openssl pkcs12 -in usercert.p12 -nocerts  -out ~/.globus/userkey.pem
chmod 400 ~/.globus/userkey.pem
chmod 444 ~/.globus/usercert.pem
voms-proxy-init voms atlas
```
ATLAS VO page (request here): https://voms2.cern.ch:8443/voms/atlas/user/home.action

## Rucio, AMI

Transfer request page: https://rucio-ui.cern.ch/r2d2/request
Rucio commands:
```
voms-proxy-init -voms atlas
lsetup rucio
rucio list-dids DATASETNAME
rucio list-file-replicas DATASETNAME
rucio get DATASETNAME
rucio list-rules --file FILETYPE(e.g. data16_13TeV):FILEPATH
```

AMI page: https://ami.in2p3.fr/
pyAMI commands: https://ami.in2p3.fr/pyAMI/

```
voms-proxy-init -voms atlas
lsetup pyami
ami show dataset info  DATASETNAME
```

## How to read Ntuple from CERN eos disk 
(see https://confluence.desy.de/display/IS/How+to+use+Grid+resources+in+batch+jobs)

```
voms-proxy-init -voms atlas
lsetup xrootd rucio root
root -l
root [0] TFile *mytuple =TFile::Open("root://eosatlas.cern.ch//eos/atlas/atlascerngroupdisk/phys-higgs/*/myntuple.root")
```
While submitting jobs in condor (validated on NAF), in the job-file, one should type
```
export X509_USER_PROXY=${PATHTOPROXY}
echo "#!/bin/expect" > voms.sh
echo "spawn voms-proxy-init -voms atlas" >> voms.sh
echo "expect \"Enter GRID pass phrase for this identity:\""  >> voms.sh
echo "send \"your_proxy_passwd\r\"" >> voms.sh
echo "interact" >> voms.sh
expect voms.sh
lsetup xrootd rucio root
```
and in condor submit-file, type
```
transfer_input_files  = ${PATHTOPROXY}
```
Where the `$PATHTOPROXY` is default to be `$X509_USER_PROXY` in host, but need to be copied to a readable path, e.g. `cp /tmp/x509up_u2xxxx ${HOME}/nfs/proxyfile/`

## Muont /cvmfs/atlas.cern.ch on you computer (Mac)
Download and setup Fuse for macOS: [OSXFuse](https://osxfuse.github.io)
Download and setup CernVM-FS: [CernVM-FS](https://cernvm.cern.ch/portal/filesystem/downloads)

```
echo "CVMFS_HTTP_PROXY=DIRECT" | sudo tee -a /etc/cvmfs/default.local
sudo mkdir /cvmfs/atlas.cern.ch
sudo mount -t cvmfs atlas.cern.ch /cvmfs/atlas.cern.ch
sudo mkdir /cvmfs/atlas-condb.cern.ch
sudo mount -t cvmfs atlas-condb.cern.ch /cvmfs/atlas-condb.cern.ch/
sudo mkdir /cvmfs/sft.cern.ch
sudo mount -t cvmfs sft.cern.ch /cvmfs/sft.cern.ch/
```

Then you can try to "setupATLAS" now. Currently athena is not supported by MacOS, only rcsetup, root etc.

But to use Docker, /cvmfs should be set up.

## Use Docker to run Athena on your computer (Mac)
Docker: https://docs.docker.com/docker-for-mac/
Old twiki for Docker with Athena: https://twiki.cern.ch/twiki/bin/viewauth/AtlasComputing/AthenaMacDockerSetup

Recommended: Webpage for Docker with Athena: https://atlassoftwaredocs.web.cern.ch/athena/mac-setup/

+ Try if Docker is set up correctly
```
docker run hello-world
```
+ Change the setting of XQuartz, and restart
<div style="width: 60%">![XQuartz setting](../../../../../shhan/images/XQuartz.jpg)</div>
Then add your IP as a new host
```
ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}') # Adapt en0 to the active interface
xhost + ${ip}
```

+ Add /cvmfs as a shared folder of Docker 
![Docker setting](../../../../../shhan/images/Docker.jpg)

+ Download and run external cvmfs package
```
docker pull atlas/atlas_external_cvmfs
docker run -i -t -v /cvmfs:/cvmfs -v $HOME:$HOME -e DISPLAY=${ip}:0 atlas/atlas_external_cvmfs
```
You can finally "setupATLAS", "asetup" now with the ATLAS softwares.

+ Save the image after making a change
While `atlas/atlas_external_cvmfs` is runing, open a new window and:
```
docker ps # to known the container ID
docker commit ${ContainerID} atlas:v1 # atlas:v1 can be replaced with any image:tag names
docker images # check what you just commited
```
Then for next time you can login the edited image by: 
```
docker run -i -t -v /cvmfs:/cvmfs -v $HOME:$HOME -e DISPLAY=${ip}:0 atlas:v1
```
To control the docker images/system, one can use `docker ps`, `docker images`, `docker system df`, `docker system prune` etc.

## Use Virtual machine to run Athena on your computer (Mac)
CERNVM: https://atlas-vp1.web.cern.ch/atlas-vp1/blog/tutorial/running-vp1-on-any-system-in-a-virtual-machine/
Follow this page until “SetupATLAS”, so you have a linux virtual machine in ATLAS environment
Administrator (in case you want to use sodo): account : atlas, password: boson

Comparing to Docker, it's not easy to control files I/O using Virtual Machine

## ATLAS Softwares
### ATLAS toturial meetings
Find intested slides/records in [tutorial meetings' category](https://indico.cern.ch/category/397/)

### AnalysisBase toturial
Creating Event-Loop package: [SoftwareTutorialxAODAnalysisInROOT](https://twiki.cern.ch/twiki/bin/viewauth/AtlasComputing/SoftwareTutorialxAODAnalysisInROOT)
Analysis Base tutorial: [ABtutorial](https://atlassoftwaredocs.web.cern.ch/ABtutorial/)
ATLAS software tutorial twiki: [SoftwareTutorialSoftwareBasics](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialSoftwareBasics)

### Make CmakeLists
CmakeLists.txt of Athena based package: [HGamTools/CMakeLists.txt](https://gitlab.cern.ch/atlas-hgam-sw/HGamCore/blob/master/HGamTools/CMakeLists.txt)
CmakeLists.txt of local code: [Purity_2D/CMakeLists.txt](https://gitlab.cern.ch/shhan/Purity_2D/blob/master/CMakeLists.txt)

### ATLAS Event Display
Latest event: http://atlas-live.cern.ch/
Public plots: [EventDisplayPublicResults](https://twiki.cern.ch/twiki/bin/view/AtlasPublic/EventDisplayPublicResults)
Event Pickup (JML and ESD): [Atlantis](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/Atlantis)
Atlantis: download latest version here (http://atlantis.web.cern.ch/atlantis/), run in a system where Java is available
VP1:
```
asetup 20.7.9.2,AtlasProduction, here
vp1 DATASET.ESD.root
```
For VP1 it's better to use the Virtual machine (or Docker)
