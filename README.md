# consul-course
# Pluralsight.com Course :: Getting Started with Consul

## Powershell Permissions:
PS D:\WORKSPACE\git_projects\consul-getting-started> Set-ExecutionPolicy RemoteSigned

## Powershell Script:
PS D:\WORKSPACE\git_projects\consul-getting-started> .\SETUP_WINDOWS.ps1

## Visual Studio Code Configurations:
- Create folder: D:\WORKSPACE\consul-course
PS D:\WORKSPACE\consul-course> code .

### User Settings (To use Cygwin Terminal like Linux):
	{
		"terminal.integrated.shell.windows": "C:\\ProgramData\\chocolatey\\bin\\Cygwin.exe",
		"files.autoSave": "afterDelay"
	}

### Change Line Sequences:
	View -> Command Palette -> Change End of Line Sequences
		Two options available:
			LF
			CRLF
		Select LF.


## COMMANDS (Vagrant) [Using VS Code]:
 - `NOTE: Nag.arvind@NAGARVIN-LT /cygdrive/d/WORKSPACE/consul-course is renamed as gudiseva:~ `

gudiseva:~/consul-course $ vagrant init --minimal boxcutter/ubuntu1404-docker
gudiseva:~/consul-course $ vagrant init --minimal wesmcclure/ubuntu1404-docker
gudiseva:~/consul-course $ vagrant up
gudiseva:~/consul-course $ vagrant ssh consul-server
	Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 4.2.0-27-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/
	----------------------------------------------------------------
	  Ubuntu 14.04.4 LTS                          built 2016-06-06

vagrant@consul-server:~$ ip a
vagrant@consul-server:~$ exit
gudiseva:~/consul-course $ vagrant box list
vagrant@consul-server:~$ ls /vagrant/
	install.consul.sh	Vagrantfile

vagrant@consul-server:~$ chmod +x /vagrant/install.consul.sh
vagrant@consul-server:~$ /vagrant/install.consul.sh
vagrant@consul-server:~$ consul
	Usage: consul [--version] [--help] <command> [<args>]

vagrant@consul-server:~$ exit
gudiseva:~/consul-course $ vagrant status
	Current machine states:

	consul-server             running (virtualbox)

gudiseva:~/consul-course $ vagrant destroy -f consul-server
gudiseva:~/consul-course $ vagrant status
	Current machine states:

	consul-server             not created (virtualbox)

gudiseva:~/consul-course $ vagrant up
gudiseva:~/consul-course $ vagrant ssh consul-server
vagrant@consul-server:~$ consul

vagrant@consul-server:~$ consul agent -dev
==> Starting Consul agent...
==> Consul agent running!
           Version: 'v1.0.7'
           Node ID: '77f09342-1527-29e9-5eac-c3939255d0b9'
         Node name: 'consul-server'
        Datacenter: 'dc1' (Segment: '<all>')
            Server: true (Bootstrap: false)
       Client Addr: [127.0.0.1] (HTTP: 8500, HTTPS: -1, DNS: 8600)
      Cluster Addr: 127.0.0.1 (LAN: 8301, WAN: 8302)
           Encrypt: Gossip: false, TLS-Outgoing: false, TLS-Incoming: false

==> Log data will now stream in as it occurs:

    2018/05/09 10:25:45 [INFO] raft: Election won. Tally: 1
    2018/05/09 10:25:45 [INFO] raft: Node at 127.0.0.1:8300 [Leader] entering Leader state


vagrant@consul-server:~$ ip link // To see the Interfaces

vagrant@consul-server:~$ consul agent -dev -advertise 172.20.20.31
==> Starting Consul agent...
==> Consul agent running!
           Version: 'v1.0.7'
           Node ID: '1dd66973-c604-2181-8868-90d7dbfc3ca9'
         Node name: 'consul-server'
        Datacenter: 'dc1' (Segment: '<all>')
            Server: true (Bootstrap: false)
       Client Addr: [127.0.0.1] (HTTP: 8500, HTTPS: -1, DNS: 8600)
      Cluster Addr: 172.20.20.31 (LAN: 8301, WAN: 8302)
           Encrypt: Gossip: false, TLS-Outgoing: false, TLS-Incoming: false

==> Log data will now stream in as it occurs:

    2018/05/09 10:42:14 [INFO] agent: Started DNS server 127.0.0.1:8600 (udp)
    2018/05/09 10:42:14 [INFO] raft: Node at 172.20.20.31:8300 [Follower] entering Follower state (Leader: "")
    2018/05/09 10:42:14 [INFO] raft: Election won. Tally: 1
    2018/05/09 10:42:14 [INFO] raft: Node at 172.20.20.31:8300 [Leader] entering Leader state
    2018/05/09 10:42:14 [INFO] consul: cluster leadership acquired
    2018/05/09 10:42:14 [INFO] consul: New leader elected: consul-server
    2018/05/09 10:42:17 [DEBUG] agent: Node info in sync


