# Networking basics

## End Device Addressing

- IP address - This identifies the host on the network.
- Subnet mask - This is used to identify the network on which the host is connected.
- Default gateway - This identifies the networking device that the host uses to access the internet or another remote network.

## Differenciate active tun connections

- Sometimes when playing with multiple ovpn (I often use htb and thm and do not always reboot my vm) you might end up with 2 tun interface in your `ip add`
- To know which one is the active one you just need to check the routes using `ip route` the one that has more routes is the one that is the current ovpn connection.

## Resources

{% embed url="https://skillsforall.com/course/getting-started-cisco-packet-tracer" %} Getting Started with Cisco Packet Tracer (this tool is very fun and useful to use when trying to understand networking) {% endembed %}
{% embed url="https://skillsforall.com/course/networking-essentials" %} Networking Essentials by Cisco {% endembed %}
