---
title: AceCloud Documentation
layout: default
name: AceCloud Documentation
description: Getting Started with AceCloud
---


## Sign Up to AWS

* AceCloud uses your existing Amazon Web Services acount for its compute power. If you don't yet have an AWS account, register <a href="http://aws.amazon.com/">here</a>. You will need to supply credit card details for billing purposes.


## Install the Client Software

For the full AceCloud experience, including automatic execution on EC2 and transparent scaling to large numbers of simulations, client software must be installed on your workstation and configured to use your Amazon account. The AceCloud client is part of Acellera's <a href="https://www.htmd.org">High Throughput Molecular Dynamics toolkit</a>. Installation instructions can be found <a href="https://www.htmd.org/download.html">here</a>.

## Configuration

AceCloud requires configuration before it can be used. Once he software is installed, run the command:
```acecloud --setup```
This will open two browser windows. In the first, complete any login prompt and then subscribe to the <a href="https://aws.amazon.com/marketplace/pp/B01N3SBK3Z">AceCloud Amazon Marketplace product</a>.
Follow the instructions in the termination window to obtain AWS access credentials from the page opened in the second window.
   
## Using the AceCloud Client

* The AceCloud client runs ACEMD and Gromacs simulations on Amazon AWS resources. It performs all file copying and VM instantiation operations automatically - once configured with AWS access credentials the user does not need to interact directly with the AWS Console.
* The syntax for all AceCloud operations is given by
```acemd --help```

### Running simulations

* AceCloud can run any ACEMD or Gromacs version 5 simulation, subject to the following restrictions:
  * All necessary files put reside in the submission directory or in the a sub-directory thereof.
  * The input file for ACEMD simulations must be called "input"
  * For Gromacs files the input file must be called topol.tpr


* To submit a simulation to AceCloud, first prepare the input in a directory and then run
```acecloud --submit  [group/name] [directory-name]```
where `[group/name]' is a two-level identifier for the simulation, for example `project2/run10` 
* The submitted simulation is named using the absolute path of the submission directory. Simulation results will be retrieved back to the submission directory, so multiple submissions from the same directory are not permitted.
* Simulations can be grouped into projects using the optional argument ```--project[project-name]```

### Checking simulation progress

* Check the progress of simulations with
```acecloud --status```
This summaries all simulations, grouped by project. Simulations will be in one of the following states:
  * PENDING Simulation has yet to start running
  * RUNNING Simulation is in progress
  * COMPLETED Simulation has finished running and results are ready to retrieve
  * PREEMPTED Simulation has terminated early because of spot market pre-emption of the AWS instance.


### Retrieving simulation results

* The output of a simulation becomes available once the job is complete```acecloud --retrieve [group/name]```
* Output is automatically placed in the directory from which the input files were originally obtained. This will be recreated if necessary.



### Important Notes

* We recommend running short (<3day) simulations with frequent restarts, rather than one long simulation. There are several reasons for this:
  * AceCloud uses <a href="http://aws.amazon.com/ec2/purchasing-options/spot-instances/">AWS Spot Instances</a>. Spot instance pricing is variable depending on demand. Rarely, in times of peak demand the spot price may exceed the default AceCloud bid price of 0.70$ and running simulations may be aborted and results lost.  Current spot pricing can be displayed with ```acecloud --print-spots```

The price that you pay is the current spot price, not the price you have set as the maximum bid.
  * The total ouput of an AceCloud simulation is limited to 1GB. No check is made that this has been exceeded, please ensure that you have configured the simulation correctly to not exceed this.
* AWS accounts have a default limit of 10 concurrent spot instances. If you need to run more instances, higher limits can be requested per-region via the AWS Console <a href="https://console.aws.amazon.com/ec2/v2/home?region=us-west-2#Limits">here</a>
