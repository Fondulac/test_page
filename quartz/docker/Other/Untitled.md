
Bettercap 
- Bettercap recon
- Bettercap wifi auth capture
- Packet capture to HCCAPX conversion and Hashcat cracking 
Airsuite tools
- Recon and shared key recovery 
- Cracking WPA 
- WEP attack
Airgeddon
- Denial of service
EAPHammer
- Capturing NETNTLM hashes
- Cracking with john 
Air-hammer

Other labs: 
- Wifiphisher 
- Arpspoof
- WPS attack

Changes

Create:
	Bettercap.py
	Airsuite.py
	Airgeddon.py

Leave:
	Eaphammer
	Wifiphisher
	ARPSpoof
	WPS Attack

Create new network containing 
- WEP (simple_wifi) - 2 - 1
- WPA2 (WPA-LAB) - 2 - 6
- WPA2 (Harlow_Home) - 4 - 11
- WPA2 (FBI_Van) - 1 - 1
- WPA2 hidden ssid - 6

This network will be the basis for the following labs:
- Bettercap
- Airsuite
- Airgeddon 


- [x] Bettercap recon
- [x] Bettercap Wi-Fi Authentication Capture
- [x] Packet Capture to HCCPAX Conversion and Hashcat Cracking
- [x] Airsuite Tools - recon and pre-shared key recovery
- [x] Cracking WPA handshakes with aircrack-ng
- [x] airgeddon denial of service beacon attacks
- [x] capture active directory credentials with Evil-twin attack
- [x] cracking NETNTLM credentials with john the ripper
- [x] Rogue AP with wifiphisher
- [x] WPS exploitation 
- [x] WEP key cracking
- [ ] ARPspoof