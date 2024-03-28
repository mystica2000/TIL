# Rabbithole - networking on games

went into rabbit-hole of how networking works in native multi-player games (not web):

In web, most of the time we use web sockets for real time. It's application protocol for the web which uses TCP.


## Game networking 
In native-mobile/desktop games uses combination of both TCP/UDP for it to function. 

Like http (TCP) for handling updates/fixes/user registration etc since it needs to be reliable. 

UDP for game movements which needs to be fast, loss of packets (jitter) is reasonably OK here. 

Reliability is tradeoff since we opt for speed. Even raw UDP isn't be 100% suitable since it's connection less protocol. Libraries could implement custom protocol on top of UDP for security/serializability/reliability/encryption measures. 

## History & Evolution of networking model:

1. peer-to-peer networking model (mesh network where every device is connected to every other device for multi-player). It introduces series of problems like NAT Traversal (not all devices can be accessed from public internet due to proxies/firewall: this is generally a issue when working with peer to peer networking protocols: check out my article on webrtc to get the idea), cheating potential since no central server. 

2. Client-Server: server acts authoritative person connecting all the clients. But here it introduces latency (due to round trip from client to server before proceeding like game movements)

3. Client-Side prediction: Here client runs more code than previously handling game movements without relying on the server. Don't wait for the round trip to be returned for the movements to take place, continue doing what you are doing. But the server still acts an authority where if it didn't received any data due to loss of network it will revert back to it's known state (server reconciliation)