##What is Storj?

Storj (pronounced: storage) aims to become a cloud storage platform that can’t be censored, monitored, or have downtime. It is the first decentralized, end-to-end encrypted cloud storage platform.

Storj is made up of  a bunch of interlocking pieces working together to create a unified system. As people interact with various parts of this system, they get a different idea of what Storj is. A home user doesn’t need any knowledge of the Bridge or of the protocol in order to share storage space, and a developer doesn’t need to know anything about the home users in order to use the Storj API. Each person can have a drastically different experience while interacting with the same system. So what is Storj? It’s a protocol, a suite of software, and the people who design, build, and use it.

###The Storj Protocol

Storj’s core technology is an enforceable peer-to-peer storage contract. It’s a way for two people (or computers) to agree to exchange some amount of storage for money without knowing each other. We call the computer selling space the “farmer,” and the computer purchasing space the “renter.” The renter and farmer meet, negotiate an agreement, and move data from the renter to the farmer for safekeeping.

####Contracts & Audits

A contract has a set duration. Over this time, the renter periodically checks that the farmer is still available. The farmer responds with a cryptographic proof that it still has the file. Finally, the renter pays the farmer for each proof it receives and verifies. This process of challenge -> proof -> payment is referred to as an “audit,” as the renter is auditing the farmer’s storage. At the end of the contract period, the farmer and renter are free to renegotiate or end the relationship. 

While the core technology allows for any type of payment, some types are better suited than others. Traditional payment systems, like ACH or SEPA, are poorly suited to paying on a per-audit basis. They’re slow, hard to verify, and often come with expensive fees. The ideal payment method for the Storj Protocol is a cryptocurrency micropayment channel. It allows for extremely small payments that are immediately verifiable and secure, with minimal fees. This means that payments and audits can be paired as closely as possible.

Enforcement follows a simple tit-for-tat model: if the farmer fails an audit, i.e. is offline or can’t demonstrate that she still has the data, then the renter doesn’t have to pay. After all, he’s no longer getting the service he was paying for. Similarly, if a renter goes offline, or fails to make a payment on time, the farmer can drop the data, and look for a new contract from someone else. As long as both parties are following the terms of the contract, everyone ends up better off.

Pairing the payments directly to the audits minimizes the risk of dealing with a stranger. If the file gets dropped halfway through the contract, the renter only paid for the service actually performed, as proven by the audits. He needs to find a new farmer, but is not out any significant amount of money. If a renter disappears or stops paying a farmer, the farmer has received payment for all her previous services already. She’s only missing one audit payment, and the time it takes to find a new renter to buy that space.

####The Storj Network

To enable renters and farmers to meet each other, the contracting and negotiation system has been built on top of a distributed hash table (DHT). A DHT is basically a way of self-organizing a bunch of nodes into a useful network. We’re using a modified version of an algorithm called [Kademlia])https://prestwi.ch/kademlia-and-colors/).

Instead of having a central server register every node and coordinate all contracts, the DHT lets farmers and renters broadcast their contract offers to a wide group of nodes. Interested nodes can easily contact the person who made the contract offer. That way farmers and renters can find any number of potential partners, and buy or sell storage space on a broad permissionless market.

To find a partner, a node can sign an incomplete contract and publish it to the network. Other nodes on the network can subscribe to certain types of contracts (i.e. types they might be interested in) and respond to these published offers. This model is called publish-subscribe or pub/sub. Nodes can easily determine what contracts they’re interested in, and forward on contracts to other nodes they think might be interested.

Together, the contracting system and the network form what we call the Storj Protocol. It’s a description of how nodes on the network behave, how nodes communicate with other nodes, how contracts get negotiated and executed, and everything else necessary to buy and sell storage space on a distributed system. Anyone can implement the Storj Protocol in any way they please. We’d love to see more people using it to build awesome apps and distributed systems.

###The Storj Toolset

The protocol contains all the tools necessary to securely make storage contracts, but it’s missing a lot of things. It functions, but it’s not useful yet. To be useful to a renter, the system needs availability, bandwidth, and any number of other commitments in the form of a Service Level Agreement (SLA). The farming software needs management features to avoid using excessive resources and automation features to effectively deploy to multiple hosts. Instead of trying to fit all these features into the core protocol, we’ve opted to address them in an additional software layer. To make this network useful and easy to interact with, we’re releasing two tools: *Storj Share* and *Bridge*.

####Storj Share

Storj Share is the reference farming client. It allows users to easily setup and run a farm on any machine. Storj Share is available as a command-line interface (CLI) for more advanced users and to enable automation. The CLI allows the user to set parameters like the amount of storage space to share, storage location, and a payment address. It also handles contract negotiation, audit responses, and all other network communications.

We’re also releasing a Storj Share graphical user interface (GUI) in order to streamline the farming process for our non-technical users. Anyone can download the Storj Share GUI, fill in a few fields, and join the network. The GUI is a wrapper around the CLI which does all the heavy lifting. After initial setup, the user rarely needs to interact with the Storj Share GUI. They should be able to set it up, minimize it, and let it run in the background.

