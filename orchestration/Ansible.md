# Ansible

## Python virtualenv

Useful for when you need a specific verison of Ansible (or not wanting to install globally)

**Pre-requisite**: python-virtualenv [https://virtualenv.pypa.io/en/stable/]()

	pip install virtualenv

Installing required version

	virtualenv ./ansible-env
	./ansible-env/bin/pip install ansible==1.9.3

(omit the ==1.9.3 for latest version)

