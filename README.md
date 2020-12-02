# ansible-django

A repo of Ansible playbooks to deploy a Django web app on Digital Ocean. The Django deployment uses gunicorn, nginx and an sqlite database.

## Create Droplet, link to domain name

Create a Digital Ocean droplet with Ubuntu 20.04. Link domain name to droplet IP address. The DNS changeover takes the longest, so do this first thing.

## Create virtual environment, install requirements.txt

```
python -m venv venv
source venv/bin/activate
python -m pip install -r requirements.txt
ansible --version
# ansible 2.10.3
```

## Copy vars and hosts files

```
cp vars/default_example.yml vars/default.yml
cp hosts_example hosts
```

fill in ```vars/default.yml``` and ```hosts``` files.

## Run initial server setup playbook

```
# verify the IP address of the server
ansible -i hosts --list-hosts all
# verify server connection
ansible -i hosts -m ping all
```

If this is the first time communicating with the server, it will ask you to verify the host key. Answer ```yes```.

If this doesn't work. Try to log into the server with SSH. May need to remove a known host ```ssh-keygen -f "/home/peter/.ssh/known_hosts" -R "XX.XXXX.XX"```

```
ansible-playbook -i hosts server.yml
```

Use SSH to log into the server as the non-root sudo user.

```
ssh peter@XXX.XXX.XX.XX
```

## Run the install django playbook

```
ansible-playbook -i hosts django.yml
```

Now you can test the Django app using Django's built-in server. Log into the server change into your app's directory and start the development server. Don't forget to open the ufw firewall to open port 8000.

```
ssh peter@XXX.XXX.XXXX
cd app-dir
source venv/bin/activate
sudo ufw allow 8000
python manage.py runserver 0.0.0.0:8000
# browse to https://XXX.XXX.XXXX:8000
sudo ufw deny 8000
```

## Run the gunicorn playbook

```
ansible-playbook -i hosts gunicorn.yml
```

## Run the nginx playbook

```
ansible-playbook -i hosts nginx.yml
```
