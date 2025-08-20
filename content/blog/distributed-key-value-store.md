+++
date = '2025-08-18T18:55:47-04:00'
draft = false
title = 'Distributed Key Value Store'
+++


# Building and locally deploying a Distributed Key Value Store 

This post explains how to build a simple distributed key value store in Golang, and how to locally 
deploy a simple cluster in Kubernetes that can demonstrate some of the algorithms that it uses. 

Note: We will not be writing our own implementation of the algorithms that get us a good distributed
key value store. We will be using libraries such as [Memberlist](https://github.com/hashicorp/memberlist) and [Raft](https://github.com/hashicorp/raft) by Hashicorp. This post details
how to actually implement them in your project, as well as why you may want to use them, plus some Kubernetes along the way.

## High level Overview

#### What is a distributed system? 

A system which coordinates many separate computers together to achieve a specific task. This is an important study in 2025 as the entire internet (social media, cloud computing...) as well as modern AI tech stacks (think AI training clusters) are all distributed systems. 

#### How does a distributed key-value store relate to distributed systems 

Every computer within a distributed system (or cluster) needs to be coordinated in such a way as to ensure  that their resources are being used correctly. Ideally you want all your nodes (individual computers) to be used efficiently, and for the whole system to respond quickly to changes in demand or unexpected failures (a node crashing). To achieve that, you need a really robust way for nodes to communicate with each other in relation to what each node is doing, their current status. This is where distributed key value stores come in. Their specialty is that they work in such conditions where many different nodes all need to agree on some core piece of information.

#### Why can't a regular database be used for this? 

Regular DBs (or even key value based ones like Redis) aren't really designed to work in situations in which you don't have an authoritative source of truth. Maintaining ACID consistency across many nodes becomes a huge problem which these DBs were not designed to solve. 

#### What makes these key value stores good enough to work in such conditions? 

Distributed key value stores implement some sort of a [consensus](https://en.wikipedia.org/wiki/Consensus_(computer_science)) algorithm which provides for a way for all unique instances of that key value store to agree on a certain core of truth. This post implements the RAFT algorithm through the [this](https://github.com/hashicorp/raft) library. 

These key value stores are used for configuration management, leader election, distributed locks and passing general high level data between each node. Notably, these algorithms would NOT be used to send actual data between nodes.

#### What about membership and discovery? how does the cluster know when a new node joins, leaves or crashes? 

In this project, I've elected to use an algorithm called gossip, provided by [this](https://github.com/hashicorp/memberlist) library. 
Gossip works in the same manner as a virus spreading, or an internet rumour: every node only needs to keep track of say 4 other nodes, and only worry about spreading the new info to those few nodes. While there is a delay to this information spreading, within only a few steps, every node in even a very massive cluster, could be aware of that piece of data being passed around. An incoming node should only be aware of a single other node, and its address, which would in turn pass on that new nodes info to every other node through the gossip algorithm. 

#### How do I deploy this? 

As I was writing this project, I tested it by starting up two separate terminals, each getting their own instace of the key value store running. They would ge given different ports, and the second instance would be given the gossip address of the first. They would discover each other through that, join the cluster, and be able to communicate and share the key-value store. In reality, a more robust deployment process is need, which is where Kubernetes comes in. 

Kubernetes is a tool to orchestrate the deployment of containers (like a conductor managing an orchestra). A container is a "wrapper" around a program you want to run that contains everything that program needs (all dependencies and configs). Each instance of the key value store becomes a container. Kubernetes then packages that container into a pod. And we deploy a cluster that has multiple of those pods. 

Kubernetes actually has its own version of the key-value store we are building, called [etcd](https://etcd.io/). It is absolutely critical for K8s, acting as the cluster's single source of truth. The Kubernetes API server writes the desired cluster state to etcd, and various components, like the kubelet on each node, read from the API server to achieve that state, creating a robust and resilient control plane.

In our project, the pods will join a StatefulSet, a specific kind of cluster focused on stable network identities (in case of failure) and each pod be given a unique host name. A private network will also be created to facilitate the communication over the different protocols. Lastly, each node will get a publicly visible (to the user) http URL to which the user can send get and set requests. 

<!-- ### TLDR
1. Each node has has an HTTP port that accepts either a GET or SET request. A SET sets a value in it's key value store and a get returns the approriate value given a key. Each node is wrapped in a Docker container for easier deployment. This HTTP node will be used for communication with the user. 
2. Each node also gets a RAFT port and a Gossip port. Upon startup, a node will be given the address of a previously existing node within a cluster. The new node will ping that known node through Gossip and request to join. The process of joining through gossip will also join it to the Raft cluster and after that Raft will take over the communication of the data.
3. The kubernetes cluster will be created locally through Minikubes. 
4. The user can interact with the cluster by sending Get and Set requests to some nodes, and then check if different nodes have the correct key - value pairs, representative of how a real system might perform.  -->


## Code explanation 

### `raft.go` 

`raft.go` handles the setup and the running of RAFT. `type kvFsm struct` implements the RAFT interface as specified by the RAFT library by Hashicorp. The key here is the `func (kf *kvFsm) Apply(log *raft.Log) any {}` which is the function responsible for implementing the incoming changes to the log into the current nodes key value map. The RAFT algorithm in turn, works by making all member nodes agree on a certain set of previous steps, logs, that were taken to get to the current state. New logs are added onto the log list, and each node's `apply()`, applies them. If the log gets too long, the Raft library provides the option of snapshotting and storing that log out of memory, and restoring it if necessary. 

```go
func (kf *kvFsm) Apply(log *raft.Log) any {
	switch log.Type {
	case raft.LogCommand:
		var sp setPayload
		if err := json.Unmarshal(log.Data, &sp); err == nil && sp.Key != "" {
			if err := kf.store.Add(sp.Key, sp.Value); err != nil {
				return fmt.Errorf("could not add to store: %s", err)
			}
			return nil
		}

		var dp deletePayload
		if err := json.Unmarshal(log.Data, &dp); err == nil && dp.Key != "" {
			if err := kf.store.Delete(dp.Key); err != nil {
				return fmt.Errorf("could not delete from store: %s", err)
			}
			return nil
		}

		return fmt.Errorf("could not parse payload: unknown operation type")
	default:
		return fmt.Errorf("unknown raft log type: %v", log.Type)
	}
}
```

In the implementation of the apply function, the code simply takes the latest log, attempts to decode it from json into an existing struct and performs the appropriate operation on the store (set or delete), based on the type of struct it could be decoded into. Otherwise, it errors out.


### `gossip.go` 

In the same manner as `raft.go` implements all the necessary RAFT logic, `gossip.go` implements all the necessary Gossip (Memberlist) logic. Memberlist provides a straightforward interface for joining a cluster. All thats needed is an instance of `*memberlist.Memberlist` for itself, and an address of at least one other already existing node. Joining is as easy as calling 

```go
func (gm *GossipManager) JoinCluster(existing []string) error {
	n, err := gm.memberlist.Join(existing)
	if err != nil {
		return err
	}
	fmt.Printf("Successfully joined cluster. Connected to %d nodes\n", n)
	return nil
}
```

As Memberlist is responsible for discovery and dynamic cluster resizing, while Raft is responsible for consensus, and the two services use separate communication protocols, they ideally should be separate onto different ports. In my specific case, these ports stay constant however in a case where that is not the case (maybe you are running this on lo    calhost on two different terminals with completelyt different ports), the Gossip algorithm needs to pass some sort of a message to every other node when a connection happens. Memberlist provides a convenient interface that executes specific functions on every node if any member node performs a specific function. I used this to my advantage to pass around the Raft port to other nodes, and perform the Raft connection based on that info. 

```go
func (e *myEvents) NotifyJoin(n *memberlist.Node) {
	e.gm.AddRaftNode(n)
}

func (gm *GossipManager) AddRaftNode(node *memberlist.Node) {
	if gm.r == nil {
		return
	}

	if gm.r.State() != raft.Leader {
		return
	}

	newRaftPort := string(node.Meta)
	raftAddr := fmt.Sprintf("%s:%s", node.Addr.String(), newRaftPort)
	err := gm.r.AddVoter(raft.ServerID(node.Name), raft.ServerAddress(raftAddr), 0, 0).Error()
	if err != nil {
		panic(fmt.Sprintf("failure when connecting to raft: %s", err))
	}
	fmt.Printf("Successfully connected RAFT!")
}
```

NotifyJoin performs AddRaftNode on every node when a new Memberlist node joins a cluster. In Raft, only a leader node can add a new member. If the current node is the leader, then the raft port is extracted from the gossip nodes metadata, formatted, and used to add the node to Raft. 

I would note that this is not necessary when deploying in a K8s system. All the ports stay constant, only the host name changes, and K8s provides many ways to pass that around. This was done before I added K8s, when I was testing everything in multiple terminals with different CLI args to each node. It's a good learning exercise nontheless. 

### Kubernetes Cluster setup 

To demonstrate this system in a realistic and useful manner, I've decided to setup a simple 3 pod K8s cluster. I used Minikubes to create a local environment for K8s to run on, and deployed on there. The instructions for how to set it up are found in `README.md`. The K8s' `deployment.md` has 3 key components to it: 
1. A `StatefulSet` that contains 3 replicas of the Pods that each run a container with the key-value store
2. A local headless network to allow for communication between the pods in the StatefulSet
3. An http service for each pod to expose its http port to the outside world, which in this case is the terminal of the user. We need this as you can't just send http requests to a pod, you need a way to connect it. 



## Potential future changes 

1. Add some sort of a UI to visualize each node and allow the user to shut down and create pods, and see if they join the cluster. 
2. Use Helm to simplify the K8s deployment.
