+++
author = "Daulton"
title = "Setup Cryptostorm on PfSense  "
date = "2018-08-31"
description = "The following guide will help you with configuring your pfSense router to be a OpenVPN client to the Cryptostorm OpenVPN servers."
tags = [
    "pfsense",
    "guides",
]
categories = [
    "guides",
]
+++

The following guide will help you with configuring your pfSense router to be a OpenVPN client to the Cryptostorm OpenVPN servers. You must first be a member and have a token, if you do not have a token then you must first purchase one and then hash it.
<!--more-->

STEPS

1. [Download client config files](https://github.com/cryptostorm/cryptostorm_client_configuration_files)

2. Add New CA:

 - On pfSense go to: System --> Cert. Manager
 - On the 'CA' tab (open by default) select 'Add'

Fill in the following info:

 - Descriptive Name: Something meaningful. I used 'CA-CS'
 - Method: leave as 'Import an existing Certificate Authority'
 - Certificate data: paste in the certificate data from below - you will need everything between (and including) "-----BEGIN CERTIFICATE-----" and "-----END CERTIFICATE-----".

```
 -----BEGIN CERTIFICATE-----
MIIFHjCCBAagAwIBAgIJAKekpGXxXvhbMA0GCSqGSIb3DQEBCwUAMIG6MQswCQYD
VQQGEwJDQTELMAkGA1UECBMCUUMxETAPBgNVBAcTCE1vbnRyZWFsMTYwNAYDVQQK
FC1LYXRhbmEgSG9sZGluZ3MgTGltaXRlIC8gIGNyeXB0b3N0b3JtX2RhcmtuZXQx
ETAPBgNVBAsTCFRlY2ggT3BzMRcwFQYDVQQDFA5jcnlwdG9zdG9ybV9pczEnMCUG
CSqGSIb3DQEJARYYY2VydGFkbWluQGNyeXB0b3N0b3JtLmlzMB4XDTE0MDQyNTE3
MTAxNVoXDTE3MTIyMjE3MTAxNVowgboxCzAJBgNVBAYTAkNBMQswCQYDVQQIEwJR
QzERMA8GA1UEBxMITW9udHJlYWwxNjA0BgNVBAoULUthdGFuYSBIb2xkaW5ncyBM
aW1pdGUgLyAgY3J5cHRvc3Rvcm1fZGFya25ldDERMA8GA1UECxMIVGVjaCBPcHMx
FzAVBgNVBAMUDmNyeXB0b3N0b3JtX2lzMScwJQYJKoZIhvcNAQkBFhhjZXJ0YWRt
aW5AY3J5cHRvc3Rvcm0uaXMwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIB
AQDJaOSYIX/sm+4/OkCgyAPYB/VPjDo9YBc+zznKGxd1F8fAkeqcuPpGNCxMBLOu
mLsBdxLdR2sppK8cu9kYx6g+fBUQtShoOj84Q6+n6F4DqbjsHlLwUy0ulkeQWk1v
vKKkpBViGVFsZ5ODdZ6caJ2UY2C41OACTQdblCqaebsLQvp/VGKTWdh9UsGQ3LaS
Tcxt0PskqpGiWEUeOGG3mKE0KWyvxt6Ox9is9QbDXJOYdklQaPX9yUuII03Gj3xm
+vi6q2vzD5VymOeTMyky7Geatbd2U459Lwzu/g+8V6EQl8qvWrXESX/ZXZvNG8QA
cOXU4ktNBOoZtws6TzknpQF3AgMBAAGjggEjMIIBHzAdBgNVHQ4EFgQUOFjh918z
L4vR8x1q3vkp6npwUSUwge8GA1UdIwSB5zCB5IAUOFjh918zL4vR8x1q3vkp6npw
USWhgcCkgb0wgboxCzAJBgNVBAYTAkNBMQswCQYDVQQIEwJRQzERMA8GA1UEBxMI
TW9udHJlYWwxNjA0BgNVBAoULUthdGFuYSBIb2xkaW5ncyBMaW1pdGUgLyAgY3J5
cHRvc3Rvcm1fZGFya25ldDERMA8GA1UECxMIVGVjaCBPcHMxFzAVBgNVBAMUDmNy
eXB0b3N0b3JtX2lzMScwJQYJKoZIhvcNAQkBFhhjZXJ0YWRtaW5AY3J5cHRvc3Rv                               cm0uaXOCCQCnpKRl8V74WzAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IB
AQAK6B7AOEqbaYjXoyhXeWK1NjpcCLCuRcwhMSvf+gVfrcMsJ5ySTHg5iR1/LFay
IEGFsOFEpoNkY4H5UqLnBByzFp55nYwqJUmLqa/nfIc0vfiXL5rFZLao0npLrTr/
inF/hecIghLGVDeVcC24uIdgfMr3Z/EXSpUxvFLGE7ELlsnmpYBxm0rf7s9S9wtH
o6PjBpb9iurF7KxDjoXsIgHmYAEnI4+rrArQqn7ny4vgvXE1xfAkFPWR8Ty1ZlxZ
gEyypTkIWhphdHLSdifoOqo83snmCObHgyHG2zo4njXGExQhxS1ywPvZJRt7fhjn
X03mQP3ssBs2YRNR5hR5cMdC
-----END CERTIFICATE-----
```


**Note:** *Alternatively, you can open an .ovpn config file or the ca2.crt file and copy out the certificate data.* 

 - You can leave the rest of the fields empty
 - Click 'Save' and 'Apply Changes' on the next page
 - The CA Page should now display your new CA

3. Configure DNS Servers

 

 -  On PfSense go to: System --> General Setup
 - Scroll down to 'DNS Server Settings' and update DNS Servers with [two Cryptostorm DNS servers of your choice.](https://github.com/cryptostorm/cstorm_deepDNS/blob/master/dnscrypt-resolvers.csv) You will need to scroll to the right on the table to find the resolver address.

**Note:** *After replacing and adding the DNS Servers, ensure 'DNS Server Override' is unchecked.*

 - Click 'Save' (and 'Apply Changes' if prompted)

4. Add new VPN Client

 - On pfSense go to: VPN --> OpenVpn
 - Click 'Clients'
 - Click 'Add'

General Information:

- Server mode: Peer to Peer (SSL/TLS)
- Protocol: UDP
- Device mode: tun
- Interface: WAN
- Local port: (leave blank)
- Server host or address: Open the config file from earlier and copy out a server address of your choice. I selected 'linux-balancer.cryptostorm.net'
- Server Port: 443
- Proxy port: (leave blank)
- Proxy Auth - extra options: none
- Server hostname resolution: Check 'Infinitely resolve server'
- Description: (I left this blank)

User Authentication Settings

- Username: Paste your hashed token details here
- Password: at least one character, but cannot be blank

Cryptographic Settings

- TLS authentication: (leave unchecked)
- Peer Certificate Authority: Select the CA you created earlier (I selected CS-CA)
- Client Certificate: None (Username and/or Password required)
- Encryption Algorithm: AES-256-CBC(256 bit key, 128 bit lock)
- Auth digest algorithm: SHA12 (512-bit)
- Hardware Crypto: No Hardware Crypto Acceleration

Tunnel Settings

- Leave all fields blank except:
- 'Compression: Enabled with Adaptive Compression'
- 'Disable IPV6: Check 'Don't forward IPV6 traffic''.

Custom options

```
client;
resolv-retry 16;
float;

remote-random;
remote linux-usnorth.cryptostorm.net 443 udp;
remote linux-usnorth.cryptostorm.nu 443 udp;
remote linux-usnorth.cryptostorm.org 443 udp;
remote linux-usnorth.cstorm.pw 443 udp;

comp-lzo no;

explicit-exit-notify 3;

hand-window 37;
mssfix 1400;

auth-user-pass /etc/crypto-token.txt;
ca /etc/crypto-cert.txt;ns-cert-type server;replay-window 128 30;

tls-client;
key-method 2;
```

Click 'Save'

5. Confirm OpenVPN connectivity:

On pfSense go to: Status --> OpenVPN. The Status at this point should be 'up' - i.e. by now you should be authenticating with the VPN server.

6. Assign and Configure Interface

- On pfSense go to: Interfaces --> (assign)

Under the 'Interface Assignments' you will see a row called 'Available netwok ports:'. On the dropdown for that row you need to select the Network Port corresponding to the OpenVPN Client you created earlier. Mine is called 'ovpnc1 ()'. 

- Click 'Add'. This will create a new interface called 'OPT1'

- From the menu select: Interface --> OPT1
General Configuration:
	- Enable: Check 'Enable interface'
	- Description: Give the interface a meaningful name. I chose "CSVPN"
	- IPV4 Configuration Type: DHCP
	- IPV6 Configuration Type: None
	- MAC Address: (leave blank)
	- MTU: (leave blank)
	- MSS: (leave blank)

**Note:** *Leave all other fields blank*

- Click 'Save'
- Click 'Apply Changes' on the next page.

7. Configure Outbound NAT rules:

- From the menu select: Firewall --> NAT
- Select Outbound NAT tab, and then the "Manual Outbound NAT rule generation" button.
- Click 'Save'. This create some (4) new mappings. 
- Edit the second from bottom rule by clicking the pencil 'Edit mapping' icon.
- The only setting you will change is the 'Interface' drop down. Change this from 'WAN' to your new OpenVPN interface. Mine was 'CSVPN'. Ignore the 'OpenVPN' option.
- Click 'Save'
- Click 'Apply Changes'

**Note:** *Don't forget to change the bottom rule by following the above steps.*

8. Create Firewall Rule:

- From the menu select: Firewall --> Rules
- Select the 'LAN' tab
- Edit the rule with Desciption 'Default allow LAN to any rule' by clicking the pencil 'Edit mapping' icon.
- Click 'Display advanced' under the 'Extra Options' section.
In the 'Advanced Options' section, go down to 'Gateway' and select the OpenVPN interface you created earlier.
- Click 'Save'
- Click 'Apply Changes' on the next page.

9. Restart the OpenVPN Service:

- From the menu select: Status --> OpenVPN
- Restart the OpenVPN service by clicking circular arrow 'Restart openVPN Service' icon

After a few moments the OpenVPN service should restart successfully, and display Status 'up'. You may need to refresh your browser (F5) to update the status. If it does not have an 'up' status, try the following:

- Go to Status > System logs > OpenVPN and look through the log file to see what might be wrong. If your Verbosity level is on '3' and you are not finding the information you need try '4'. You can edit this in the OpenVPN client configuration we have been editing.
- Verify the /etc/crypto-token.txt file and that your token is in there. Assure that there is no space at the end of the line where your token is pasted in.

10. Update system DNS entries:

- Go to System --> General Setup.
- Add a (or edit the existing) DNS entry so that it points to the DeepDNS instance for the exit node you've connected to above. If you've connected to linux-balancer.cryptostorm.net, then any DeepDNS instance will do. [DeepDNS instances can be found here](https://github.com/cryptostorm/cstorm_deepDNS/blob/master/dnscrypt-resolvers.csv).
- Set the gateway to VPN1_VPNV4, or whichever name reflects the the VPN gateway you have

**Note:** *Do not use non-cryptostorm DNS servers at the same time as using cryptostorm servers. It can contribute to DNS leaks.*

11. You should be good to go now. To be sure everything is running as intended:

Check your IP, using a service like: http://ifconfig.me/
Go to https://cryptostorm.is/. Ensure 'You are connected to cryptostorm' is displayed in a green box at the top of the page
Go to https://dnsleaktest.com/ and run a leak test.

