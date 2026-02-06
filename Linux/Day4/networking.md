# Networking Commands

1. **`ping google.com`** – Checks connectivity to a remote server.
2. **`ifconfig`** – Displays network interfaces (deprecated, use `ip`).
3. **`ip a`** – Shows IP addresses of network interfaces.
4. **`netstat -tulnp`** – Displays open network connections.
5. **`curl https://example.com`** – Fetches a webpage's content.
6. **`wget https://example.com/file.zip`** – Downloads a file from the internet.
Example 1: Connecting to a Remote Server
To establish a connection with a remote system using Telnet on Linux Ubuntu or Telnet Linux CentOS, use:

telnet 192.168.1.1        # This command will attempt to access the device at the specified IP address
telnet
connection with a remote system using Telnet
Example 2: Checking an Open Port
To check if port 80 (HTTP) is open on a website:

telnet example.com 80
If the connection is successful, the Telnet command Linux confirms the port is open. Otherwise, it indicates that the port is closed or blocked by a firewall.

telnet
To check if port open or not

