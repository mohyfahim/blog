---
title: "Learning Rust: Developing Your Own Naivechain Blockchain"
seoTitle: "Rust: Build a Naivechain Blockchain"
seoDescription: "Learn Rust by building a Naivechain blockchain, exploring performance, security, and concurrency in a minimal implementation"
datePublished: Wed Aug 07 2024 15:23:03 GMT+0000 (Coordinated Universal Time)
cuid: clzk00p2f000209lcdlb6f1zv
slug: learning-rust-developing-your-own-naivechain-blockchain
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/0eqgB57xMeA/upload/f24d8e28e6f457a7354c66f5e49ca1ea.jpeg
tags: tutorial, opensource, rust, blockchain

---

In the world of blockchain, simplicity is often overshadowed by complex protocols and intricate algorithms. Yet, simplicity has its own charm and power, which is precisely what inspired the creation of [Naivechain](https://github.com/lhartikk/naivechain)—a minimal blockchain implementation designed to teach the basic principles of blockchain technology. In this blog post, I am excited to share a fresh take on Naivechain, rewritten in Rust, and invite you to join me on this journey to improve and optimize the project.

### Why Rust?

Rust is known for its performance, safety, and concurrency capabilities. These features make it an ideal choice for blockchain development, where performance and security are paramount. By rewriting Naivechain in Rust, we not only aim to leverage these benefits but also provide an opportunity for developers to learn Rust through a practical and engaging project.

### Project Overview

The Rust implementation of Naivechain maintains the core simplicity of the original project while introducing several enhancements. Here’s a quick overview of the project structure:

* **Main Component**: Initializes the blockchain and starts the HTTP server.
    
* **API Module**: Handles RESTful API requests for interacting with the blockchain.
    
* **Engine Module**: Manages the blockchain’s core logic, such as adding new blocks and validating chains.
    
* **Chain Module**: Defines the blockchain structure, including blocks and chain management.
    
* **Network Module**: Implements peer-to-peer networking for blockchain propagation using Libp2p.
    

### Key Features

1. **Improved Performance**: Rust’s zero-cost abstractions and memory safety features help achieve better performance without sacrificing safety.
    
2. **Concurrency Support**: By using Rust’s concurrency model, the project can handle multiple requests efficiently, making it more robust and scalable.
    
3. **Enhanced Networking**: With the integration of Libp2p, the project supports peer-to-peer networking, allowing for decentralized and distributed operation.
    

### Get Involved

This project is an open invitation for collaboration and innovation. Whether you’re a seasoned developer or new to Rust, your ideas and contributions are welcome. Here are some ways you can get involved:

* **Review the Code**: Check out the project on GitHub, explore the code, and suggest improvements or optimizations.
    
* **Share Your Ideas**: Have a feature in mind that could enhance the project? Share your thoughts, and let’s discuss how we can integrate it.
    
* **Contribute**: Found a bug or want to add a new feature? Submit a pull request and become a part of the project’s evolution.
    

### File Structure Overview

1. [`main.rs`](http://main.rs): The entry point of the application, setting up the Actix Web server and initializing the blockchain state.
    
2. [`api.rs`](http://api.rs): Handles the HTTP API for interacting with the blockchain, including endpoints for retrieving blocks and mining new ones.
    
3. [`chain.rs`](http://chain.rs): Defines the core blockchain structures and methods for block validation and manipulation.
    
4. [`engine.rs`](http://engine.rs): Manages the P2P message handling and processing of blockchain updates from peers.
    
5. [`net.rs`](http://net.rs): Manages the network layer using Libp2p, implementing P2P communication and network behavior.
    

## Chain.rs

The [`chain.rs`](http://chain.rs) file is a crucial part of your blockchain project, as it defines the structure and behavior of the blockchain itself. Below, we'll summarize the code, highlighting the important points to help you understand how it works.

### Overview

The [`chain.rs`](http://chain.rs) file consists of two main components: `Block` and `Chain`. These structures define the individual blocks in the blockchain and the blockchain itself.

### Key Structures and Functions

#### 1\. **Block Structure**

The `Block` structure represents a single block in the blockchain. Each block contains:

* `index`: The position of the block in the chain.
    
* `previous_hash`: The hash of the previous block, ensuring the integrity of the chain.
    
* `timestamp`: The time the block was created.
    
* `data`: The data stored in the block (could be transactions, messages, etc.).
    
* `hash`: The block's own hash, calculated using its contents.
    

```rust
#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, Eq)]
pub struct Block {
    pub index: usize,
    pub previous_hash: String,
    pub timestamp: u64,
    pub data: String,
    pub hash: String,
}
```

**Constructor Functions**:

* `new_with_hash`: Allows creating a block with a predefined hash (for testing or genesis block).
    
* `new`: Creates a new block and calculates its hash using the `calculate_hash` function.
    

#### 2\. **Chain Structure**

The `Chain` structure represents the blockchain as a whole. It maintains a list of blocks and handles operations on the blockchain.

```rust
#[derive(Debug, Serialize, Deserialize)]
pub struct Chain {
    pub next_index: usize,
    pub chains: Vec<Block>,
}
```

* **Important Methods**:
    
    * `new`: Initializes a new blockchain with a genesis block.
        
    * `replace_block_chain`: Replaces the current blockchain with a new one if it's valid and longer.
        
    * `is_valid_chain`: Validates the entire blockchain to ensure it hasn't been tampered with.
        
    * `is_valid_new_block`: Checks if a new block is valid by comparing it with the previous block.
        
    * `get_latest_block`: Retrieves the most recent block in the chain.
        
    * `add_block`: Adds a new block to the chain if it's valid.
        

#### 3\. **Hash Functions**

The file includes two functions to calculate the hash of a block, which is essential for maintaining the chain's integrity:

* `calculate_hash`: Generates a hash for a new block using its index, previous hash, timestamp, and data.
    
    ```rust
    pub fn calculate_hash(index: usize, previous_hash: &str, timestamp: u64, data: &str) -> String {
        let block_data = format!("{}{}{}{}", index, previous_hash, timestamp, data);
        let mut hasher = Sha256::new();
        hasher.update(block_data);
        let result = hasher.finalize();
        format!("{:x}", result)
    }
    ```
    

`calculate_hash_from_block`: Calculates the hash from an existing block structure.

```rust
pub fn calculate_hash_from_block(block: &Block) -> String {
    let block_data = format!(
        "{}{}{}{}",
        block.index, block.previous_hash, block.timestamp, block.data
    );
    let mut hasher = Sha256::new();
    hasher.update(block_data);
    let result = hasher.finalize();
    format!("{:x}", result)
}
```

### Important Points

* **Block Creation and Validation**: Blocks are created with a calculated hash to ensure data integrity. Each block points to the previous one using a hash, forming a chain.
    
* **Chain Integrity**: The blockchain is validated by checking the genesis block and ensuring all blocks are correctly linked with valid hashes and indices.
    
* **Chain Updates**: New blocks are added only if they are valid, and the chain can be replaced if a valid longer chain is received, ensuring consensus among nodes.
    
* **Data Structures**: The use of `Vec<Block>` for storing the chain and `usize` for indices helps manage the chain efficiently. Serialization with Serde allows easy transmission of blocks over the network.
    

This [`chain.rs`](http://chain.rs) file implements a simple yet effective blockchain mechanism in Rust, encapsulating both the concept of a block and the overall blockchain while ensuring integrity and consensus.

## Net.rs

The [`net.rs`](http://net.rs) file provides the networking functionality for the blockchain project, using the Rust library `libp2p` for peer-to-peer communication. This file is responsible for managing peer discovery and message exchange in the network, enabling the blockchain to function as a decentralized system.

### Key Components and Concepts

#### 1\. **P2PMessage Enum**

The `P2PMessage` enum defines the different types of messages that can be exchanged between peers in the network. These messages include:

* `QueryLatest`: Request the latest block from peers.
    
* `QueryAll`: Request the entire blockchain from peers.
    
* `ResponseBlockchain`: Send the current blockchain to peers.
    
* `QueryPeers`: Request the list of peers.
    
* `ResponsePeers`: Respond with a list of peers.
    
* `AddPeer`: Add a new peer to the network.
    

```rust
#[derive(Serialize, Deserialize, Debug)]
pub enum P2PMessage {
    QueryLatest,
    QueryAll,
    ResponseBlockchain(Vec<Block>),
    QueryPeers,
    ResponsePeers(Vec<PeerId>),
    AddPeer(String),
}
```

#### 2\. **Network Behavior**

The `P2PNetWorkBehaviour` struct combines two important network protocols: Gossipsub and mDNS. These protocols facilitate message propagation and peer discovery, respectively.

```rust
#[derive(NetworkBehaviour)]
pub struct P2PNetWorkBehaviour {
    pub gossipsub: gossipsub::Behaviour,
    pub mdns: mdns::tokio::Behaviour,
}
```

* **Gossipsub**: A publish-subscribe protocol that allows nodes to communicate by broadcasting messages to subscribers. It is used for disseminating blockchain updates and other messages.
    
* **mDNS (Multicast DNS)**: A protocol for automatic service discovery on a local network, allowing peers to find each other without needing a central registry.
    

#### 3\. **Transmit and Receive Handlers**

These structs manage message transmission and reception between different components of the system, allowing for asynchronous communication.

```rust
#[derive(Clone)]
pub struct TransmitHandlers {
    pub swarm_tx: UnboundedSender<P2PMessage>,
    pub router_tx: UnboundedSender<P2PMessage>,
    pub api_peers_tx: UnboundedSender<P2PMessage>,
}

pub struct ReceiveHandlers {
    pub api_peers_rx: Mutex<UnboundedReceiver<P2PMessage>>,
}
```

#### 4\. **Handling Swarm Events**

The `handle_swarm` function manages the swarm's events and message handling. It uses `tokio::select!` to asynchronously handle incoming messages and network events, such as discovering new peers or receiving messages.

```rust
pub async fn handle_swarm(
    mut swarm: Swarm<P2PNetWorkBehaviour>,
    topic: gossipsub::IdentTopic,
    transmit_handler: TransmitHandlers,
    mut rx: UnboundedReceiver<P2PMessage>,
) {
    log::info!("swarm task is started");

    let handle_msg = |msg: P2PMessage, swarm: &mut Swarm<P2PNetWorkBehaviour>| match msg {
        // Handle different message types
    };

    loop {
        tokio::select! {
            Some(msg) = rx.recv() => handle_msg(msg, swarm.borrow_mut()),
            event = swarm.select_next_some() => match event {
                SwarmEvent::Behaviour(P2PNetWorkBehaviourEvent::Mdns(mdns::Event::Discovered(list))) => {
                    for (peer_id, _multiaddr) in list {
                        log::info!("mDNS discovered a new peer: {peer_id}");
                        swarm.behaviour_mut().gossipsub.add_explicit_peer(&peer_id);
                    }
                },
                // Handle other swarm events
            }
        }
    }
}
```

* **Peer Discovery**: When a new peer is discovered via mDNS, it is added as an explicit peer in the Gossipsub network.
    
* **Message Propagation**: When a message is received or generated, it is published to the network using Gossipsub.
    

#### 5\. **Configuring the Network**

The `config_network` function sets up the network configuration, including the transport protocols (TCP and QUIC) and initializes the network behavior (Gossipsub and mDNS).

```rust
pub fn config_network(transmit_handler: TransmitHandlers, rx: UnboundedReceiver<P2PMessage>) {
    let mut swarm = libp2p::SwarmBuilder::with_new_identity()
        .with_tokio()
        .with_tcp(
            tcp::Config::default(),
            noise::Config::new,
            yamux::Config::default,
        )
        .unwrap()
        .with_quic()
        .with_behaviour(|key| {
            // Configure Gossipsub and mDNS
        })
        .unwrap()
        .with_swarm_config(|c| c.with_idle_connection_timeout(tokio::time::Duration::from_secs(60)))
        .build();

    let topic = gossipsub::IdentTopic::new("test-net");
    swarm.behaviour_mut().gossipsub.subscribe(&topic).unwrap();

    // Start listening on network interfaces
    swarm
        .listen_on("/ip4/0.0.0.0/udp/0/quic-v1".parse().unwrap())
        .unwrap();
    swarm
        .listen_on("/ip4/0.0.0.0/tcp/0".parse().unwrap())
        .unwrap();

    actix_web::rt::spawn(handle_swarm(swarm, topic, transmit_handler, rx));
}
```

* **Transport Protocols**: The network uses TCP and QUIC for transport, providing reliability and performance.
    
* **Message Handling**: The network is configured to handle incoming messages, peer discovery, and message propagation.
    

### Important Points

* **Peer-to-Peer Communication**: This file establishes a decentralized network using libp2p, allowing nodes to communicate and synchronize without a central server.
    
* **Message Types**: The `P2PMessage` enum defines the protocol for exchanging information between nodes, including querying the blockchain and discovering peers.
    
* **Network Behavior**: The combination of Gossipsub and mDNS facilitates efficient message propagation and peer discovery, enabling the blockchain to maintain consistency across nodes.
    
* **Asynchronous Handling**: The use of `tokio` and channels for message transmission and reception ensures that the network can handle events and messages efficiently.
    

The [`net.rs`](http://net.rs) file is essential for the decentralized nature of the blockchain, enabling communication and synchronization across a distributed network of peers.

## Conclusion

Rewriting NaiveChain in Rust is more than just a technical exercise; it’s a community-driven effort to explore the possibilities of blockchain technology using Rust. By simplifying blockchain concepts, we aim to make learning and contributing accessible to everyone.

I invite you to explore the project on [GitHub](https://github.com/mohyfahim/naivechain-rs) and join the discussion. Let’s work together to make this project better, more efficient, and a valuable resource for learners and developers alike.

Thank you for reading, and I look forward to your contributions and insights!