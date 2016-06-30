##What is Storj?

Storj (pronounced: storage) aims to become a cloud storage platform that can’t be censored, monitored, or have downtime. It is the first decentralized, end-to-end encrypted cloud storage platform.

Storj is made up of  a bunch of interlocking pieces working together to create a unified system. As people interact with various parts of this system, they get a different idea of what Storj is. A home user doesn’t need any knowledge of the Bridge or of the protocol in order to share storage space, and a developer doesn’t need to know anything about the home users in order to use the Storj API. Each person can have a drastically different experience while interacting with the same system. So what is Storj? It’s a protocol, a suite of software, and the people who design, build, and use it.

###The Storj Protocol

Storj’s core technology is an enforceable, peer-to-peer, storage contract. It’s a way for two people (or computers) to agree to exchange some amount of storage for money without knowing each other. We call the computer selling space the “farmer,” and the computer purchasing space the “renter.” The renter and farmer meet, negotiate an agreement, and move data from the renter to the farmer for safekeeping.

####Contracts & Audits

A contract has a set duration. Over this time, the renter periodically checks that the farmer is still available. The farmer responds with a cryptographic proof that it still has the file. The renter pays the farmer for each proofit receives and verifies. This process of challenge -> proof -> payment is referred to as an “audit,” as the renter is auditing the farmer’s storage. At the end of the contract period, the farmer and renter are free to renegotiate or end the relationship.
 
While the core technology allows for any type of payment, some types are better suited than others. Traditional payment systems, like ACH, are poorly suited to paying on a per-audit basis. They’re slow, hard to verify, and come with expensive fees. The ideal payment method for the Storj Protocol is a crypto-currency micropayment channel. It allows for extremely small payments that are immediately verifiable and secure, with minimal fees. This means that payments and audits can be paired as closely as possible.

Enforcement follows a simple tit-for-tat model. If the farmer fails an audit, i.e. are offline or can’t demonstrate that they still have the data, then the renter doesn’t have to pay. After all, they’re no longer getting the service they’re paying for. Similarly, if a renter goes offline, or fails to make a payment on time, the farmer can drop the data, and look for a new contract from someone else. As long as both parties are following the terms of the contract, everyone ends up better off.

Pairing the payments directly to the audits minimizes the risk of dealing with a stranger. If the file gets dropped halfway through the contract, well, the renter only paid for the service actually performed, as proven by the audits. He needs to find a new farmer, but is not out any significant amount of money. If a renter disappears or stops paying a farmer, the farmer has received payment for all her previous service already. She’s only missing one audit payment, and the time it takes to find a new renter to buy that space.

####The Storj Network

To enable renters and farmers to meet each other, the contracting and negotiation system has been built on top of a distributed hash table (DHT). A DHT is basically a way of self-organizing a bunch of nodes into a useful network. We’re using a modified version of an algorithm called Kademlia.

Instead of having a central server register every node and coordinate all contracts, the DHT lets farmers and renters broadcast their contract offers to a wide group of nodes. Interested nodes can easily contact the person who made the contract offer. That way farmers and renters can find any number of potential partners, and buy or sell storage space on a broad permissionless market.

To find a partner, a node can sign an incomplete contract, and publish it to the network. Other nodes on the network can subscribe to certain types of contracts (i.e. types they might be interested in) and respond to these published offers. This model is called publish-subscribe or pub/sub. Nodes can easily determine what contracts they’re interested in, and forward on contracts to other nodes they think might be interested.

Together the contracting system and the network form what we like to call the Storj Protocol. It’s a description of how nodes on the network behave, how nodes communicate with other nodes, how contracts get negotiated and executed, and everything else necessary to buy and sell storage space on a distributed system. Anyone can implement the Storj Protocol in any way they like. We’d love to see more people using it to build awesome apps and distributed systems.

###The Storj Toolset

The protocol contains all the tools necessary to securely make storage contracts, but it’s missing a lot of things. It functions, but it’s not useful yet. To be useful to a renter, the system needs availability, bandwidth, and any number of other service level commitments. The farming software needs management features to avoid using excessive amounts of resources, and automation features to effectively deploy to multiple hosts. Instead of trying to fit all these features into the core protocol, we’ve opted to address them in an additional software layer. So to make the network useful, and easy to interact with, we’re releasing two tools: StorjShare, and Bridge.

####StorjShare

StorjShare is the reference farming client. It allows users to easily setup and run a farm on any machine. StorjShare is available as a CLI for more advanced users and for automation. It allows the user to set parameters like storage amount and location, as well as a payment address, and handles contract negotiation, audit responses, and all other network communications.

We’re also releasing a StorjShare GUI. The goal is to streamline the farming process for non-technical users. Anyone can download the StorjShare GUI, fill in a few fields, and join the network. The GUI is a wrapper around the CLI, which does all the heavy lifting. The user, ideally, rarely needs to interact with the StorjShare GUI after initial setup. They should be able to set it up, minimize it, and let it run in the background.

