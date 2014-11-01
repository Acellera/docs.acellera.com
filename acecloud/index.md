---
title: AceCloud Documentation
layout: default
name: AceCloud Documentation
description: Getting Started with AceCloud
---

# Get Started with AceCloud

## Sign Up

* AceCloud uses your existing Amazon Web Services acount for its compute power. If you don't yet have an AWS account, register <a href="http://aws.amazon.com/">here</a>. You will need to supply credit card details for billing purposes.
* Next, subscribe to the AceCloud product in the <a href="https://aws.amazon.com/marketplace">Amazon Marketplace</a>.
* Finally, obtain access keys AWS account <a href="https://console.aws.amazon.com/iam/home?nc2=h_m_sc#security_credential">here</a>. If you use IAM, create credentials that have full access to EC2 and S3.

## Install Client Software

* Download the <a href="cloud.acellera.com/acecloud/acecloud-client.tgz">AceCloud Linux Client</a>
* Unpack the tarball to a suitable location with
```tar -zxvf acecloud-client.tgz```
this will create a directory called acecloud
* Add the program to the shell's PATH with
```export PATH=$PWD/acecloud/bin:$PATH```
* Configure the client with your AWS access keys with
```acemd --configure auth XXXXXXKEY YYYYYYSECRET```

## Using the AceCloud Client

* The AceCloud client runs ACEMD and Gromacs simulations on Amazon AWS resources. It performs all file copying and VM instantiation operations automatically - once configured with AWS access credentials the user does not need to interact directly with the AWS Console.
* The AceCloud client is called ```acemd```. If you already have ACEMD installed, AceCloud will automatically run it when required provided the environment variable ACEMD_HOME is set correctly. To check if ACEMD is correctly configured, see the output of 
```acemd --configure -print``` 
* The syntax for all AceCloud operations is given by
```acemd --help```

### Running simulations

* AceCloud can run any ACEMD or Gromacs version 5 simulation, subject to the following restrictions:
  * All necessary files put reside in the submission directory or in the a sub-directory thereof.
  * The input file for ACEMD simulations must be called "input"
  * For Gromacs files the input file must be called topol.tpr


* To submit a simulation to AceCloud, first prepare the input in a directory and then run
```acemd --submit  [directory-name]```
* The submitted simulation is named using the absolute path of the submission directory. Simulation results will be retrieved back to the submission directory, so multiple submissions from the same directory are not permitted.
* Simulations can be grouped into projects using the optional argument ```--project[project-name]```

### Checking simulation progress

* Check the progress of simulations with
```acemd --status```
This summaries all simulations, grouped by project. Simulations will be in one of the following states:
  * QUEUED Simulation has yet to start running
  * RUNNING Simulation is in progress
  * COMPLETED Simulation has finished running and results are ready to retrieve
  * ABORTED Simulation failed. Most likely the Amazon instance was preempted. See Notes below.
* To see the state of all individual simulations use
```acemd --status --long```


### Retrieving simulation results

* The output of a simulation becomes available once the job is complete```acemd --retrieve```
* Output is automatically placed in the directory from which the input files were originally obtained. This will be recreated if necessary.
* The default behaviour is to make a single pass and then exit. If run with the flag --loop the client will run indefinitely and retrieve files as soon as they become available. It can be terminated with Ctrl-C. 



### Important Notes

* We recommend running short (<3day) simulations with frequent restarts, rather than one long simulation. There are several reasons for this:
  * The output of an AceCloud job is not available until it has completed. When running from new model, for example, we recommend testing locally or with a short run before submitting a long simulation
  * AceCloud uses <a href="http://aws.amazon.com/ec2/purchasing-options/spot-instances/">AWS Spot Instances</a>. Spot instance pricing is variable depending on demand. Rarely, in times of peak demand the spot price may exceed the default AceCloud bid price of 0.70$ and running simulations may be aborted and results lost. 

The bid price is set with
```acemd --configure rate [rate in USD]```
The price that you pay is the current spot price, not the price you have set as the maximum bid.

  * The total ouput of an AceCloud simulation is limited to 1GB. No check is made that this has been exceeded, please ensure that you have configured the simulation correctly to not exceed this.


* AWS accounts have a default limit of 10 concurrent spot instances. If you need to run more instances, higher limits can be requested per-region via the AWS Console <a href="https://console.aws.amazon.com/ec2/v2/home?region=us-west-2#Limits">here</a>
