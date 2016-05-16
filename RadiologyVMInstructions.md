# Instructions for Creating a VM to Test the Radiology Module

##Build box with OpenMRS and dcm4chee

```
git clone git@github.com:teleivo/puppet-openmrs-radiologydcm4chee.git
cd puppet-openmrs-radiologydcm4chee
vagrant up
```

###Connect to OpenMRS

`http://localhost:8080/`

###Follow setup instructions

[https://github.com/teleivo/puppet-openmrs-radiologydcm4chee/wiki/OpenMRS-Installation-Wizard]()

On step 1: database name, username, and password are all `openmrs`

###Shut Down Box and Create Custom Box

```
vagrant halt
vagrant package --output openmrs-radiologydcm4chee.box
vagrant box add openmrs-radiologydcm4chee.box --name openmrs-radiologydcm4chee
```

##Create Box with Radiology Dependencies Installed

```
cd ..
mkdir puppet-openmrs-radiology-base
cd puppet-openmrs-radiology-base
vagrant init openmrs-radiologydcm4chee
```

###Edit Vagrantfile to Include Port Redirects

Open Vagrantfile in your editor

Copy the #Ports section from the Vagrantfile in the puppet-openmrs-radioologydcm4chee directory and paste it in to the Vagrantfile that you are editing. (There are comments about port forwarding in the Vagrant file you are editing. Paste the #Ports section there.)

This is what you are copying:

```
# Ports
  # tomcat port (openmrs)
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  # dcm4chee jboss/tomcat port
  config.vm.network "forwarded_port", guest: 8081, host: 8081
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  # dcm4chee dicom server port
  config.vm.network "forwarded_port", guest: 11112, host: 8112
  # openmrs-module-radiologydcm4chee dicom server port
  config.vm.network "forwarded_port", guest: 11114, host: 8114
  # dcm4chee hl7 server port
  config.vm.network "forwarded_port", guest: 2575, host: 8115
  # mysql port
  config.vm.network "forwarded_port", guest: 3306, host: 3306
```

Save your changes.

###Install Radiology Dependencies


####Download Module Dependencies

Download the following modules from [https://modules.openmrs.org/#/search]()

1.    providermanagement-2.3
2.    uiframework-omod-3.4
3.    uilibrary-1.5
4.    emrapi-omod-1.13
5.    event-2.2.1
6.    reporting-0.9.8.1
7.    htmlwidgets-1.7.0
8.    calculation-1.1
9.    serialization.xstream-0.2.9
10.    metadatasharing-1.1.9
11.    metadatamapping-1.0.1

####Install Module Dependencies

```
vagrant up
```

Connect to [http://localhost:8080/]() and log in.

Go to the `Administration` tab

Select `Manage Modules`

Add all 11 modules listed above using the `Add or Upgrade Module` button. Not all modules will start immediately - once all modules are added, press the `Start All` button.

*Do not install the Radiology Module at this point*

Log out

###Shut Down Box and Create Custom Box

```
vagrant halt
vagrant package --output openmrs-radiology-base.box
vagrant box add openmrs-radiology-base.box --name openmrs-radiologybase
```

This box will be used to create a test box anytime you need to test a new version of the Radiology module.

##Create a Test Box for the Radiology Module

```
cd ..
mkdir puppet-openmrs-radiology-test
cd puppet-openmrs-radiology-test
vagrant init openmrs-radiology-base
```

###Edit Vagrantfile to Include Port Redirects

Open Vagrantfile in your editor

Copy the #Ports section from the Vagrantfile in the puppet-openmrs-radioology-base directory and paste it in to the Vagrantfile that you are editing. (There are comments about port forwarding in the Vagrant file you are editing. Paste the #Ports section there.)

This is what you are copying:

```
# Ports
  # tomcat port (openmrs)
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  # dcm4chee jboss/tomcat port
  config.vm.network "forwarded_port", guest: 8081, host: 8081
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  # dcm4chee dicom server port
  config.vm.network "forwarded_port", guest: 11112, host: 8112
  # openmrs-module-radiologydcm4chee dicom server port
  config.vm.network "forwarded_port", guest: 11114, host: 8114
  # dcm4chee hl7 server port
  config.vm.network "forwarded_port", guest: 2575, host: 8115
  # mysql port
  config.vm.network "forwarded_port", guest: 3306, host: 3306
```

Save your changes.

###Build and Install Radiology Module

####Build Radiology Module

```
cd openmrs-module-radiology
maven clean package
```

####Install Radiology Module

```
vagrant up
```

Connect to [http://localhost:8080/]() and log in.

Go to the `Administration` tab

Select `Manage Modules`

Add the Radiology Module using the `Add or Upgrade Module` button. It will be in `openmrs-module-radiology/omod/target/`

####Configure Radiology Module

[https://wiki.openmrs.org/display/docs/Radiology+Module]()

[https://github.com/teleivo/puppet-openmrs-radiologydcm4chee/wiki/Dcm4chee-Configuration]()

####Troubleshooting

[https://github.com/teleivo/puppet-openmrs-radiologydcm4chee/wiki/Troubleshooting]()

## Notes: ##

###Do not use Windows Command

Perform all command-line commands in Git Bash or Cygwin

[https://enrise.com/2012/12/git-and-vagrant-in-a-windows-environment/]()

[https://hplgit.github.io/vagrantbox/doc/pub/._vagrant_box002.html]()

###Do not use Virtual Box to start, stop or suspend machines

Starting: Use `vagrant up`

Stopping: Use `vagrant halt`

Suspending: Use `vagrant suspend`

###Do not delete machines in Virtual Box

Use `vagrant destroy -f`