If the user opts in to data collection, Storj Share will also collect system telemetry. This data might include hard drive capacity and level of utilization as well as information about network connection quality. The telemetry data gets sent back to Storj Labs so that we can use it to improve the network and our software. In the future, Storj Share could even enable people to opt in to special services and programs. For example, we would love to give people the ability to share their hard drive space for free with organizations they support (like the Internet Archive or Project Gutenberg). There are any number of interesting possibilities.

####Bridge

To help renters use the network, we’ve also created Bridge. Bridge is designed to be deployed to a production server to handle contract negotiation, auditing, payments, availability, and a number of other needs. Bridge exposes these services and, by extension, storage resources via an Application Programming Interface (API) and client. The client is designed to be integrated into other apps, so that any application can use a Bridge server to store data on the Storj network without having to be a part of the network.

As its name implies, Bridge is a centralized bridge into the decentralized Storj network. Its goal is to allow traditional applications to interact with the Storj network like they would with any other object store. It distills all the complexities of p2p communication and storage contract negotiation to push and pull requests. Unlike most object stores, Bridge doesn’t deal directly in objects, but rather in references to objects. It stores pointers to the locations of the objects on the distributed network, as well as the information necessary to audit those objects. Ideally, no data will transit through Bridge, but rather will be transmitted directly to farmers on the network.

The Bridge Client handles all the client-side work to use the network effectively. It encrypts files as they enter the network, preserving privacy and security. To ensure availability, it shards files, applies erasure coding, and spreads the shards across multiple farmers. The client then communicates with the Bridge to manage each of the shard locations on the network, and helps the user manage their encryption keys locally. While the initial implementation of the Bridge Client is a Node.js package, the end goal is to have it available in many other languages.

####The Storj API

Our core service is an object store, similar to Amazon S3. This object store is managed by a set of public Bridge nodes. We maintain the infrastructure to negotiate contracts, manage payments, audits, etc. Our customers interact with our Bridges via the Bridge Client, and never even have to know they’re using a distributed network. The API is designed for usability, so everything complicated gets handled behind the scenes to provide a smooth, extensible development experience.

We use our extensive knowledge of the network to provide the top-notch quality of service. The decisions our Bridge makes are based on historical interactions with countless farmers. We use data about their performance, as well as their self-reported telemetry data, to intelligently distribute data across the network. We optimize for high uptime as well as fast retrieval.

Account management features are available via the Bridge Client, or via our beautiful webapp. Again, the function of the GUI is to make the experience as smooth as possible. We believe that user experience (UX) is tragically neglected by most developer-oriented services, and because of this, we’re designing the product with simplicity and usability in mind. Instead of offering a wide range of cloud computing and storage services, we aim to offer a single elegant user experience.

With the Storj API, we’re trying to build the ideal tool for developers like us: people who care about time-to-prototype, high-quality code, and rapid iteration. We want to provide tools and support to small teams, rapidly-scaling products, and individual developers. We understand that every developer actually works for herself and the projects she cares about. This is a labor of love for us. We want to build an object store that gets out of the way so that developers can concentrate on building the projects they love.

###The Storj Community

More important than the protocol or the software are the people involved: The Storj Community. 

####Storj Labs

[Storj Labs Inc.](https://storj.io/team) was founded in 2015 to push development of the protocol, and to help build demand for the network. We’re a team of hackers headquartered in Atlanta, with contributors around the world. We’re passionate about decentralization, free software, and the impact of technology on society. We’re dedicated to building better tools so developers can make better products. And we believe that the cloud was built for everyone: Anyone, anywhere can be the cloud!

Our role in the ecosystem is two-fold. First, we finance development of the network and tools. Our team of full- and part-time engineers churns out high-quality code night and day. We ensure that the lights stay on, the data keeps flowing and the bugs get squashed. Our code runs on thousands of users’ machines, all cooperating to make the network. And we understand how critical it is to keep all those machines running smoothly. 

Second, we work to source demand for the network. It’s slower to upload data to the network than to farm, and it requires more development time. You have to integrate the network with an app, and then manage your own infrastructure to issue audits. We can help people discover and use those tools. Applications need reliability, and companies need a service-level agreement. We can provide those features as a company where a distributed network never could. Keeping demand high is essential to the security and viability of this network. After all, if nobody buys storage space there won’t be any farmers.

####Be The Cloud

Storj is bigger than just a company – we’re a community! We’re a group of enthusiasts, developers, and farmers who are excited about building and being part of the future of cloud storage. Our vision doesn’t stop at the office door. Thousands of people participate in our community chat; the Storj codebase has dozens of contributors. People show up and help us because they share our passion and our goals.

Our community provides time, talent, ideas, and resources that we could never come up with on our own. We have community members speaking at conferences for us around the world, devoting a dozen servers to stress test the network, constantly answering questions, and helping others in our community chat. Community feedback has caught some of our strangest bugs. Our founders and several of our employees joined the community first, and the company later. The community has been critical to everything we’ve achieved.

As we move together towards a full release, we’re excited to spend more time and resources interacting with and supporting our community. Together we can build, teach, learn, and change the future of object storage. Be the cloud!