vagrant@consul-server:~$ CTRL + C => Exit

### NOTE:
--- ADD **advertise** and **bind** to allow clients to connect to the Consul Server

vagrant@consul-server:~$ consul agent -dev -bind 172.20.20.31 -client 0.0.0.0
-- OR --
vagrant@consul-server:~$ consul agent -dev -advertise 172.20.20.31 -bind 0.0.0.0 -client 0.0.0.0 //Preferred
-- OR --
vagrant@consul-server:~$ consul agent -dev -config-file /vagrant/server.consul.json & // Moved configurations to server.consul.json

gudiseva:~/consul-course $ vagrant status
	Current machine states:

	consul-server             running (virtualbox)
	lb                        not created (virtualbox)
	web1                      not created (virtualbox)
	web2                      not created (virtualbox)
	web3                      not created (virtualbox)


gudiseva:~/consul-course $ vagrant up

gudiseva:~/consul-course $ vagrant status
	Current machine states:

	consul-server             running (virtualbox)
	lb                        running (virtualbox)
	web1                      running (virtualbox)
	web2                      running (virtualbox)
	web3                      running (virtualbox)


gudiseva:~/consul-course $ vagrant ssh consul-server

vagrant@consul-server:~$ consul agent -dev -advertise 172.20.20.31 -bind 0.0.0.0 -client 0.0.0.0 &
vagrant@consul-server:~$ ps aux | grep consul
vagrant@consul-server:~$ exit

vagrant@consul-server:~$ killall consul // To kill Consul

gudiseva:~/consul-course $ vagrant ssh web1
	
vagrant@web1:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web1:~$ echo $ip
vagrant@web1:~$ consul agent -advertise $ip -config-file /vagrant/common.json
vagrant@web1:~$ CTRL + C => Exit
vagrant@web1:~$ consul agent -advertise $ip -config-file /vagrant/common.json &
vagrant@web1:~$ ps aux | grep consul
vagrant@web1:~$ exit

gudiseva:~/consul-course $ vagrant ssh web1

vagrant@web1:~$ ps aux | grep consul
vagrant@web1:~$ exit

#### Repeat the above steps in web2, web3 and lb

gudiseva:~/consul-course $ vagrant ssh web2
	
vagrant@web2:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web2:~$ echo $ip
vagrant@web2:~$ consul agent -advertise $ip -config-file /vagrant/common.json &
vagrant@web2:~$ ps aux | grep consul
vagrant@web2:~$ exit

gudiseva:~/consul-course $ vagrant ssh web3
	
vagrant@web3:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web3:~$ consul agent -advertise $ip -config-file /vagrant/common.json &
vagrant@web3:~$ exit

gudiseva:~/consul-course $ vagrant ssh lb
	
vagrant@lb:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@lb:~$ consul agent -advertise $ip -config-file /vagrant/common.json &
vagrant@lb:~$ exit

[Verify the above in UI]



gudiseva:~/consul-course $ vagrant ssh web1

vagrant@web1:~$ killall consul
vagrant@web1:~$ ps aux | grep consul
vagrant@web1:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web1:~$ consul agent -advertise $ip -config-file=/vagrant/common.json -config-file=/vagrant/web.service.json &
	2018/05/10 14:03:40 [INFO] agent: Synced service "web"

vagrant@web1:~$ exit

#### Note: Service Management is separate from Service Registration

#### Reference to Killall => http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_12_01.html
	Table 12-2. Common kill signals

	Signal name	Signal value	Effect
	SIGHUP	1	Hangup
	SIGINT	2	Interrupt from keyboard
	SIGKILL	9	Kill signal
	SIGTERM	15	Termination signal
	SIGSTOP	17,19,23	Stop the process

vagrant@web1:~$ killall -s 1 consul
    2018/05/10 14:13:08 [INFO] agent: Caught signal:  hangup
    2018/05/10 14:13:08 [INFO] agent: Reloading configuration...

vagrant@web1:~$ exit

gudiseva:~/consul-course $ vagrant ssh web1

vagrant@web1:~$ cat /vagrant/setup.web.sh
vagrant@web1:~$ chmod +x /vagrant/setup.web.sh
vagrant@web1:~$ /vagrant/setup.web.sh
	Unable to find image 'nginx:latest' locally
	latest: Pulling from library/nginx
	Status: Downloaded newer image for nginx:latest

#### Note: The latest version of Nginx Docker image is pulled from => https://hub.docker.com/_/nginx/

vagrant@web1:~$ docker ps
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
	5dfab0e8d584        nginx               "nginx -g 'daemon off"   11 minutes ago      Up 11 minutes       0.0.0.0:8080->80/tcp   web

