---
layout: default
---
Domiugo
=======
dom-e-ah-go: A distributed home automation system for controlling lights, music, TVs, and pretty much anything that can be controlled by a remote or with an HTTP interface. "Domiugo" is a portmanteau of "Domus" and "Iugo" - the Latin for "Home" and "Connect."

Inception
---------
I own an old linux PC that currently sits in the corner of my living room beside my entertainment system. The computer acts as a media server and is hooked up to my system's receiver but is rarely used when guests are over because it has to be controlled via SSH. One day I had the idea of remotely controlling my media server by using an application on my phone or laptop and then a lightbulb went off: "What else can I control this way?"

Setup
-----
A Domiugo network is made up of three different kinds of components:
* Central Command: The root node of the network. Central Command is aware of the capabilities of the network and issues commands to members of the network on behalf of the user. 
* Nodes: A Node is a device connected to the Domiugo network. On start-up, a Node connects to Central Command and publishes its capabilities. The node can then be issued commands.
* Modules: A Module is a kernel of functionality. Modules are installed into Nodes, giving the Node whatever capability is represented by the Module. Modules are often dependant on additional hardware or software eg. A "TV Control" module requires an IR receiver/transmitter in order to function.

Central Command issues commands in two different situations:
* The user wants something done: An app will allow the user to control the devices on the network by telling Central Command to issue the appropriate commands.
* Scripted behaviour: Central Command can be scripted to react to certain network conditions. A common example of a network event is "User Entered Network" which effectively means "User X just got home."

Progress
--------
Domiugo is in its infancy; the project was started during the summer months of 2013 and development continues to this day.

Under the hood, Domiugo is 100% [Scala][1]. [Akka][2] is used for managing Nodes and [Spray][3] is used for the HTTP/RESTful interfaces provided by all Domiugo components. The source can be accessed from by [public GitHub repository][4]

[1]: http://www.scala-lang.org/
[2]: http://akka.io/
[3]: http://spray.io/
[4]: https://github.com/JakeGreene/Domiugo
