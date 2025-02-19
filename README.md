# mini-loop v5.0 - install Mojaloop v15.0.0 (using kubernetes v1.26 - 1.27 with k3s or microk8s) 
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FBashyPy%2Fmini-loop.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2FBashyPy%2Fmini-loop?ref=badge_shield)

## Description 
mini-loop is a simple, scripted/automated installer for Mojaloop (http://mojaloop.io) to enable demo, test, training and development of the Mojaloop switch and its associated environment. 

The goal is to make it realistic, easy, quick ,scriptable and cost effective to deploy Mojaloop in a variety of local or cloud environments. 
- realistic: running a full kubernetes stack , so you can do real-world tests
- easy : you only need to run 2 simple shell scripts
- quick : With a sufficiently configured linux instance and internet connection it should be possible to deploy and configure Mojaloop in approx 30 mins or less.
- scriptable: the scripts are easily callable from other scripts or from CI/CD tools
- cost effective : uses minimal resources, everything you need to test Mojaloop and nothing you don't need

Example environments include:-
- an x86_64 laptop or server running ubuntu 22 
- an x86_64 laptop or server running ubuntu 22 as a guest VM (say using virtualbox , prarallels, qemu or similar) 
- an appropriately sized x86_64 ubuntu 22 cloud instance running in any of the major cloud vendors
 
This project automates the instructions for mojaloop deployment in the mojaloop.io documentation at https://docs.mojaloop.io/documentation/deployment-guide/local-setup-linux.html. 

# Simplest install
Assuming you have an x86_64 environment running Ubuntu release 22 and are logged in as a non-root user (e.g. mluser)
```bash
login as mluser                                          # login as an existing non-root user e.g. mluser                                                  
git clone https://github.com/mojaloop/mini-loop.git       # clone the mini-loop scripts
sudo ./mini-loop/scripts/mini-loop-simple-install.sh     # install kubernetes and Mojaloop 
source $HOME/.bashrc                                     # or logout/log back in again to set kubernetes env
```

# Examples of other ways to install with more flexibility over kubernetes release, Mojaloop options etc

## Example #1 K3s, kubernetes 1.26 and Mojaloop 3PPI
Assuming you have an x86_64 environment running Ubuntu release 22 and are logged in as a non-root user (e.g. mluser)
```bash
login as mluser                                                       # login as an existing non-root user e.g. mluser
git clone https://github.com/mojaloop/mini-loop.git                    # clone the mini-loop scripts
sudo ./mini-loop/scripts/k8s-install.sh -m install -k k3s -v 1.26     # install and configure microk8s 
source $HOME/.bashrc                                                  # or logout/log back in again to set kubernetes env
./mini-loop/scripts/mojaloop-install.sh -m install_ml -o thirdparty   # deploy and configure the mojaloop helm chart -o deploys 3PPI too
```

## Example #2 Microk8s, kubernetes 1.27, Mojaloop 3PPI and bulk 
Assuming you have an x86_64 environment running Ubuntu release 22 and are logged in as a non-root user (e.g. mluser)
```bash
login as mluser                                                             # login as an existing non-root user e.g. mluser
git clone https://github.com/mojaloop/mini-loop.git                          # clone the mini-loop scripts
sudo ./mini-loop/scripts/k8s-install.sh -m install -k microk8s -v 1.27      # install and configure microk8s v1.25
source $HOME/.bashrc                                                        # or logout/log back in again to set kubernetes env
./mini-loop/scripts/mojaloop-install.sh -m install_ml  -o thirdparty,bulk   # deploy and configure the mojaloop helm chart 
```
## Some videos that might also help
For the recent DFS labs hack-a-thon I created a couple of videos that might also help you run mini-loop to install Mojaloop and also to run the TTK from your laptop
you can find these at 
- https://youtu.be/BpBbl2UuQxI (deploying Mojaloop)
- https://youtu.be/mGVZNAWDowg (running the Testing Toolkit against Mojaloop v15.0.0 from your laptop )

## Running the Testing Toolkit via ```helm test```
To ensure that your deployment is all up and running and Mojaloop functioning correctly you can run the testing Toolkit.
login to the system or VM where you install Mojaloop and as the non-root user (e.g. mluser)... 
```
helm test ml --logs 
```
For more detailed instructions on running the helm tests see "Testing Deployments" section of : https://github.com/mojaloop/helm

# Accessing Mojaloop from a laptop 
1. ensure that http / port 80 is accessible on the install system.  For instance if mini-loop has installed Mojaloop onto a VM in the cloud then it will be necessary to ensure that the cloud network security rules allow inbound traffic on port 80 to that VM (this is normally a default)
2. Add the following hosts to an entry for the external/public ip address of the install system or VM  in the **/etc/hosts file of the laptop** you are using. 

 For example if Mojaloop is installed on a cloud VM with a public IP of 192.168.56.10  Then add an entry to your **laptop's /etc/hosts** similar to ...
```
192.168.56.10 ml-api-adapter.local central-ledger.local account-lookup-service.local account-lookup-service-admin.local quoting-service.local central-settlement-service.local transaction-request-service.local central-settlement.local bulk-api-adapter.local moja-simulator.local sim-payerfsp.local sim-payeefsp.local sim-testfsp1.local sim-testfsp2.local sim-testfsp3.local sim-testfsp4.local mojaloop-simulators.local finance-portal.local operator-settlement.local settlement-management.local testing-toolkit.local testing-toolkit-specapi.local
```
You should now be able to browse or curl to Mojaloop url's e.g. http://central-ledger.local/health

Note: see [below](#modify-hosts-file-on-windows-10) for intructions on updating the hosts file on your windows 10 laptop 

## Running the Testing Toolkit From your laptop 
Assuming you have followed the instructions above for "accessing Mojaloop from a laptop" then from your laptop you should be able to browse to http://testing-toolkit.local and you should see the main page for the Testing Toolkit.

## simple example instructions for running testing toolkit for 3PPI on your laptop
To run testing toolkit against Mojaloop including 3PPI
- again with the hosts above added to your hosts file on your laptop you should be able to browse to http://testing-toolkit.local. 
- download the latest Mojaloop test collection to your laptop via curl -LO https://github.com/mojaloop/testing-toolkit-test-cases/archive/refs/tags/v15.0.0.zip, then unzip this and you will find a folder called **testing-toolkit-test-cases-15.0.0**
- import the **testing-toolkit-test-cases-15.0.0** into the testing toolkit web application by selecting test runner ->  collections manager -> import folder 
- still under collections manager select the boxes for testing-toolkit-test-cases.15.0.0->collections->hub->provisioning_thirdparty and also thirdparty
- close the collections manager and hit the run button to run the tests and examine the conversation 

For a good overview of the Testing Toolkit functionality please see the video (https://www.youtube.com/watch?v=xyC6Pd3zE9Y),
- Full documentation for the Testing Toolkit (https://github.com/mojaloop/ml-testing-toolkit/blob/master/documents/User-Guide-Mojaloop-Testing-Toolkit.md) 

## Prerequisites for running Mojaloop with mini-loop 
- a running  Ubuntu 22 OS on x86_64.
- sudo access
- non-root user (with bash shell)
- git installed (usually installed by default on Ubuntu  22) 
- min 8 or 16GB ram available  (current testing suggests 16 GB is needed if deploying 3PPI and Bulk options  ) 
- min 50GB storage available
- broadband internet connection from the ubuntu OS (for downloading helm charts and container images )

## Notes:
- Mojaloop code is developed to be deployable in a robust, highly available and highly secure fashion *BUT* the mini-loop deployment focusses on simplicity and hence is not deploying Mojaloop in a highly secure, highly available fashion.  The mini-loop deployment of Mojaloop is *NOT* intended for production purposes rather it enables:
  - trial: users new,expert and in-between to quickly run and access Mojaloop and its various features
  - test : the Mojaloop community do realistic testing of Mojaloop across a broad range of settings 
           From DFSPs working on integrating with Mojaloop to the Mojaloop core team where mini-loop is being used to enhance the quality of the Mojalop switch.
  - education: a powerful education "on-ramp" especially when used in conjunction with the testing toolkit and the Mojaloop tutorial.
  - demonstrations: an excellent platform for a range of Mojaloop demonstrations. This is due to the cost-effect and light-weight yet realistic (e.g. uses kubernetes) nature of the Mojaloop deployment 
  - simplicity: anyone can read the simple bash scripts and understand how kubernetes is being installed and Mojaloop is being configured and deployed
- the mini-loop scripts output messages to help guide your deployment , please pay attention to these messages
- .log and .err files are written to /tmp by default (but this is configurable) 
- each of the scripts has a -h flag to show parameters and give examples of how to use and customise
- The the scripts are intended to provide a starting point, for further customisation. For instance it should be easy for the user to 
  add extra nodes to the kubernetes cluster or as mentioned above to modify the mojaloop configuration etc.
- please note that the installation adds the /etc/hosts entries for the endpoints configured in the $ETC_DIR/miniloop_values.yaml file if you 
  use different values you will likely have to adjust the /etc/hosts endpoints and if you configure a domain name then /etc/hosts entries are not needed instead you need your domain name to resolve. 
- the mini-loop/install/mini-loop/util directory contains a few scripts and debugging tools that I find useful , I am not really maintaining these but you might find some of them handy as I do. 
- mini-loop v5.0 clones the 15.0.0 release of the Mojaloop helm charts to $HOME and then modifies the charts and values to facilitate deployment to kubernetes 1.25+ , the scripts then package and deploy these locally modified charts.  These local modifications are now negligable given the updates ion Mojaloop v15.0.0 , specifically the separation of the backend services to a seperate helm chart.

## modify hosts file on windows 10
1. open Notepad
2. Right click on Notepad and then Run as Administrator.
3. allow this app to make changes to your device? type Yes.
4. In Notepad, choose File then Open C:\Windows\System32\drivers\etc\hosts or click the address bar at the top and paste in the path and choose Enter.  If you don’t see the host file in the /etc directory then select All files from the File name: drop-down list, then click on the hosts file.
5. Add the IP from your VM or system and then add a host from the list of required hosts (see example below)
6. flush your DNS cache. Click the Windows button and search command prompt, in the cpmmand prompt:-
    ipconfig /flushdns
```
Note you can only have one host per line so on windows 10 your hosts file should look something like: 
192.168.56.10  ml-api-adapter.local 
192.168.56.10  central-ledger.local 
192.168.56.10  account-lookup-service.local 
192.168.56.10  account-lookup-service-admin.local 
192.168.56.10  quoting-service.local 
192.168.56.10  central-settlement-service.local 
192.168.56.10  transaction-request-service.local 
192.168.56.10  central-settlement.local 
192.168.56.10  bulk-api-adapter.local 
192.168.56.10  moja-simulator.local 
192.168.56.10  sim-payerfsp.local 
192.168.56.10  sim-payeefsp.local 
192.168.56.10  sim-testfsp1.local 
192.168.56.10  sim-testfsp2.local 
192.168.56.10  sim-testfsp3.local 
192.168.56.10  sim-testfsp4.local 
192.168.56.10  mojaloop-simulators.local 
192.168.56.10  finance-portal.local 
192.168.56.10  operator-settlement.local 
192.168.56.10  settlement-management.local 
192.168.56.10  testing-toolkit.local 
192.168.56.10  testing-toolkit-specapi.local
```

## known issues
1. mini-loop v5.0 deployment of Mojaloop has only been tested properly with ubuntu 20 and 22 
2. Not contradicting point 1. above BUT the k3s option should(?) work on linux distros other than Ubuntu. It has been tested on fedora36 where there are some unresolved issues around open files. [Note if you try mini-loop k3s on Linux other than Ubuntu please let me know (tdaly61@gmail.com) ]
3. The format of the logfiles is a bit of a mess at the moment, it is intended to tidy these up so that mini-loop scripts can be used very (cost) effectively in CI/CD pipelines across multiple configurations of Mojaloop and its environment such as kubernetes releases etc. 
4. the  -d option allows the user to configure the DNS domain name for their Mojaloop services. It is not as fully tested as other mini-loop features and I need to add some documentation on how to use and critically test this (note this is important for Mifos integration )
5. if we deploy with -o and then come and redeploy without -f or -o then thirdparty and bulk will again be deployed and this might not be intended 
6. there appear to be minor but annoying issues with Mojaloop v15.0.0 where helm test fails and some TTK tests also fail, see https://github.com/mojaloop/helm/issues. 
   It appears that these issues are not showing up for other members of the core team , including when deploying v15.0.0 to AWS managed kubernetes (EKS) , yet my testing 
   independent of mini-loop and depoying from the pre-packaged helm archive still shows the issues so I guess it is unclear if the issues are related to the k8s environment
   or perhaps the resources devoted to the Ubuntu OS.  Please use the Mojaloop slack to advise if you gather any more information on these minor issues.

## Noteble changes in mini-loop v5.0
1. updated to deploy Mojaloop v15.0.0 including deploying the now standard Mojaloop example backend services chart which deploys MySQL, Mongo, Kafka etc
2. updated to use and require kubernetes v1.26 or 1.27 
3. simplified the directory structure and the names of the scripts installing and configuring both kubernetes (k8s-install.sh) and Mojaloop (mojaloop-install.sh)
4. added the mini-loop-simple-install.sh script to further simplify access to Mojaloop !
5. added the -o option to allowed the deployment and configuration of 3PPI and Bulk Mojaloop charts
6. added a statics section for the deployment, reporting memory usage, deployment times and a few other basic stats.
7. updated the utils/test/miniloop-test.sh script to run mini-loop in a "loop". This is to illustrate how mini-loop can be used in CI/CD tools that are spun-up and spun-down with the aim of increasing test scope and reducing cloud costs for Mojaloop operators and developers.
8. added memory and disk space checks to make sure that there is enough memory and disk available for Mojaloop,  it is currently set at 8GB Ram , this is a case of trying to "fail fast" , the alternative is confusion as kubernetes tries and fails to fit all pods into insufficient memory. If you want to try running in < 8GB ram just modify the MIN_RAM=8 setting in the scripts/k8s.install.sh script.

## Notable changes in mini-loop v4.1
- dropped support for out of date kubernetes versions. mini-loop version 4.1 only allows use of kubernetes 1.24
- deploys Mojaloop v14.1.0 (where a fix in the TTK means Vijay's mobile emulator works)
- I don't have time to test redhat so dropped support of it for v4.1
- added a more current (short) apache 2.0 license to top level directory
- updates to the latest helm release 

## Notable changes in mini-loop v4.0
- dropped support for out of date kubernetes versions. mini-loop version 4.0 only allows use of kubernetes v1.22 - 1.24
- sucessfully tested redhat 8 using k3s 
- the mysql database chart is updated (no longer using percona) and is a seperate deploy with a password generated at deploy time (see ~/mini-loop/install/mini-loop/etc/mysql_values.yaml for the generated password)

## Notable changes in mini-loop v3.0
- inclusion of rancher k3s as an option. This is to enable mini-loop to function over more linux distributions as Microk8s is only installable with snapd and snapd seems very complex (to install) and unreliable on current linux distributions other than Ubuntu (where it seems to work nicely) 
- hardcoded the kubernetes releases that can be used (see above)
- tested the functionality and added in the instructions for remote access to the Mojaloop deployment and also the browser access to the Testing Toolkit


## Notable changes in mini-loop v2.0
- re-worked all the scripts to function in any running ubuntu enviroment as described above
- updated to default to Mojaloop v13.1.1
- updated K8s version to v1.20.x
- removed all automation that created the ubuntu enviromnent, this is now left to the user. It became obvious that the utility of mini-loop install would be far 
  improved by making this change
- removed the script to run the testing toolkit, currently `helm test` is utilised and the user guided as how to run helm test from the mini-loop scripts

## FAQ
1. I think it installed correctly, but how do I verify that everything is working?
   The mini-loop scripts test several of the mojaloop API /health endpoints and will report an errors.  Also `helm test` is your friend!  
   See the instructions at the end of the mojaloop deployment for instructions on running the tests and also refer to the testing section of 
   https://github.com/mojaloop/helm 

2. The best way to run on a windows laptop is to provision an Ubuntu 20 or 22 virtual machine using one of the popular Hypervisors available today (HyperV, VirtualBox, UTM etc) 

## License
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FBashyPy%2Fmini-loop.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2FBashyPy%2Fmini-loop?ref=badge_large)