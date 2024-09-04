## nmap commands
- `nmap -sV -p <list of ports> <IP address>`: Lists all the services running on the ports

## hydra commands
- `hydra -C <username & password file> ftp://<IP address>`: Attempts to log in to the FTP service at the specified IP address using a list of username and password combinations provided in the file.

## skipfish commands
- `skipfish -o <Output file> <website url>`: Crawls the website to find all available directories
- `skipfish -o <Output file> -A <user>:<password> <website url>`: Crawls while logged in with HTML credentials
