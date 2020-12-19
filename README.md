# VPN-
creation of VPN  using GCP 
Task 1: Explore the networks and instances
Two custom networks with VM instances have been configured for you. For the purposes of the lab, both networks are VPC networks within a Google Cloud project. However, in a real-world application, one of these networks might be in a different Google Cloud project, on-premises, or in a different cloud.

Explore the networks
Verify that vpn-network-1 and vpn-network-2 have been created with subnets in separate regions.

In the Cloud Console, on the Navigation menu (Navigation menu), click VPC network > VPC networks.
Note the vpn-network-1 network and its subnet-a in us-central1.

Note the vpn-network-2 network and its subnet-b in europe-west1.

Explore the firewall rules
In the navigation pane, click Firewall.
Note the network-1-allow-ssh and network-1-allow-icmp rules for vpn-network-1.
Note the network-2-allow-ssh and network-2-allow-icmp rules for vpn-network-2.
These firewall rules allow SSH and ICMP traffic from anywhere.

Explore the instances and their connectivity
Currently, the VPN connection between the two networks is not established. Explore the connectivity options between the instances in the networks.

In the Cloud Console, on the Navigation menu (Navigation menu), click Compute Engine > VM instances.
Click Columns, and select Network.
From server-1, you should be able to ping the following IP addresses of server-2:

Internal IP address

External IP address

Note the external and internal IP addresses for server-2.

For server-1, click SSH to launch a terminal and connect.

To test connectivity to server-2's external IP address, run the following command, replacing server-2's external IP address with the value noted earlier:

ping -c 3 <Enter server-2's external IP address here>
This works because the VM instances can communicate over the internet.

To test connectivity to server-2's internal IP address, run the following command, replacing server-2's internal IP address with the value noted earlier:

ping -c 3 <Enter server-2's internal IP address here>
You should see 100% packet loss when pinging the internal IP address because you don't have VPN connectivity yet.

Exit the SSH terminal.
Let's try the same from server-2.

Note the external and internal IP addresses for server-1.

For server-2, click SSH to launch a terminal and connect.

To test connectivity to server-1's external IP address, run the following command, replacing server-1's external IP address with the value noted earlier:

ping -c 3 <Enter server-1's external IP address here>
To test connectivity to server-1's internal IP address, run the following command, replacing server-1's internal IP address with the value noted earlier:

ping -c 3 <Enter server-1's internal IP address here>
You should see similar results.

Exit the SSH terminal.
Why are we testing both server-1 to server-2 and server-2 to server-1?

For the purposes of this lab, the path from subnet-a to subnet-b is not the same as the path from subnet-b to subnet-a. You are using one tunnel to pass traffic in each direction. And if both tunnels are not established, you won't be able to ping the remote server on its internal IP address. The ping might reach the remote server, but the response can't be returned.

This makes it much easier to debug the lab during class. In practice, a single tunnel could be used with symmetric configuration. However, it is more common to have multiple tunnels or multiple gateways and VPNs for production work, because a single tunnel could be a single point of failure.

Task 2: Create the VPN gateways and tunnels
Establish private communication between the two VM instances by creating VPN gateways and tunnels between the two networks.

Reserve two static IP addresses
Reserve one static IP address for each VPN gateway.

In the Cloud Console, on the Navigation menu (Navigation menu), click VPC network > External IP addresses.

Click Reserve static address.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	vpn-1-static-ip
IP version	IPv4
Region	us-central1
Click Reserve.

Repeat the same for vpn-2-static-ip.

Click Reserve static address.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	vpn-2-static-ip
IP version	IPv4
Region	europe-west1
Click Reserve.

Note both IP addresses for the next step. They will be referred to us [VPN-1-STATIC-IP] and [VPN-2-STATIC-IP].

Create the vpn-1 gateway and tunnel1to2
In the Cloud Console, on the Navigation menu (Navigation menu), click Hybrid Connectivity > VPN.

Click Create VPN Connection.

If asked, select Classic VPN, and then click Continue.

Specify the following in the VPN gateway section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	vpn-1
Network	vpn-network-1
Region	us-central1
IP address	vpn-1-static-ip
Specify the following in the Tunnels section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	tunnel1to2
Remote peer IP address	[VPN-2-STATIC-IP]
IKE pre-shared key	gcprocks
Routing options	Route-based
Remote network IP ranges	10.1.3.0/24
Make sure to replace [VPN-2-STATIC-IP] with your reserved IP address for europe-west1.

Click command line.
The gcloud command line window shows the gcloud commands to create the VPN gateway and VPN tunnels and it illustrates that three forwarding rules are also created.

Click Close.
Click Create.
Click Check my progress to verify the objective.

Create the 'vpn-1' gateway and tunnel
Create the vpn-2 gateway and tunnel2to1
Click VPN setup wizard.

If asked, select Classic VPN, and then click Continue.

Specify the following in the VPN gateway section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	vpn-2
Network	vpn-network-2
Region	europe-west1
IP address	vpn-2-static-ip
Specify the following in the Tunnels section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	tunnel2to1
Remote peer IP address	[VPN-1-STATIC-IP]
IKE pre-shared key	gcprocks
Routing options	Route-based
Remote network IP ranges	10.5.4.0/24
Make sure to replace [VPN-1-STATIC-IP] with your reserved IP address for us-central1.

Click Create.
Click Cloud VPN Tunnels.
Click Check my progress to verify the objective.

Create the 'vpn-2' gateway and tunnel
Wait for the VPN tunnels status to change to Established for both tunnels before continuing.

Click Check my progress to verify the objective.

Tunnel establishment
Task 3: Verify VPN connectivity
From server-1, you should be able to ping the following IP addresses of server-2:

External IP address

Internal IP address

Verify server-1 to server-2 connectivity
In the Cloud Console, on the Navigation menu, click Compute Engine > VM instances.

For server-1, click SSH to launch a terminal and connect.

To test connectivity to server-2's internal IP address, run the following command:

ping -c 3 <insert server-2's internal IP address here>
Exit the server-1 SSH terminal.

For server-2, click SSH to launch a terminal and connect.

To test connectivity to server-1's internal IP address, run the following command:

ping -c 3 <insert server-1's internal IP address here>
Remove the external IP addresses
Now that you verified VPN connectivity, you can remove the instances' external IP addresses. For demonstration purposes, just do this for the server-1 instance.

On the Navigation menu, click Compute Engine > VM instances.
Select the server-1 instance and click Stop. Wait for the instance to stop.
Instances need to be stopped before you can make changes to their network interfaces.
Click on the name of the server-1 instance to open the VM instance details page.
Click Edit.
For Network interfaces, click the Edit icon (Edit).
Change External IP to None.
Click Done.
Click Save and wait for the instance details to update.
Click Start.
Click Start again to confirm that you want to start the VM instance.
Return to the VM instances page and wait for the instance to start.
Notice that External IP is set to None for the server-1 instance.
Feel free to SSH to server-2 and verify that you can still ping the server-1 instance's internal IP address. You won't be able to SSH to server-1 from the Cloud Console but you can do so from Cloud Shell using Cloud IAP as described here.
