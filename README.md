# Google Cloud Platform OpenShift TestDrive

The following describes how to create the GCP OpenShift TestDrive.

There are a number of customizations that must be done prior to executing the scripts.  
 
####ansible/gce.ini
* In order to run ansible with gce, authorization settings must first be defined in **ansible/gce.ini**

```
# If you are not going to use a 'secrets.py' file, you can set the necessary
# authorization parameters here.

gce_service_account_email_address='email address for the service account'
gce_service_account_pem_file_path='local pem path/file for the service account'
gce_project_id='name of the project to execute against'
```

####ansible/setup_vars.yml
* Users for the target system must be defined for ansible in **ansible/setup_vars.yml**.  Typcally the same user.
```
### Common Vars
ansible_ssh_user: 
remote_user: 
```
* The following variable is used in the generation of the openshift-hosts file.  `UNIQUE_SUBDOMAIN_PREFIX` will be replaced at a later step based upon the generated sub DNS from GCP.
```
subdomain_base: "UNIQUE_SUBDOMAIN_PREFIX.{{ subdomain_base_short }}.testdrive.openshift.com"
```

####ansible/setup.yml
* All `hosts` entries in **ansible/setup.yml** have to match the desired hosts output from `gce.py`

```
hosts:
```

openshift.jinja


## Running the Scripts


###Step 1 - Preparing the environment for the OpenShift install
This step was only used to initially create disk images to be  used as the 'base vm' for the OpenShift installation in step 2.
```
ansible-playbook --private-key=<local key file> -i gce.py setup.yml
```
>**Note:** It is possible the above will fail without the full path for  gce.py


###Step 2 - Running the GCloud Installer
This command will 
1. execute some configuration changes due to the random generated subdomain
2. run the OpenShift installer
3. create a developer user with random password
```
gcloud --project openshift-test-drive deployment-manager deployments create <unique-gcp-instance-name> --config install.jinja
```
