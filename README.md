# Ansible automation – Student Portal Web Tier

**Student:** Ishwari Mahajan  
**PRN:** 202301040173  
**Branch:** Computer Engineering

## Scenario
This lab automates the configuration of a small student-portal web tier on an Ubuntu/Debian host. Ansible installs Nginx, creates the document root, renders a Jinja2 landing page, configures a virtual host, enables the service and validates the HTTP response.

## Files
- `inventory.ini` – target host and connection settings
- `site.yml` – idempotent configuration/deployment playbook
- `group_vars/web.yml` – environment variables
- `templates/index.html.j2` – parameterized landing page
- `ansible.cfg` – project-local defaults
- `requirements.txt` – controller dependency note

## Prerequisites
- Ubuntu 22.04/24.04 (WSL2 or VM) with sudo access
- Python 3 available
- Internet access for package installation
- Ansible installed on the control node

## Setup and run
```bash
sudo apt update
sudo apt install -y ansible
cd ansible-project
ansible --version
ansible-inventory -i inventory.ini --list
ansible-playbook -i inventory.ini site.yml --syntax-check
ansible-playbook -i inventory.ini site.yml --check --diff
ansible-playbook -i inventory.ini site.yml --diff
```
The playbook uses `localhost` with Ansible's local connection so the exercise can be completed on one Ubuntu machine. It requires privilege escalation because it installs a system package and writes under `/etc` and `/var/www`.

## Verify
```bash
sudo systemctl status nginx --no-pager
curl -I http://127.0.0.1/
curl http://127.0.0.1/ | grep -E "MITAOE Student Portal Demo|Deployment successful"
```
Open `http://127.0.0.1/` in a browser.

## Idempotency test
Run the playbook a second time:
```bash
ansible-playbook -i inventory.ini site.yml
```
Expected result: Nginx remains running and already-correct files/configuration are not changed. A second run should report fewer or zero changes (unless package metadata or the environment has changed).

## Important notes
- Capture your own terminal and browser screenshots after running these commands. Do not submit example output as if it were your machine's execution.
- If port 80 is already occupied, stop the conflicting service or change `http_port` consistently in `group_vars/web.yml` and use the same port in verification.
- For a multi-server deployment, replace `localhost` with the managed host's DNS/IP and configure SSH user/key in inventory. Do not store passwords or private keys in this repository.
