# Description of our experiments

## Allocated resources

Prefix: `45.132.188.0/22`
ASN: `3970`
Peer AS: `3130`

## Traffic Engineering with BGP
Traffic Engineering (TE) is the set of actions taken by network operators to get a better performance out of their network infrastructure. In our experiment we focus on TE with BGP. We perform incomming TE.

There are two common ways to control inbound traffic:
- one way is using selective advertisements and
- the other is using AS-path prepending

### Selective Advertisements
Selective Advertisement is done by announcing a subset of the prefixes to one BGP neighbour.
This way traffic for these prefixes uses the desired neighbour.
Most of the time, a super-prefix is announced to all neighbours. This way, if the desired path is down, the detinations are still reachable. Note that the route of the targeted prefix will be used because of the fact that the route is associated with a more specific prefix.
In the following example P1 is a sub-prefix of P2
![selective advertisement](images/te_examples.drawio.png)

### AS Prepending
AS prepend uses the fact that AS-path length is an important criteria for path selection. An AS will add it's AS number multiple times to a path to make it less favorable.

## Experiments
Our AS has one provider (AS 3130) which is connected to NTT (AS 2914), Sprint (AS 1239) and the Seattle Internet Exchange.

### EXP 1
Our first experiment will test the effectiveness of selective advertisement as a TE technique. Prefix 45.132.188.0/24 will be the one targeted.
We will try to steer traffic to this prefix. Because of the fact that our AS is not
connected directly to either provider (NTT and Sprint) we cannot simply choose
to announce to one but not the other. To prevent some routes to be accepted in one of AS3130's upstreams, we will poison the AS.
For example if we want to choose the route going through 2914 but not 1239, We
should announce our prefix with the following AS Path 3970 1239 3970. This way
we will use 2914.

For this experiment we will alternate the advertisment between the two providers every 6 hours.
We will monitor BGP updates received for our prefixes from multiple vantage points. We will also perform regular traceroute to our prefix to check stability.
Note that prefix 45.132.188.0/22 will be announced without poisoning to act as a safe route in case anything happens to the currently chosen route.

We aim to see that the paths chosen by the different vantage points is the one we choose and that the convergence is fairly quick.

### EXP 2
This experiment will test AS-path prepending as a valid TE technique to prioritize a route over other.
Similar to our first experiment we prepend toward AS 2914 or AS 1239. Poisoning is replaced by prepending such that the other route is more likely to be used. For this experiment we will use the following communities: 2914:413	2914:423 and 65003:0

We wish to see that most of the routes to our prefix will follow the desired route.

## Setup summary

In this experiment we will annonce *a subset* of the prefixes below:
- `45.132.188.0/22`
- `45.132.188.0/23`
- `45.132.190.0/23`
- `45.132.188.0/24`
- `45.132.189.0/24`
- `45.132.190.0/24`
- `45.132.191.0/24`

The following poisoning may be observed:
- 3970 1239 3970
- 3970 2914 3970

Prepending may be observed for these prefixes.

In addition we perform traceroutes from remote locations to IP addresses in these prefixes.

## Contacts
For any questions or problems regarding our experimentation please contact us.
* Omar Darwich: omar.darwich@laas.fr
* Cristel Pelsser: cristel.pelsser@uclouvain.be
* kevin Vermeulen: kevinmylinh.vermeulen@gmail.com
* Randy Bush: randy@psg.com
