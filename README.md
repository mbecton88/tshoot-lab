**Objective:**
To simulate common help desk/IT Support scenarios and practice troubleshooting techniques using osTicket, Azure VMs, and Packet Tracer.

**Prerequisites:**
* Completion of osTicket lab
* Completion of Active Directory lab
* Cisco's Packet Tracer installed (Free to download)
* Basic networking knowledge

## Part 1: Setting Up the Environment

### Packet Tracer Topology:

This following outlines the setup for a Packet Tracer network topology. By the end, your topology should resemble below, for the troubleshooting portions of the lab:
![image](https://github.com/user-attachments/assets/9564842d-f909-47a9-96e4-73c37075329f)

## Devices

* PCs: 2 (PC1 and PC2) - Representing local users.
* Switch: 1 (SW1 - 2960 Switch) - Connecting the PCs and Server1.
* Routers: 2 (R1 and R2 - 2911 Routers) - R1 connecting the local network to R2 (simulating the internet), and R2 connecting to the Azure Server.
* Servers: 2 (Server1 and Azure Server) - Server1 representing an internal resource, and Azure Server representing an Azure VM.

## Connections

* PC1 -> SW1 (FastEthernet0 -> FastEthernet0/1)
* PC2 -> SW1 (FastEthernet0 -> FastEthernet0/2)
* Server1 -> SW1 (FastEthernet0 -> FastEthernet0/3)
* SW1 -> R1 (FastEthernet0/24 -> GigabitEthernet0/0)
* R1 -> R2 (GigabitEthernet0/1 -> GigabitEthernet0/0)
* R2 -> Azure Server (GigabitEthernet0/1 -> FastEthernet0)

## IP Addressing

* **Local Network (PC1, PC2, Server1, R1):**
    * Network Address: 192.168.1.0/24
    * R1 (GigabitEthernet0/0): 192.168.1.1
    * PC1: 192.168.1.10
    * PC2: 192.168.1.11
    * Server1: 192.168.1.12
* **R1 to R2 Link:**
    * Network Address: 203.0.113.0/30
    * R1 (GigabitEthernet0/1): 203.0.113.1
    * R2 (GigabitEthernet0/0): 203.0.113.2
* **Azure Network (R2, Azure Server):**
    * Network Address: 10.0.0.0/24
    * R2 (GigabitEthernet0/1): 10.0.0.1
    * Azure Server (FastEthernet0): 10.0.0.4

## Configuration Commands

### R1 Configuration

enable<br>
configure terminal<br>
interface GigabitEthernet0/0<br>
ip address 192.168.1.1 255.255.255.0<br>
no shutdown<br>
interface GigabitEthernet0/1<br>
ip address 203.0.113.1 255.255.255.252<br>
no shutdown<br>
ip route 0.0.0.0 0.0.0.0 203.0.113.2<br>
end<br>
copy running-config startup-config<br>

### R2 Configuration

enable<br>
configure terminal<br>
interface GigabitEthernet0/0<br>
ip address 203.0.113.2 255.255.255.252<br>
no shutdown<br>
interface GigabitEthernet0/1<br>
ip address 10.0.0.1 255.255.255.0<br>
no shutdown<br>
ip route 192.168.1.0 255.255.255.0 203.0.113.1<br>
end<br>
copy running-config startup-config<br>
![image](https://github.com/user-attachments/assets/78d78c38-2665-4e44-bf66-e8410fa540a2)<br>

### PC1 Configuration

IP Address: 192.168.1.10<br>
Subnet Mask: 255.255.255.0<br>
Default Gateway: 192.168.1.1<br>
![image](https://github.com/user-attachments/assets/d6406f48-9192-4ebb-b90f-f594f47cc18a)
![image](https://github.com/user-attachments/assets/57de3ef8-2a73-4f53-895a-e6b30b91b687)

### PC2 Configuration

IP Address: 192.168.1.11<br>
Subnet Mask: 255.255.255.0<br>
Default Gateway: 192.168.1.1<br>


### Server1 Configuration

IP Address: 192.168.1.12<br>
Subnet Mask: 255.255.255.0<br>
Default Gateway: 192.168.1.1<br>


### Azure Server Configuration

IP Address: 10.0.0.4<br>
Subnet Mask: 255.255.255.0<br>
Default Gateway: 10.0.0.1<br>

## Testing Connectivity

* From PC1 or PC2:
    * `ping 192.168.1.1`
    * `ping 203.0.113.2`
    * `ping 10.0.0.4`
![image](https://github.com/user-attachments/assets/8a515b49-d83a-4675-a5e4-f3b7ba3cb222)
![image](https://github.com/user-attachments/assets/ca6a6b52-80c5-4feb-aab3-85ffee5e2fa0)

## Breaking the Network for Troubleshooting

* **Disconnected Ethernet Cable (Hardware Issue):**
    * Disconnect the Ethernet cable between PC1 and SW1.
* **Network Troubleshooting (Azure Connectivity):**
    * On R1:
        ```
        configure terminal
        no ip route 0.0.0.0 0.0.0.0 203.0.113.2
        end
        ```
### Azure VMs:

* **Action:** Ensure your "DC-1" and "Client-1" VMs are running.
* **Action:** Verify that "Client-1" is joined to the "cyberlab.com" domain.

### osTicket:

* **Action:** Ensure your osTicket installation is running and accessible.
* **Action:** Verify that users "Karen" and "Ken" are created in osTicket.

## Part 2: Troubleshooting Scenarios

### Scenario 1: Password Reset (osTicket & Active Directory)

#### Ticket Creation:

* **Action:** As "Karen," open a web browser and go to your osTicket user portal (http://localhost/osTicket).
* **Action:** Create a new ticket with the subject "Password Reset Request."
* **Action:** In the body, state that you are locked out of your domain account and need a password reset.
![image](https://github.com/user-attachments/assets/00fef121-cc88-46bf-bf42-6a74ac6a21d7)
![image](https://github.com/user-attachments/assets/aac88645-1881-436f-84bd-0158009f6841)
![image](https://github.com/user-attachments/assets/13188f27-9675-41a4-b5d4-9d57766515c7)<br>

**Objective:**
#### Ticket Handling:

* **Action:** As an agent, log in to the osTicket agent panel.
* **Action:** Open the "Password Reset Request" ticket.
* **Action:** Verify "Karen's" identity (e.g., check her user profile in osTicket).
![image](https://github.com/user-attachments/assets/8082aa8e-9de8-4de0-85d2-ad5b65763a6c)
#### Password Reset:

* **Action:** Log in to "DC-1" as "mydomain.com\jane_admin."
* **Action:** Open "Active Directory Users and Computers (ADUC)."
* **Action:** Locate "Karen's" user account.
* **Action:** Right-click and select "Reset Password."
* **Action:** Enter a new temporary password.
![image](https://github.com/user-attachments/assets/e36d6e0d-6215-4f9f-a240-8bfdfaab4c7d)
![image](https://github.com/user-attachments/assets/b3e4fd97-474b-4bbb-bfd1-c144af56c099)
![image](https://github.com/user-attachments/assets/8fdc07de-6b8c-497c-bf4c-7f4a2d61dace)
#### Communication:

* **Action:** In osTicket, reply to "Karen's" ticket with the new temporary password.
* **Action:** Advise her to change the password upon her next login.
![image](https://github.com/user-attachments/assets/2c562aa1-b73a-474c-a58d-8cf78c7dfd91)
#### Verification:

* **Action:** As "Karen," attempt to log in to "Client-1" with the new password.
* **Action:** Verify that the password reset was successful.
* **Action:** Close the ticket in osTicket.
![image](https://github.com/user-attachments/assets/86117c3e-8a96-42db-892e-bb578c55b63e)

### Scenario 2: Hardware Issue (Packet Tracer & Physical Connectivity)

#### Ticket Creation:

* **Action:** As "Ken," open a web browser and go to your osTicket user portal.
* **Action:** Create a new ticket with the subject "Network Connection Issue."
* **Action:** In the body, state that your computer cannot connect to the network.
![image](https://github.com/user-attachments/assets/b21fbea4-cfc6-43e1-827e-908e25cdc09f)

#### Ticket Handling:

* **Action:** As an agent, log in to the osTicket agent panel.
* **Action:** Open the "Network Connection Issue" ticket.

#### Simulate the Issue:

* **Action:** In Packet Tracer, disconnect the Ethernet cable between PC1 and the switch.(Should already be done in previous steps)
  ![image](https://github.com/user-attachments/assets/8eb6ceea-ecae-4490-a54b-86296d4152d1)

#### Troubleshooting:

* **Action:** In osTicket, reply to "Ken's" ticket.
* **Action:** Instruct him to check the Ethernet cable connection to his computer and the wall outlet.
* **Action:** Ask him to verify that the network adapter is enabled.

#### Resolution:

* **Action:** In Packet Tracer, reconnect the Ethernet cable.
* **Action:** Instruct "Ken" to test his network connection. Have him ping the default gateway of 192.168.1.1.
* **Action:** Verify that the issue is resolved.
* **Action:** Close the ticket in osTicket.<br>
![image](https://github.com/user-attachments/assets/fe8e7e63-7e82-492a-9cf5-b4327e6a3b6a)<br>
![image](https://github.com/user-attachments/assets/6f2ff26d-d087-4244-81d8-1bc78d658a49)

### Scenario 3: Network Troubleshooting (Packet Tracer & Escalation)

#### Ticket Creation:

* **Action:** As a user, open a web browser and go to your osTicket user portal.
* **Action:** Create a new ticket with the subject "Remote Server Unavailable."
* **Action:** In the body, state that you cannot access a specific application or resource on the Azure server VM.

#### Ticket Handling:

* **Action:** As an agent, log in to the osTicket agent panel.
* **Action:** Open the "Remote Server Unavailable" ticket.

#### Troubleshooting:

* **Action:** In osTicket, reply to the ticket.
* **Action:** Instruct the user to ping the Azure server's simulated private IP address (10.0.0.4).
* **Action:** Instruct them to run `tracert 10.0.0.4`.
* **Action:** Use the command `192.168.1.1` to see if the default gateway is reachable.<br>
![image](https://github.com/user-attachments/assets/8c901e35-e197-44d0-8823-6a2f90da7489)
#### Escalation:

* **Action:** Since the user can ping the default gateway but not the remote server, escalate the ticket to a higher-tier support team.
* **Action:** Document the troubleshooting steps and findings in osTicket.

#### Resolution:

* **Action:** Add the default route back to the router. `ip route 0.0.0.0 0.0.0.0 203.0.113.2`
* **Action:** Test connectivity again.
* **Action:** Close the ticket in osTicket.<br>
![image](https://github.com/user-attachments/assets/9b2a8a8a-2424-4a7f-a284-43bdd51df4cf)<br>
![image](https://github.com/user-attachments/assets/0a701382-b1e5-4432-b9ab-f6fa29038411)


