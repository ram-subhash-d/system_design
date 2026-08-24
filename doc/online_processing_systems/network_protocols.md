Each service/client on the internet is identified by an IP address(system or server) and a port(one application or connection). Every IP packet sent on the internet has a destination IP address and port number, and the source IP address and port number.

# Domain name system(DNS)
It is a service that takes the server name and gives the servers IP address(for the routers).

# Protocols
Once the connection is established between client and server, they communicate using some standard protocol. Protocol is a formal description of the message format, it specifies the rules to be followed during the exchange. Below are some of the well known protocols.
* Internet protocol(IP) : This is the protocol that is needed by the routers to send the packet from the source to the destination on the network.
* Hypertext transfer protocol(HTTP) : Its a protocol on top of IP. It is used to transfer web pages/content(text/vedio/audio) between client and server on the internet. curl(client url) is command line tool to communicate on HTTP. HTTP port number on the server machine will be 80.