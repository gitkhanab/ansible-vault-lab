# Ansible Vault Assignment 4

### Repository Setup

1. **Create GitHub Repository:**
   - Repository name: `ansible-vault-lab`

2. **Clone Repository:**
   ```bash
   git clone https://github.com/gitkhanab/ansible-vault-lab
   cd ansible-vault-lab
   git init

2. **Create Directory Structure:**
   mkdir -p group_vars/all inventory playbooks
   touch group_vars/all/{vault.yml,vars.yml}
   touch inventory/production.ini
   touch playbooks/{create_user.yml,configure_service.yml}
touch README.md requirements.txt .gitignore

4. **Initial Commit:**
   git add .
   git commit -m "First Commit – directory structure"
   git push origin main

6. **Verify Structure with tree:**

### Basic Vault Operations

1. **Create Encrypted vault.yml:**
   ansible-vault create group_vars/all/vault.yml
   
   **Inside vault.yml:**
     vault_user_password: "secure_password123"
     vault_api_key: "api_key_123456"
     vault_db_credentials:
       username: "db_admin"
       password: "db_pass_123"
   
2. **Create Reference File vars.yml:**
    user_password: "{{ vault_user_password }}"
    api_key: "{{ vault_api_key }}"
    db_user: "{{ vault_db_credentials.username }}"
    db_pass: "{{ vault_db_credentials.password }}"

3. **Playbook - create_user.yml:**
- name: Create user
  hosts: all
  become: true
  vars_files:
    - ../group_vars/all/vars.yml
    - ../group_vars/all/vault.yml
  tasks:
    - name: Create user
      ansible.builtin.user:
        user:
          name: user
          password: "{{ user_password | password_hash('sha512') }}"

4. **Playbook - configure_service.yml:**
- name: Configure service
  hosts: all
  vars_files:
    - ../group_vars/all/vars.yml
    - ../group_vars/all/vault.yml
  tasks:
    - name: Service API
      ansible.builtin.debug:
        msg: "API Key: {{ api_key }}"

### Multiple Environment Support

1. **Create Vault Files for Development and Production:**
   mkdir -p group_vars/all
   ansible-vault create group_vars/all/dev_vault.yml
   ansible-vault create group_vars/all/prod_vault.yml

2. **Environment-Specific Values:**
   **Development (dev_vault.yml):**
     vault_user_password: "dev_pass"
     vault_api_key: "dev_key_api"
     vault_db_credentials:
       username: "dev_db_user"
       password: "dev_db_pass"

   **Production (prod_vault.yml):**
     vault_user_password: "prod_pass"
     vault_api_key: "prod_key_api"
     vault_db_credentials:
       username: "prod_db_user"
       password: "prod_db_pass"


4. **Example Playbook Commands:**
   **Run create_user.yml:**
     ansible-playbook playbooks/create_user.yml --ask-vault-pass \
       -e "@group_vars/all/dev_vault.yml" -e "@group_vars/all/vars.yml"

   **Run configure_service.yml:**
     ansible-playbook playbooks/configure_service.yml --ask-vault-pass \
       -e "@group_vars/all/dev_vault.yml" -e "@group_vars/all/vars.yml"
   
### Extra

1. **.gitignore File:**
     *.retry
     *.pyc
     __pycache__/
     *.log
     vault.yml
     dev_vault.yml
     prod_vault.yml
     *.swp
     group_vars/all/*vault.yml

2. **Best Practices:**
   **Do not commit vault passwords or plaintext variables.**
   **Manage passwords securely:**
     echo "password" > .vault-pass
     ansible-playbook playbooks/create_user.yml --vault-password-file ~/.vault_pass.txt





  



