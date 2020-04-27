## Automated, self-updating, Pi-hole stack using unbound as recursive DNS server

This Ansible playbook installs a self updating Pi-hole stack from scratch:
- You can choose between a Public Pi-hole or a Personal/Private one
- Both use unbound as recursive DNS server instead of public upstream DNS servers
- Both deploy (by default) the uber adlist/blocklist by OISD.nl  
- Both add my (auto-updated) whitelist for your websurfing convenience
- If deploying a public Pi-hole, it includes LetsEncrypt to secure the admin interface with valid certificate over HTTPS
- If deploying a public Pi-hole, basic protection against DNS Amplification, SSH and bruteforce attacks is included using fail2ban
- The entire stack is based on Docker (so OpenVZ VPSes are NOT supported)

Tested on Ubuntu Server 18.04.2 LTS (Minimal) with Ansible 2.8 on Scaleway.com

## Proposed Usecase
I designed this playbook for my own personal usecase and decided to make it public for those of you who have the same usecase and/or want to (re)use specific parts from my playbook.  
I'm willing to learn how to make this playbook more robust and generic. Therefore I am accepting pull requests :)  
I highly recommend checking the playbook before running so that you know what will be installed etc.

## Remarks for Public installation
- **Please do not set up a PUBLIC Pi-hole if you don't know what you're doing**. You risk getting in all sorts of trouble. Most ISPs don't allow a public DNS resolver on their networks and will shut you down without notice. Why? [Because it's generally a bad idea.](https://community.infoblox.com/t5/Community-Blog/How-Dangerous-Can-An-Open-DNS-Resolver-Be-Part-I/ba-p/4017) **You have been warned.**
- Make sure to add an A (and if applicable, AAAA) record for your Pi-hole's hostname in your domainname's DNS, pointing to the IP address of your server.
- If certificate generation fails, [click here for troubleshooting steps](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion/wiki/Invalid-authorizations) 

## Installation Instructions
1. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu)
2. Clone repository using `git clone https://github.com/Freekers/automated-pihole.git`
3. Edit `hosts` and `.env` file to reflect your setup, i.e. change domains, IP etc. 
`playbook.yml` does NOT need to be changed!
4. Install required roles from Ansible Galaxy roles using `ansible-galaxy install -r requirements.yml`
5. Start playbook using `ansible-playbook playbook.yml --ask-become-pass`  
When running the playbook on a remote target/server, make sure to copy your SSH key to remote target/server first (before running the playbook) using `ssh-copy-id`

## Usage Instructions
Once everything has been installed, you can use regular docker and docker-compose commands to stop/start/restart containers.  
The `docker-compose.yml` file will be located at `/opt/automated-pihole` after installation.

## Uninstallation Instructions
1. `sudo docker-compose -f /opt/automated-pihole/docker-compose.yml down`
2. `rm -rf /opt/automated-pihole`
3. Consult playbook.yml to manually review packages & firewall rules that are no longer needed

## Disclaimer
- Even though this playbook configures and automatic updating Pi-hole, the Pi-Hole developers specifically have not included an auto-update option. It is best to read the release notes and determine if an update is appropriate for you, prior to updating. Sometimes updates can introduce breaking changes requiring manual intervention, such as editing/updating the docker-compose file. Therefore always keep an eye on the release notes.
- This playbook/script/repository is 100% unofficial and NOT affiliated with, funded, endorsed, or in any way associated with [Pi-hole.](https://pi-hole.net/)

## Acknowledgements
- [Pi-hole](https://hub.docker.com/r/pihole/pihole/) Docker image by Pi-hole
- [ouroboros](https://github.com/pyouroboros/ouroboros) Docker image by pyouroboros
- [unbound](https://github.com/obi12341/docker-unbound) Docker image by obi12341
- [nginx-proxy](https://github.com/jwilder/nginx-proxy) Docker image by jwilder
- [letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) Docker image by JrCs
- [Unattended-Upgrades](https://github.com/jnv/ansible-role-unattended-upgrades#unattended-upgrades-role-for-ansible) Role for Ansible by jnv
- [Docker](https://github.com/geerlingguy/ansible-role-docker) Role for Ansible by geerlingguy
- [pihole-fail2ban](https://github.com/goncalopereira/pihole-fail2ban) by goncalopereira
- [dbl.oisd.nl](https://dbl.oisd.nl) blocklist by [sjhgvr](https://www.reddit.com/user/sjhgvr)
- [Commonly white listed domains for Pi-Hole](https://github.com/Freekers/whitelist) by anudeepND & Freekers
- [Unbound configuration file for Pi-hole](https://docs.pi-hole.net/guides/unbound/) by Pi-hole

## License
Unless otherwise specified, all code is released under the GNU General Public License v3.0. See the [repository's `LICENSE` file](https://github.com/Freekers/automated-pihole/blob/master/LICENSE) for details.

### Licenses for other components
- Docker: [Apache 2.0](https://github.com/docker/docker/blob/master/LICENSE)
- Ansible: [GNU 3.0](https://github.com/ansible/ansible/blob/devel/COPYING)
- Pi-hole: [EUPL 1.2](https://github.com/pi-hole/pi-hole/blob/master/LICENSE)
- ouroboros: [MIT](https://github.com/pyouroboros/ouroboros/blob/master/LICENSE)
- unbound: [Apache 2.0](https://github.com/obi12341/docker-unbound/blob/master/LICENSE)
- nginx-proxy: [MIT](https://github.com/jwilder/nginx-proxy/blob/master/LICENSE)
- letsencrypt-nginx-proxy-companion: [MIT](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion/blob/master/LICENSE)
- Unattended-Upgrades: [GPL 2.0](https://github.com/jnv/ansible-role-unattended-upgrades/blob/master/LICENSE)
- Ansible Role - Docker: [MIT](https://github.com/geerlingguy/ansible-role-docker/blob/master/LICENSE)
- whitelist: [MIT](https://github.com/Freekers/whitelist/blob/master/LICENSE)
