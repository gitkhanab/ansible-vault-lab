Ansible Vault Assignment 4
Repository Setup
1.	Creating GitHub Repository called ansible-vault-lab
 
2.	Cloning Repository
a.	git clone https://github.com/gitkhanab/ansible-vault-lab
b.	cd ansible-vault-lab
c.	git init
3.	Creating directory structure
a.	mkdir -p group_vars/all inventory playbooks
b.	touch group_vars/all/{vault.yml,vars.yml}
c.	touch inventory/production.ini
d.	touch playbooks/{create_user.yml,configure_service.yml}
e.	touch README.md requirements.txt .gitignore
4.	Commit
a.	Git add .
b.	Git commit -m “First Commit – directory structure”
c.	Git push origin main
5.	Tree to verify
Basic Vault Operations
1.	Creating encrypted vault.yml
a.	ansible-vault create group_vars/all/vault.yml
b.	Inside vault.yml:
i.	vault_user_password: "secure_password123"
ii.	vault_api_key: "api_key_123456"
iii.	vault_db_credentials:
iv.	  	username: "db_admin"
v.	 	 password: "db_pass_123"
2.	Creating reference file vars.yml
a.	user_password: "{{ vault_user_password }}"
b.	api_key: "{{ vault_api_key }}"
c.	db_user: "{{ vault_db_credentials.username }}"
d.	db_pass: "{{ vault_db_credentials.password }}"
3.	create_user.yml
a.	- name: Create user 
b.	  hosts: all
c.	  become: true
d.	  vars_files:
e.	    - ../group_vars/all/vars.yml
f.	    - ../group_vars/all/vault.yml
g.	  tasks:
h.	    - name: Create user
i.	ansible.builtin.user:
j.	      user:
k.	        name: user
l.	        password: "{{ user_password | password_hash('sha512') }}"
4.	 configure_service.yml
a.	- name: Configure service
b.	  hosts: all
c.	  vars_files:
d.	    - ../group_vars/all/vars.yml
e.	    - ../group_vars/all/vault.yml
f.	  tasks:
g.	    - name: Service API
h.	ansible.builtin.debug:
i.	      debug:
j.	        msg: "API Key: {{ api_key }}"
Multiple Environment Support
1.	Creating Vault files for development and production
a.	mkdir -p group_vars/all
b.	ansible-vault create group_vars/all/dev_vault.yml
c.	ansible-vault create group_vars/all/prod_vault.yml
2.	Different values for each environment
a.	Developers
i.	vault_user_password: "dev_pass"
ii.	vault_api_key: "dev_key_api"
iii.	vault_db_credentials:
iv.	  username: "dev_db_user"
v.	  password: "dev_db_pass"
b.	Production
i.	vault_user_password: "prod_pass”
ii.	vault_api_key: "prod_key_api”
iii.	vault_db_credentials:
iv.	  username: "prod_db_user"
v.	  password: "prod_db_pass
3.	Playbook documentation
a.	Example 1:
i.	ansible-playbook playbooks/create_user.yml --ask-vault-pass \
ii.	  -e "@group_vars/all/dev_vault.yml" -e "@group_vars/all/vars.yml"
b.	Example 2:
i.	ansible-playbook playbooks/configure_service.yml --ask-vault-pass \
ii.	  -e "@group_vars/all/dev_vault.yml" -e "@group_vars/all/vars.yml"
Extra
1.	.gitignore
a.	*.retry
b.	*.pyc
c.	__pycache__/
d.	*.log
e.	vault.yml
f.	dev_vault.yml
g.	prod_vault.yml
h.	*.swp
2.	No committing vault passwords and variables in plaintext
3.	Password Management
a.	For adding to .gitignore
i.	echo "password" > .vault-pass
b.	ansible-playbook playbooks/create_user.yml --vault-password-file ~/.vault_pass.txt
c.	In gitignore:
i.	group_vars/all/*vault.yml
