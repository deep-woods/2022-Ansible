# <span id='top'>02 Ansible Concepts</span>

<br>

[[Ansible Inventory]](#inventory)  
[[Ansible Playbooks]](#playbooks)  
[[Ansible Modules]](#modules)  
[[Ansible Variables]](#variables)  

<br>


## <span id='inventory'>Ansible Inventory</span>

[[Top]](#top)

<br>

- Agentless: no need to install any additional software on the target machine to work with Ansible.
- SSH connectivity is all Alsible requires.

<br>

### Ansible parameters

- `ansible_host`: an inventory parameter that specifies the FQDN or IP Address of a server.
- `ansible_conenction`: defines how Ansible connects to the target server.
  - `ssh` for linux
  - `winrm` for windows
- `ansible_port`: defines which port to connect to, port 22 by default.
- `ansible_user`: user who makes remote connections.
- `ansible_ssh_pass`: SSH password for Linux.
  - `ansible_password` for Windows

<br>
<br>

## Hands-on practice 1 - <span id='inventory_example'>`Inventory`</span>

(Go back to the playbooks [question 4](#q4).)  
(Go back to the variables [question 1](#q1).)

        # database servers
        sql_db1 ansible_host=sql01.coding-forest.org ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass 
        sql_db2 ansible_host=sql02.coding-forest.org ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Passquestionquestion

        # web servers
        web_node1 ansible_host=web01.coding-forest.org ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass snmp_port=160-161 nameserver_ip=100.123.120.0
        web_node2 ansible_host=web02.coding-forest.org ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass snmp_port=160-161 nameserver_ip=100.123.120.0
        web_node3 ansible_host=web03.coding-forest.org ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass snmp_port=160-161 nameserver_ip=100.123.120.0


        [db_nodes]
        sql_db1
        sql_db2

        [web_nodes]
        web_node1
        web_node2
        web_node3

        [Java_nodes]
        sql_db1
        web_node1

        [Sumatra_nodes]
        sql_db2
        web_node2
        web_node3

        [Indonesia_nodes:children]
        Java_nodes
        Sumatra_nodes



<br>
<br>

## <span id='playbooks'>Ansible Playbooks</span>

[[Top]](#top)

<br>

Ansible's orchestration language.
- Written in `yaml`
- `ansible-playbook <playbook filename>
- `ansible-playbook --help`

<br>

### Playbook structure - a `yaml` file

- `Play`: defines a set of activities to be run on hosts
  - `Task`: an action to be performed on the host
    - Execute a command
    - Run a script
    - Install a package
    - Shutdown/restart

<br>

Example 1

    - Run command1 on server1
    - Run command2 on server2
    - Run command3 on server3
    - Run command4 on server4

    ...

    - Run command_n on server_n

<br>

Example 2

    - Deploy 100 VMs on Public Cloud
    - Deploy 150 VMs on Private Cloud
    - Provision storage to all VMs
    - Setup Network Configuration on Private VMs
    - Setup Cluster Configuration
    - Configure Web server on 50 public VMs
    - Configure db server on 50 public VMs
    - Setup Loadbalancing between web server VMs
    - Setup Monitoring components
    - Install and configure backup clients on VMs
    - Update CMDB database with new VM information

<br>

### An actual playbook looks like...

| `playbook.yaml`   | `inventory`   |
| -- | -- |
| -<br>&nbsp; - name: Play 1<br>&nbsp; hosts: `localhost`<br>&nbsp; tasks:<br>&nbsp;&nbsp;  - name: Execute command 'uptime'<br>&nbsp;&nbsp;&nbsp;  command: uptime<br><br>&nbsp; - name: Execute script on server<br>&nbsp;&nbsp; script: test_script.sh<br><br>&nbsp;&nbsp;  - name: Install httpd service<br>&nbsp;&nbsp;&nbsp;&nbsp; yum:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;name: httpd<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; state: present<br><br>&nbsp; - name: Start web server<br>&nbsp;&nbsp;&nbsp;&nbsp; service: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;name: httpd<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;state: started   | `local host`<br><br>server1.forest.org<br>server2.forest.org<br><br>[mail]<br>mail1.forest.org<br>mail2.forest.org<br><br>[db]<br>db1.forest.org<br>db2.forset.org<br><br>[web]<br>web1.forest.org<br>web2.forest.org   |

<br>
<br>

## Hands-on practice 2 - Playbooks

<br>

1. Update the name of the `play` - Play is the outer-most key in the `playbook.yaml` dictionary.

        -
            name: 'Execute uptime on localhost'                 <---- This is play. 
            hosts: localhost
            tasks:
                -
                    name: 'Execute the uptime command'
                    command: uptime


<br>

2. Update the name of the `task` and `task command` - Look at the level. A `task` is an attribute of a `playbook`.

        -
            name: 'Display the list of hosts'
            hosts: localhost
            tasks:
                -
                    name: 'Display the list of hosts'               <---- This is task and its name. 
                    command: cat /etc/hosts


<br>

3. Add a second task. 

        -
            name: 'Push all day's work to remote repository at the end of the day'
            hosts: localhost
            tasks:
                -
                    name: 'Add and commit all changes prior to push'
                    command: git add . ; git commit -m 'Push prep for EOD commit'

                - 
                    name: 'Push to origin'
                    command: git push origin master

<br>

<span id='q4'>4.</span> Run the commands on all web servers. Refer to the above [inventory](#inventory_example) for configuration.

        -
            name: 'Push all day's work to remote repository at the end of the day'
            hosts: web_nodes                <------------------------------------------UPDATED
            tasks:
                -
                    name: 'Add and commit all changes prior to push'
                    command: cd ../web_branch; git checkout master; git add . ; git commit -m 'Push prep for EOD commit'

                - 
                    name: 'Push to origin'
                    command: git push origin master

<br>

5. Create a new play

        - <------------------------------------------CREATED
            name: 'Pull all updates from origin at the start of the day'
            hosts: web_nodes                
            tasks:
                -
                    name: 'pull all updates from origin'
                    command: cd ../web_branch; git checkout master; git pull origin master

                - 
                    name: 'Push to origin'
                    command: git push origin master


        -
            name: 'Push all day's work to remote repository at the end of the day'
            hosts: web_nodes          
            tasks:
                -
                    name: 'Add and commit all changes prior to push'
                    command: cd ../web_branch; git checkout master; git add . ; git commit -m 'Push prep for EOD commit'

                - 
                    name: 'Push to origin'
                    command: git push origin master

<br>

6. Create a playbook of sequential operations on different server combinations.

    -
        name: 'Stop all services on all servers in Java location'
        hosts: java_nodes
        tasks:
            -
                name: 'stop all services in Java'
                command: 'service httpd stop; service mysql stop'

    -
        name: 'Restart all servers (web and db) in Java location at once'
        hosts: java_nodes
        tasks:
            -
                name: 'restart all services in Java'
                command: '/sbin/shutdown -r'


    -
        name: 'Resume all services in Java location'
        hosts: java_nodes
        tasks:
            -
                name: 'Resume web/db services'
                command: 'service mysql start; service httpd start'



<br>
<br>

## <span id='modules'>Ansible Modules</span>

[[Top]](#top)

Definition: the different actions run by tasks

A module is a reusable, standalone script that Ansible runs on your behalf, either locally or remotely. Modules interact with your local machine, an API, or a remote system to perform specific tasks like changing a database password or spinning up a cloud instance ([Ansible doc](https://cn-ansibledoc.readthedocs.io/zh_CN/latest/dev_guide/developing_modules_general.html)).

<br>

- System: level such as modifying the users and groups on the system, modifying iptables, firewall configurations on the system, working with logical volume groups, mounting operations, or working with services.
  - starting, stopping, or restarting services in a system.
- Commands:  execute commands or scripts on a host. execution can be simple or performed using the interactive module by responding to prompts.
- Files
- Database:  working with databases such as MongoDB, MySQL, MSSQL,PostgreSQL to add or remove databases or modify database configurations.
- Cloud: Amazon, Azure, Docker, Google, OpenStack, and VMware ...
  - creating and destroying instances, performing configuration changes in networking & security, managing containers, datacenters, clusters, virtual networking, VSAN
- Windows: create a IIS website, install a software using MSI installer,  manage services and users in Windows.


<br>

### Service

- Manage services - start, stop, restart

        playboo.yaml
        
        -
            name: start services in order
            hosts: java_nodes
            tasks:
                - name: start all services in Java location
                  service: name=postgresql state=started
        

<br>

### Script

- runs a local script on a remote node after transferring it over.


<br>

### Idempotent, Idempotency

    "started" the service httpd

    if [ httpd.state = 'started' ]
    then 
        exit
    else
        service httpd start
    fi

Idempotency is a Web API design principle defined as the ability to apply the same operation multiple times without changing the result beyond the first try. The Stripe API guarantees the idempotency of GET and DELETE requests, so it's always safe to retry them ([stripe.com](https://stripe.com/docs/idempotency#:~:text=Idempotency%20is%20a%20Web%20API,always%20safe%20to%20retry%20them.)).

<br>

### lineinfile

- Search for a line in a file and replace it or add it if it doesn't exist.
- idempotent

        /etc/resolv.conf
        nameserver 100.123.120.1
        nameserver 100.123.120.2


        playbook.yaml
        -
          name: Add DNS server to resolv.conf
          hosts: localhost
          tasks:
            - lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 100.123.120.0'

<br>

## Hands-on practice 3 - Modules

- Create a playbook using different modules at the task level.
- Modules to use: 
  - `script`: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html
  - `service`: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html
  - `lineinfile`: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html
  - `user`: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html
 
        -
            name: "Execute a script on all nodes in Java location"
            hosts: java_nodes
            tasks:

            - 
                lineinfile:
                    path: /etc/resolv.conf
                    line: 'nameserver 10.1.250.10'

            - 
                user:
                    group: developers
                    uid: 1040

            - script: /tmp/install_script.sh

            - 
                service:
                    name: httpd
                    state: started
            
            -
                service:
                    name: mysql
                    state: started
        



<br>


## <span id='variables'>Ansible Variables</span>

[[Top]](#top)

<br>

Stores information that varies with each host


<br>

## Hands-on practice 4 - Variables

- <span id='q1'>Scenario 1. Update the playbook using variables in the `inventory`.</span> 
Refer to the above [inventory](#inventory_example) for configuration.

        -
            name: 'Update nameserver entry into resolv.conf file on web_nodes in Java location'
            hosts: java_web_nodes
            tasks:
                -
                    name: 'Update nameserver entry into resolv.conf file'
                    lineinfile:
                        path: /etc/resolv.conf
                        line: 'nameserver {{ nameserver_ip }}'          <------- 100.123.120.0

                -
                    name: 'Disable SNMP Port'
                    firewalld:
                        port: '{{snmp_port}}'                           <------- 160-161
                        permanent: true
                        state: disabled

<br>

Scenario 2. Print some product model data. 
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: java_nodes
    vars: 
        product: ansible
        developers: Ansible Community
        language: Python PowerShell, Shell, Ruby

    tasks:
        -
            name: 'Print the automation tool product name'
            command: 'echo "Automation tool: {{ product }}"'
        -
            name: 'Print the developers'
            command: 'echo "Developers: {{ developers }}"'
        -
            name: 'Print the languages'
            command: 'echo "written in: {{ language }}"'
