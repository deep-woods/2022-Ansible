# <span id='top'>03 Conditionals, Loops & Roles</span>

<br>

[[Ansible Conditionals]](#conditionals)  
[[Ansible Loops]](#loops)  
[[Ansible Roles]](#roles)  

<br>


## <span id='conditionals'>Ansible Conditionals</span>

[[Top]](#top)

<br>

- `when`
  - `or`
  - `and`

        - name: Install NGINX
          hosts: all
          tasks: 
          - 
            name: Install NGINX on Debian 
            apt:                                         <------Package manager becomes different
                name: nginx
                state: present
            when: ansible_os_family == "Debian" and      <------Depending on the condition
                  ansible_distribution_version == "16.04"

          - 
            name: Install NGINX on Debian 
            yum:                                         <------Package manager becomes different
                name: nginx
                state: present
            when: ansible_os_family == "RedHat" or       <------Depending on the condition
                  ansible_os_family == "SUSE"
        


<br>
<br>

## Hands-on practice 1 - Conditionals

1. if specific server ...

        -
            name: 'Execute a script on all web server nodes'
            hosts: all_servers
            tasks:
                -
                    service: 'name=mysql state=started'
                    when: 'ansible_host=="db1.forest.org"'
            
        -
            name: 'Execute a script on all web server nodes'
            hosts: all_servers
            tasks:
                -
                    service: 'name=httpd state=started'
                    when: 'ansible_host=="web2.forest.org"'

<br>

2. for specific numeric range ...

        -
            name: 'Print watch video permission'
            hosts: localhost
            vars:
                age: 25
            tasks:
                -
                    command: 'echo "This video is not accessible to minor users. Age verification required."'
                    when: "age < 18"
                -
                    command: 'echo "Directing to the video content"'
                    when: "age >= 18"

<br>

3. Implement the functionality of the `lineinfile` module. 

- Use a `register` directive to store the `cat` output. Store it to `resolv_output`.

- Use a conditional to check if the output contains the name server (10.0.250.10) already. Use `cat_output.stdout.find(<IP>) == -1`.

        -
            name: 'Add name server entry if not present in resolv.conf'
            hosts: localhost
            vars:
                name_server: 8.8.8.8
            tasks:
                -
                    shell: 'cat /etc/resolv.conf'
                    register: resolv_output
                -
                    shell: 'echo "nameserver {{ name_server }}" >> /etc/resolv.conf'
                    when: 'resolv_output.stdout.find({{ name_server }}) == -1'


<br>
<br>

## <span id='loops'>Ansible Loops</span>

[[Top]](#top)

<br>

- Loop directive 

<br>

- `with_*`
  - `with_items`
  - `with_file`
  - `with_url`
  - `with_mongodb`

  - `with_dict` 
...

<br>

## Hands-on practice 2 - 

<br>

Scenario 1. Learn the syntax and structure.

    -
        name: 'Print list of languages'
        hosts: localhost
        vars:
            languages:
                - Python
                - Java
                - JavaScript
                - Shell
        tasks:
            -
                command: 'echo "{{ item }}"'
                with_items: "{{ languages }}"


<br>

Scenario 2. 

    -
        name: 'Install required packages for linux template setup'
        hosts: localhost
        vars:
            packages:
                - httpd
                - binutils
                - glibc
                - ksh
                - libaio
                - libXext
                - gcc
                - make
                - sysstat
                - unixODBC
                - mongodb
                - nodejs
                - grunt
                - python3
                - tkinter
        tasks:
            -
                yum: 'name={{ item }} state=present'
                with_items: '{{ packages }}'



<br>


## <span id='roles'>Ansible Roels</span>

[[Top]](#top)

<br>

|  `Mysql`  | `nginx`   |
| -- | -- |
|  Installing pre-requisits<br>Installing `mysql` packages<br>Configuring `mysql` service <br>Configuring database and users  |   Installing pre-requisits<br>Installing `nginx` packages<br>Configuring `nginx` service <br>Configuring custom webpages |

<br>

- Ansible galaxy community https://galaxy.ansible.com/

<br>

1. Initialise your role directory structure. 

        ansible-galaxy init mysql

        my-playbook
        ├── playbook.yaml
        ├── roles
        │   └── mysql
        │        ├── README.md
        │        ├── templates
        │        ├── tasks
        │        ├── handlers
        │        ├── vars
        │        ├── defaults
        │        ├── meta 
        ...
         

1.1 `playbook.yml` example

        - name: Install and configure MySQL
          hosts: db-server
          roles: 
              - mysql

<br> 

2. Commands


Find/install a role from in the community.

- find

        ansible-galaxy search mysql

- install

        ansible-galaxy install codingforest.mysql

<br>

- List roles.

        ansible-galaxy list

          kodekloud.mysql
          codingforest.mysql
        
- List role paths.

        ansible-config dump | grep ROLE

          DEFAULT_PRIVATE_ROLE_VARS(DEFAULT) = False
          DEFAULT_ROLES_PATH(default)=[u'/root/.ansible/roles', u'/sur/share/ansible/roles', u'/etc/ansible/roles']
          GALAXY_ROLE_SKELETON(default) = None
          GALAXY_ROLE_SKELETON_IGNORE(default) = ['^.git$', '^.*/.git_keep$']

- Install a role

        ansible-galaxy isntall codingforest.mysql -p ./roles

<br>

3. Use role.

- `Playbook-all-in-one.yaml`

        -
          name: Install and configure MySQL
          hosts: db-and-webserver
          roles:
            - codingforest.mysql
            - nginx

<br>

- `Playbook-distributed.yaml`

        -
          name: Install and configure MySQL
          hosts: db-server
          roles:
            - codingforest.mysql

        -
          name: Install and configure Web Sserver
          hosts: web-server
          roles:
            - nginx
