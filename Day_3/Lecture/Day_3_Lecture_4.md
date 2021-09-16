# Introducing QoS

## Converged Networks

### Characteristics
* Competition between constant small packet voice flows, bursty video, and data flow
* time-sensitive voice and video flows
* critical traffic that must get priority

### Quality issues

* Lack of bandwidth
    * QoS can't really help
* End to end delay
* Jitter
* Packet Loss


## QoS Policy

3 categories
* Video
* Voice
* Data

### Flow
1. Identify traffic and requirements
2. Divide traffic into classes
3. Define QoS policies for each class

#### Group Traffic into QoS classes

IE:
* Voice
* Mission-Critical
* Transaction and Interactive
* Best Effort
* Scavenger
    * No real capacity
    * Very sporadic
    * Not very critical
    * IE: torrents or internet radio

Example:

Class | Bandwidth | Priority | Congestion Management Examples  
---|---|---|---  
Voice | Minimum 1 Megabit per second (Mbps) | Level 5 | Low Latency Queuing (LLQ)  
Mission-critical and transactional | Minimum 1 Mbps | Level 4 | Class-based Weighted Fair Queuing (CBWFQ)  
Best-effort | Maximum 500 kilobits per second (kbps) | Level 2 | CBWFQ  
Scavenger | Maximum 100 kbps | Level 0 | CBWFQ and Weighted Random Early Detection (WRED)

## QoS Mechanisms

* Classification 
* Marking 
* Policing and shaping 
* Congestion management 
* Congestion avoidance 
* Link efficiency 

## Classification and Marking

### Classification

Just groups

### Marking

Just label set in the header, sometimes called `coloring` according to books.

### Policing and Shaping

Policers drop traffic, shapers hold it for a bit and then pass it.

#### Usage
* Policiers
    * ingress tools
    * placed at egress
    * when traffic is exceeded
    * Significantly TCP resend can occur
* Shaping
    * Fewer TCP resends
    * Introduces jitter and delay

### Queuing and Scheduling
Queuing sets up order of packets of each type/stream

Scheduling is just how it orders sending the packets out.

`priority` keyword in QoS config says give it a high scheduling priority

`WRED`:
> Cisco specific traffic dropping, "randomly" drops traffic based on weights.

### Link Efficiency Mechanisms

Increase bandwidth, use compression.

## QoS Models

* Best Effort
* IntServ
* DiffServ

### Best Effort

### Differentiated Serviced (DiffServ)

#### Characteristics
* Similar to package delivery service
* Network traffic is identified by class
* Network QoS policy applied on classes
* Choose level of service for each traffic class.

#### Per Hop

* Default
    * used for best effort
* Expedited Forwarding (EF)
    * low delay service
* Assured Forwarding (AF)
    * guaranteed bandwidth
* Class selector
    * Backwards compatibility

## Deploying QoS

A lot of time spent monitoring, analyzing, and implementing policies over the entire life of the network.

`Control Plane`: CPU

`Data Plane`: Hardware

## Exam notes

Not really on the exam much, but when it is it's always just random memorization stuff.