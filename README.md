# ngnix-lb
Deploy a golang sample application to the application nodes and front with a load balanced nginx webserver

## Tool to deploy a golang sample application to the application nodes and front with a load balanced nginx webserver

- ansible 2.4.0.0 or greater
- python 3.4 or greater
- boto3 1.4.4 or greater
- botocore 1.5.88 or greater
- aws-cli 1.11.117 or greater
- jinja2 2.9.6 or greater

Assuming that python3 may be run as an OS command to invoke python3 interpreter.

> Note - In theory python3 up to 3.3 is not supported by jinja2

## How To

### Install Ansible

Ref: <http://docs.ansible.com/ansible/intro_installation.html>

#### IN a Python Virtual Env

```
$ cd ansible
$ mkvirtualenv ngnix-with-lb
$ pip install -r requirements.txt
```

#### With Brew
```
$ brew install ansible
```

#### ...or with Pip
```
$ pip install ansible
```

### Recommendations

Ensure that the ansible installation is able to run under python3 and is able to import boto3.    

The following seems to work under MacOS 10.12.6 / Darwin 16.7.0.

Use pip install python3 and ensure that ansible-playbook installation is at /usr/local/bin/ansible-playbook.

```
$ pip show boto3
Name: boto3
Version: 1.4.4
$
$ echo $PYTHONPATH 
/usr/local/lib/python3.6/site-packages

```

### Mandatory

Once you have devised the python version (preferably > 3.4) which is right for your deployment, you must ensure that its local installation directory is in agreement with
```
group_vars/all/configuration.yml: ansible_python_interpreter: "/usr/local/bin/python"
```

### Configure Authentication Across AWS Accounts

To add or destroy resources in a non-default account, ensure that your current workstation has defined a credentials file, of the following structure

`~/.aws/credentials`

```
[default]
aws_access_key_id = XXXXXX
aws_secret_access_key = XXXXXX
region = eu-west-1

[ngnix_lb_example]
aws_access_key_id = XXXXXX
aws_secret_access_key = XXXXXX
```

**Note**: The convention nginx_lb_example must be adhered to as the label for test in the expected account.    


### Deploy an Environment

From your clone of the latest repo, go the role which needs to deploy next. For example to deploy the Vpc network building role into the test stack

```
$ cd ngnix-with-lb/ansible/
$ ansible-playbook create.yml -i inventory/<environment>/hosts.ini
```
For example, to deploy the test environment, if using venv to invoke:
```
ansible-playbook -i inventory/test/hosts.ini create.yml 
```

or if having installed from brew or pip, so as to invoke ansible via a system command, then
```
$ python3 /usr/local/bin/ansible-playbook -i inventory/test/hosts.ini create.yml 
```

Depending on your system python it may be possible to leave out the python3 executable.

Depending on the latest contents of ngnix-with-lb-automation/ansible/create.yml the necessary roles will be invoked. Currently the role at ngnix-with-lb-automation/ansible/roles/network contains a create.yml task which is the first one to be invoked.

### Destroy an Environment

```
$ ansible-playbook destroy.yml -i inventory/<environment>/hosts.ini
```
For example, to destroy the test environment, if using venv to invoke:
```
$ ansible-playbook destroy.yml -i inventory/<environment>/hosts.ini
```
or if having installed from brew or pip, so as to invoke ansible via a system command, then
```
$ python3 /usr/local/bin/ansible-playbook -i inventory/test/hosts.ini destroy.yml 
```

#### WARNING - For this project, destroy means 'tear down to the ground', i.e. no AWS resources for the solution are left behind.

## TODO

Initially the code is to be tested in a stage named
```
test
```

Introduce extra stages such as dev, perf etc by discussion with the wider team. Consider the role of a CD tool to push builds automatically once feature testing has been completed in the pre-prod environments.

## Deployment responsibilities

- When tearing down or recreating environments for a specific stack ensure that all users are alerted and that the change is planned
- Consideration must be given to potential disruption depending on the extent of change determined by the roles selected

