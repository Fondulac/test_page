(and headscale)

# What is tailscale

tailscale is a VPN service that makes the devices and applications you own accessible anywhere in the world. 
- Encrypted point to point connections
- Uses opensource WireGuard protocol
- Operates on a peer to peer mesh network (this network is called the tailnet)

Key difference: *no central VPN server!*

==MagicDNS== - hostnames

# How it works

WireGaurd and WireGuard-go

Requirements for each user
- Static IP address
- Open firewall port
- Set of wireguard keys

Problem:
- Each node needs to know the wireguard tunnel endpoint configurations of every other node on the network
- Can't open that many unique firewall ports! *dangerous*
- really hard to secure (so many nodes!)

*Tailscale covers all these issues*

# ==Open source tailscale node software==
- Coordination server (shared drop box for public keys)

Process!
- Each node generate a random public/private key pair for itself and associates the key pair with its identity 
- The node contacts the coordination server and leaves its public key and a note about where that node can currently be found and what domain it's in
- The node downloads a list of public and private key sand addresses in its domain, which have been left on the coordination server by other nodes
- The node configures its WireGaurd instance with the appropriate set of public keys 

*private keys never leave their own nodes*


# Questions

- Is it safe to assume that clients only need to contact the key server once? 
- 