[Verify Nginx is running => http://172.20.20.21:8080/]
[Custom Page (with IP Address and Hostname) is available at => http://172.20.20.21:8080/ip.html]

vagrant@web1:~$ exit

gudiseva:~/consul-course $ vagrant ssh web2

vagrant@web2:~$ killall consul
vagrant@web2:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web2:~$ consul agent -advertise $ip -config-file=/vagrant/common.json -config-file=/vagrant/web.service.json &
vagrant@web2:~$ exit

gudiseva:~/consul-course $ vagrant ssh web2

vagrant@web2:~$ /vagrant/setup.web.sh
vagrant@web2:~$ docker ps

[Verify Nginx (Custom Page) is running => http://172.20.20.22:8080/ip.html]

vagrant@web2:~$ exit

gudiseva:~/consul-course $ vagrant ssh web3

vagrant@web3:~$ killall consul
vagrant@web3:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web3:~$ consul agent -advertise $ip -config-file=/vagrant/common.json -config-file=/vagrant/web.service.json &
vagrant@web3:~$ exit

gudiseva:~/consul-course $ vagrant ssh web3

vagrant@web3:~$ /vagrant/setup.web.sh
vagrant@web3:~$ docker ps

[Verify Nginx (Custom Page) is running => http://172.20.20.23:8080/ip.html]

vagrant@web3:~$ exit


gudiseva:~/consul-course $ vagrant ssh lb

vagrant@web3:~$ killall consul
vagrant@web3:~$ consul configtest -config-file /vagrant/lb.service.json
	echo $? => Result is 0 // Config file is good
	echo $? => Result is 1 // This means there is a problem with the config file

vagrant@web3:~$ ip=$(ifconfig eth1 | grep 'inet addr' | awk '{ print substr($2,6) }')
vagrant@web3:~$ consul agent -advertise $ip -config-file=/vagrant/common.json -config-file=/vagrant/lb.service.json &
vagrant@web3:~$ exit


gudiseva:~/consul-course $ vagrant ssh lb

vagrant@lb:~$ chmod +x /vagrant/provision/setup.lb.sh
vagrant@lb:~$ /vagrant/provision/setup.lb.sh
vagrant@lb:~$ docker ps
vagrant@lb:~$ ls
	consul.zip  haproxy.cfg

vagrant@lb:~$ consul monitor
vagrant@lb:~$ dig @localhost -p 8600 lb.service.consul SRV
vagrant@lb:~$ docker stop haproxy
vagrant@lb:~$ docker start haproxy
vagrant@lb:~$ consul exec -node web1 docker stop web

	[http://172.20.20.11:80/ip.html => Round robin connects to all the Web Servers.  When connected to web1, gives error `503 Service Unavailable`]


vagrant@lb:~$ chmod +x /vagrant/provision/install.consul-template.sh
vagrant@lb:~$ $_
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
									 Dload  Upload   Total   Spent    Left  Speed
	100 3446k  100 3446k    0     0  4448k      0 --:--:-- --:--:-- --:--:-- 4447k
	Archive:  consul-template.zip
	  inflating: consul-template

vagrant@lb:~$ consul-template -h
	
vagrant@lb:~$ consul-template -template /vagrant/provision/haproxy.ctmpl -dry

	[Verify the above by playing with the below commands in another terminal:]

vagrant@lb:~$ consul exec -node web1 docker start web
vagrant@lb:~$ consul exec -node web1 docker stop web
vagrant@lb:~$ consul exec -node web docker stop web
vagrant@lb:~$ consul exec -node web docker start web
vagrant@lb:~$ consul exec -node web1 docker stop web
	
vagrant@lb:~$ CTRL + C [In main terminal]
	
vagrant@lb:~$ consul-template -config /vagrant/provision/lb.consul-template.hcl
	haproxy	
	
	[http://172.20.20.11:80/ip.html => Round robin connects to the web2 and web3 Servers.  `Load Balancer does not connect to web1 Server.`]	

<<In another terminal:>>	
vagrant@lb:~$ killall -HUP consul-template
vagrant@lb:~$ cat haproxy.cfg
	
	[Output from previous terminal:]
	Received HUP, reloading configuration...
	haproxy	
	
vagrant@lb:~$ consul exec -node web1 docker start web
	[http://172.20.20.11/haproxy => web1 Server should be immediately available.]	

vagrant@lb:~$ consul exec -node web2 docker stop web
	[http://172.20.20.11/haproxy => web2 Server is unavailable now.]		
	
vagrant@lb:~$ consul maint -http-addr=172.20.20.23:8500 -enable
	Node maintenance is now enabled

vagrant@lb:~$ consul maint -http-addr=172.20.20.23:8500 -disable
	Node maintenance is now disabled



## COMMANDS (LOCAL) [Using Cygwin]:
Nag.arvind@NAGARVIN-LT /cygdrive/d/wORKSPACE/consul-course
$ ipconfig
	IPv4 Address. . . . . . . . . . . : 172.20.20.1

$ consul agent -config-file desky.consul.json

Kill Consul
$ taskkill /F /IM consul.exe


## Consul DNS (Port 8600)
#### Install DIG DNS Tool on Windows 10. Refer below URL:
	http://nil.uniza.sk/linux-howto/how-install-dig-dns-tool-windows-10

### Get All the Nodes
D:\> dig @localhost -p 8600 consul-server.node.consul

### Get All the Services
D:\> dig @localhost -p 8600 consul.service.consul 

### Get Port for the Services 
D:\> dig @localhost -p 8600 consul.service.consul SRV

### 
C:\> dig @localhost -p 8600 web.service.consul SRV

;; ANSWER SECTION:
web.service.consul.     0       IN      SRV     1 1 8080 web1.node.dc1.consul.
web.service.consul.     0       IN      SRV     1 1 8080 web2.node.dc1.consul.
web.service.consul.     0       IN      SRV     1 1 8080 web3.node.dc1.consul.

;; ADDITIONAL SECTION:
web1.node.dc1.consul.   0       IN      A       172.20.20.21
web1.node.dc1.consul.   0       IN      TXT     "consul-network-segment="
web2.node.dc1.consul.   0       IN      A       192.168.127.6
web2.node.dc1.consul.   0       IN      TXT     "consul-network-segment="
web3.node.dc1.consul.   0       IN      A       192.168.127.7
web3.node.dc1.consul.   0       IN      TXT     "consul-network-segment="



#### Note: SRV is part of a Standard Specification. Refer below URL:
	https://en.wikipedia.org/wiki/SRV_record


## Consul CLI PRC (Port 8400)

### Get Consul INFO
D:\> consul info
agent:
        check_monitors = 0

### Get Consul Members
D:\> consul members
Node           Address            Status  Type    Build  Protocol  DC   Segment
consul-server  172.20.20.31:8301  alive   server  1.0.7  2         dc1  <all>
NAGARVIN-LT    172.20.20.1:8301   alive   client  1.0.7  2         dc1  <default>

### Consul Monitor to remotely tail the Logs
D:\> consul monitor
2018/05/10 17:38:15 [INFO] serf: EventMemberJoin: NAGARVIN-LT 172.20.20.1
2018/05/10 17:38:15 [INFO] agent: Started DNS server 127.0.0.1:8600 (udp)
2018/05/10 17:38:15 [INFO] agent: Started DNS server 127.0.0.1:8600 (tcp)

### Other Commands:
D:\> consul members
	Node           Address            Status  Type    Build  Protocol  DC   Segment
	consul-server  172.20.20.31:8301  alive   server  1.0.7  2         dc1  <all>
	NAGARVIN-LT    172.20.20.1:8301   alive   client  1.0.7  2         dc1  <default>
	lb             172.20.20.11:8301  alive   client  1.0.7  2         dc1  <default>
	web1           172.20.20.21:8301  alive   client  1.0.7  2         dc1  <default>
	web2           172.20.20.22:8301  alive   client  1.0.7  2         dc1  <default>
	web3           172.20.20.23:8301  alive   client  1.0.7  2         dc1  <default>

D:\> consul exec uptime

D:\> consul exec -node=web uptime

D:\> consul exec -node=web2 killall -s 2 consul

D:\> consul exec -node=web3 killall -s 9 consul

#### Stop Nginx
D:\> consul exec -node web1 docker stop web
D:\> dig @localhost -p 8600 web.service.consul SRV

#### Start Nginx
D:\> consul exec -node web1 docker start web
D:\> dig @localhost -p 8600 web.service.consul SRV



D:\> consul maint
	[No Output]
D:\> consul maint -enable -reason Because
Node maintenance is now enabled

D:\> consul maint
Node:
  Name:   NAGARVIN-LT
  Reason: Because

D:\> consul maint -disable
Node maintenance is now disabled

D:\>consul maint
	[No Output]

-- Note: The above is handy when we want to get a particular service out of pool for maintenance activities.



## HTTP APIs (Port 8500)

### View Health of Services and Nodes
http://localhost:8500/ui/#/dc1/nodes

### View the Connected Nodes
http://localhost:8500/v1/catalog/nodes

### Http Catalog
https://www.consul.io/api/catalog.html

### Demo URL
https://demo.consul.io/ui/


## HTTP APIs (cURL)
#### Install cURL:
	http://open-edx-windows-7-installation-instructions.readthedocs.io/en/latest/6_Install_cURL_for_Windows.html

###
D:\> curl http://localhost:8500/v1/catalog/services?pretty

D:\> curl http://localhost:8500/v1/catalog/service/web?pretty

### Health API
D:\> curl http://localhost:8500/v1/health/service/web?pretty