If the user opts in to data collection StorjShare will also collect system telemetry. This data might include hard drive capacity and fill level, or information about network connection quality. The telemetry gets sent back to Storj Labs, so that we can improve the network and our software. In the future, StorjShare could enable people to opt into special services and programs. We’d like to give people the option to share hard drive space for free with organizations they support (like the Internet Archive, or Project Gutenberg). There are any number of cool things we can do.

####Bridge

To help renters use the network, we’ve also created Bridge. Bridge is designed to be deployed to a production server to handle contract negotiation, auditing, payments, availability and a number of other needs. Bridge exposes these services and by extension storage resources via an API and client. The client is designed to be integrated into other apps, so that any application can use a Bridge to store data on the network, without being part of the network.

As its name implies, Bridge is a centralized bridge onto the decentralized network. Its goal is to allow traditional applications to interact with the network like they would another object store. Distill all the complexities to put and get requests. Unlike most object stores, Bridge doesn’t deal directly in objects, but rather in references to objects. It stores pointers to the locations of the object on the distributed network, and the information necessary to audit those objects. Ideally, no data transits through Bridge.

The Bridge Client handles all the client-side work to use the network effectively. It encrypts files as they enter the network, to preserve privacy and security. To ensure availability it shards files, applies erasure coding, and spreads the shards across multiple farmers. It communicates with Bridge to manage shard locations on the network, and helps the user manage their encryption keys locally. The initial implementation of the Bridge Client is a Node.js package, but in the future it’ll be available in many languages.

####The Storj API

Our core service as a company is an object store, similar to Amazon S3. This object store is manage by a set of public Bridge nodes. We maintain the infrastructure to negotiate contracts, manage payments, audits, etc. on behalf of our customers. Our customers interact with our Bridge via the Bridge Client, and never even have to know they’re using a distributed network. The API is designed for usability, and everything complicated gets handled behind the scenes to provide a smooth development experience.

We use our extensive knowledge of the network to provide the best possible quality of service. The decisions our Bridge makes are based on long histories of interaction with various farmers. We use data on their actual performance, as well as their self-reported telemetry data to intelligently distribute data across the network. We optimize for uptime as well as retrievability.

Account management features are available via the Bridge Client, or via on online Bridge GUI. Again, the function of the GUI is to make the experience as smooth as possible. We believe that UX is tragically neglected by most developer tools, and that because because we offer only one product, instead of a wide range of cloud computing and storage services, we can offer a simple and beautiful user experience.

We’re trying to build the ideal tool for developers like us. For people who care about time-to-prototype, high-quality code, and rapid iteration. We believe that it’ll be quicker and more efficient to use our service than to build, deploy, and manage a custom production-grade Bridge node. We want to provide tools and support to small teams, rapidly-scaling products, and individual developers. We want to build an object store that gets out of the way so that developers can go back to working on products they love.

###The Storj Community

More important than the protocol or the software are the people involved.
 
####Storj Labs

Storj Labs Inc. was founded in 2015 to push development of the protocol, and to help build demand for the network. We’re a team of hackers headquartered in Atlanta, with contributors around the world. We’re passionate about decentralization, free software, and the impact of technology on society. We’re dedicated to building better tools for developers to make better products. We believe that anyone, anywhere can be the cloud.

Our role in the ecosystem is two-fold. First, we finance development of the network and tools. We have a team of full- and part-time engineers turning out high-quality code night and day. We ensure that the lights stay on, the data keeps flowing and the bugs get patched. Our code runs on thousands of machines that all cooperate to make the network, and we understand how critical it is to keep them running smoothly.

Second, we work to source demand for the network. It’s slower to upload data to the network than to farm, and it requires more development time. You have to integrate the network with an app, and then manage your own infrastructure to issue audits. We can help people discover and use those tools.  Applications need reliability, and companies need a service-level agreement. We can provide those as a company. Keeping demand high is essential to the security and viability of the network. After all, if nobody’s buying space there won’t be any farmers.

####Be The Cloud

Storj is more than just a company, we’re a community. We’re a group of enthusiasts, developers, and farmers who are excited about building and being part of the future of cloud storage. Our vision doesn’t stop at the office door. Thousands of people participate in our community chat; the codebase has dozens of unpaid contributors. People show up and help us because they share our passion and our goals.

Our community provides time, talent, ideas, and resources that we could never come up with on our own. We have community members speaking at conferences for us around the world, devoting a dozen servers to stress testing the network, and constantly answering questions and helping others in our community chat. Community feedback has caught some of our strangest bugs. Our founders and several of our employees joined the community first, and the company later. The community has been critical to everything we’ve achieved.

As we move together towards a product release, we’re excited to spend more time and resources interacting with and supporting our community. Together we can build, teach, learn, and hopefully change the future of object storage. Be the cloud!
