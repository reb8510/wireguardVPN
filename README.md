# Cloud VPN-Docker Project

**Installation Process**
<p>Create a Digital Ocean account on digitalocean.com</p>
<p>Create a DO Ubunto Droplet by navigating to the left side of the DO dashboard, click "Droplet", then create a Droplet with Ubunto Distributions, Basic plan, with Regular Intel, and choose either a SSH key or password (I chose Password). </p>
<p>Once created, click on "Access" on the left side of the Droplet navigation bar. Once there, you will click "Launch Droplet Console" to launch the terminal for your Droplet. </p>

**Setup Wireguard VPN server**
<p>Once in the Droplet terminal, you will begin to setup Wireguard. Copy this below to run Wireguard</p>
<pre><code>mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml</code></pre>

<p>Copy below and put in the Compose file</p>
<pre><code> version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=143.198.97.82
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1</code></pre>

<p>Start Wireguard by running this in the terminal</p>
<pre><code>cd ~/wireguard/
docker-compose up -d</code></pre>

**Wireguard on phone**
<p>Next you will connect your phone to Wireguard. Search Ipleak.net on a mobile browser and check your IP address and screenshot it. Download the Wireguard app on your mobile device to start the connection process. While the app is downloading, run the command below in your terminal.</p>
<pre><code>docker-compose logs -f wireguard</code></pre>
<p>Once Wireguard downloads, it will show a QR code in the terminal.</p>
<p>Open the Wireguard app and create a tunnel through a QR code. Scan the QR code from your terminal and activate the tunnel. Refresh your mobile browser to see your new IP address.</p>

**Wireguard on laptop**
<p>Next you will connect your laptop to Wireguard. Search Ipleak.net on a browser and check your IP address and screenshot it.</p>
<p>Look for your .conf file by these commands in your terminal.</p>
<pre><code>cd wireguard</code></pre>
<pre><code>ls</code></pre>
<pre><code>cd config/code></pre>
<pre><code>ls</code></pre>
<pre><code>cd peer_pc1</code></pre>
<pre><code>ls</code></pre>
<pre><code>nano peer_pc1.conf</code></pre>
<p>Copy what is in your config file. Similar to mine below...</p>
<pre><code>[Interface]
Address = 10.0.0.2
PrivateKey = 6CCItqWIdYT6gChq6PGEzISbr83Z9zI6/V1F0xAIWk4=
ListenPort = 51820
DNS = 10.0.0.1

[Peer]
PublicKey = XDXvbp5adasCk27g/ytwwicMz7Wnu5eKVId3lH58LAc=
Endpoint = 143.198.97.82:51820
AllowedIPs = 0.0.0.0/0, ::/0</code></pre>

<p>Create a new empty tunnel on Wireguard with a name, and your pasted config file. Activate your new tunnel once saved.</p>
<p>Your IP address should have changed. Check your IP address and screenshot the new IP address.</p>







