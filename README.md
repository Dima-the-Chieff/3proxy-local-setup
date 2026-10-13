I have 3proxy on Windows 10 in a chain to external proxy in internet with SOCKS5 5.253.189.9:19443 with login and pass. 
my 3proxy must serve at 127.0.0.1:8084 on HTTP with no auth. 
I use a bat file:
start "" /B "c:\3proxy\bin64\3proxy.exe" "c:\3proxy\3proxy.cfg"
powershell -NoExit -Command "$env:HTTP_PROXY='http://127.0.0.1:8084'; $env:HTTPS_PROXY='http://127.0.0.1:8084'; $env:NO_PROXY='localhost,127.0.0.1';

How to setup?


<code>
# --- c:\3proxy\3proxy.cfg ---

# DNS (only used if something must resolve locally)
nserver 1.1.1.1
nserver 8.8.8.8
nscache 65536

# Logging (create c:\3proxy\logs first, or delete these two lines)
log "c:\3proxy\logs\3proxy-%y%m%d.log" D
logformat "L%d-%m-%Y %H:%M:%S %C:%c -> %R:%r %O/%I %T"

maxconn 200
timeouts 1 5 30 60 180 1800 15 60

# No user/password for local clients, only allow localhost.
# Use "iponly", not "none": with "auth none" the ACLs are skipped,
# so the "parent" line below would be ignored.
auth iponly
allow * 127.0.0.1

# Chain everything to the upstream SOCKS5 proxy.
# socks5+ = send hostnames to the upstream, so DNS is resolved there.
parent 1000 socks5+ 5.253.189.9 19443 YOUR_LOGIN YOUR_PASSWORD

# Local HTTP/HTTPS (CONNECT) proxy, no auth, localhost only
proxy -p8084 -i127.0.0.1
</code>
