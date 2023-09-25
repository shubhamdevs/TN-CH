# Zero-Knowledge Proofs

<center>Introduction to Cryptography</center>

## 3-colorable Graphs

We will show how you can construct a zero-knowledge proof for Graph 3- Coloring, using a security
assumption. Since Graph 3-Coloring is NP-complete, this will allow us to produce zero-knowledge
proofs for all NP problems.

**Definition** A graph G is 3-colorable if the vertices of a given graph can be colored with only three
colors, such that no two vertices of the same color are connected by an edge.

<!-- Image need to fix as per hosting platform-->

![A 3-coloring of a graph](https://drive.google.com/file/d/1cjadV27phekEU5NsrGreYqYKJpuCQ2Kq/view?usp=sharing)

In other words given a graph we denote each vertices as $v_i$ and $v_j$ where $i,j$ < $n$. If there exists an edge between $v_i$ and $v_j$ we will denote that edge as $e_{i,j}$ . We are supposed to color the graph of all vertices with only three colors $(R, G, B)$ such that no edge should have two vertices of the same color. Below are two common facts about 3-colorable graphs.

- **Fact 1:** If we are given a 3-coloring, permuting the 3 colors $(R, G, B)$ still gives rise to a valid 3-coloring. Ie: Coloring all red vertices blue and coloring all blue vertices red gives a valid 3-coloring.

- **Fact 2:** If the graph is not 3-colorable, then at least one edge has matching colors.

## Zero-Knowledge Proof for 3-coloring Graphs

Let $G$ be graphs on $n$ vertices and define $V = \{v1, \ldots, vn\}$ be the set of vertices,
$E = \{e_{i,j} : \exists \text{ edge between } v_i, v_j\}$. On input the graph $G$ is known to both parties. The prover is given a private input in the protocol that is the witness which is a 3-coloring of the graph $G$. The protocol proceeds as follows.

- **Prover:** Given $w$ a 3-coloring of the graph $G$. Randomly permute the 3-colors to obtain a
new coloring. Utilize a commitment scheme to commit the color of all vertices.

$$
\forall i \in[n], c_{i}=\operatorname{COM}\left(v_{i}, \text { color of } v_{i}\right)
$$

- **Verifier:** Pick edge $e_{i,j} \in E$ and send $e_{i,j}$ to the Prover.

- **Prover:** Open $c_i$ and $c_j$.

- **Verifier:** Return Accept if $ c_i \neq c_j $. Reject otherwise.

An explanation of this protocol is provided. At the first step the Prover will randomly permute
the 3-colors to obtain a new coloring. This does not modify the validity of $w$. We use a committment scheme to hide the coloring of each vertex as a string. Every string is essentially hidden and binded.

The Verifier is allowed to select one edge. The Prover opens the committment $c_i$ and $c_j$ and hence the Verifier learns the colors of vertices $ v_i $ and $ v_j $. Finally the Verifier checks if the two colors $c_i$ and $c_j$ are different. If so, accept. Else reject. Now lets try to prove this protocol is a zero-knowledge protocol of 3-coloring graphs.

**Theorem 1** *The above protocol satisfies completeness, soundness with $\frac{1}{|E|}$, and zero-knowledge*

### Proof.

**Completeness:** If witness w provides a valid 3-coloring of the graph $G$. Then the Prover can commit to the colors such that regardless of what edge the Verifier chooses the Verifier will see that $ c_i \neq c_j $ and will return accept.

**Soundness:** We need to show that if w provides an invalid 3-coloring of $G$. Then the

$$
\operatorname{Prob}[\text { Verifier returns accept }] \leq \operatorname{negl}(n)
$$

Since $w$ is an invalide 3-coloring of $G$, then there exists edge $e_{i,j}$ such that $c_i = c_j$. Thus

$$
\operatorname{Prob}[\text { Verifier returns reject }] \geq \operatorname{Prob}\left[\text { Verifier picks } e_{i, j}\right]=\frac{1}{|E|}
$$

Once we have protocol with soundness of $\frac{1}{|E|}$ we can just repeat the protocol sequentially to improve the soundness.

By sequential repetition if we repeat the protocol $k$ times and $k \gg E$ then

$$
\operatorname{Prob}[\text { Verifier returns accept }] \leq(1-1 / E)^{k} \leq \operatorname{negl}(n)
$$

**Zero-Knowledge:** To prove Zero-Knowledge we construct a simulator $S$ which has the code
of $V^∗$, the Verifier, and works as follows.

- **Step1:** Choose random $e_{i, j}^{\prime}=\left(v_i^{\prime}, v_j^{\prime}\right)$ and commit to 2 different random colors for $c_i^{\prime}, c_j^{\prime}$. For all other vertices, $v_{k}$ where $k \neq i, j$. Let $c_{k}=0$, the zero string.
- **Step 2:** Send first message to $V^{*}$ and get $e_{i, j}$ from $V^{*}$.
- **Step 3:** If $e_{i, j}=e_{i, j}^{\prime}$ open $c_{i}^{\prime}, c_{j}^{\prime}$. Else go to Step 1.

Now we need to prove the transcript of simulator $\mathrm{S}$ is indistinguishable from the transcript of the real world protocol. Intuitively the only difference is that in the real protocol, all commitments to all the vertices are nicely done. They all are colored whereas in S, most of the vertices have a commitment of a zero string. Next thing to note is that all these other commitments will never be opened. So by the hiding property, all those zero string commitments look identical to the commitments of the vertices in the real protocol.

We will use the hybrid lemma to show that these two transcripts are indistinguishable. Let $H_{0}$ be the description of the protocol, $H_{3}$ be the description of simulator $\mathrm{S}$. 

- $H_{0}$ Algorithm $S_{0}$ has the correct witness $w$ and code of $V^{*} . S_{0}$ acts as an honest Prover and interacts with $V^{*}$ which means:

    - **Step 1:** Commit colors of vertices and compute first message honestly with witness $w$.
    - **Step 2:** Get $e_{i, j}$
    - **Step 3:** Open $c_{i}, c_{j}$ and if $c_{i} \neq c_{j}$ return accept, else return reject.

Output the transcript $\tau_{0} . \tau_{0}$ has the same distribution as in the real protocol.

- $H_{1}$ Algorithm $S_{1}$ has the correct witness $w$ and code of $V^{*} . S_{1}$ guesses a random edge $e_{i, j}^{\prime}$. $S_{1}$ acts as an honest Prover and interacts with $V^{*}$ which means:

    - **Step 1:** Commit colors of vertices and compute first message honestly with witness $w$.
    - **Step 2:** Get $e_{i, j}$
    - **Step 3:** Now if $e_{i, j} \neq e_{i, j}^{\prime}$ go to Step 1. Else open $c_{i}, c_{j}$ and if $c_{i} \neq c_{j}$ return accept, else return reject.

Output the transcript $\tau_{1}$.

- $H_{2}$ Algorithm $S_{2}$ has the correct witness $w$ and code of $V^{*} . S_{2}$ guesses a random edge $e_{i, j}^{\prime}$. $S_{2}$ computes the first message using $e_{i, j}^{\prime}$ which means:
    - **Step 1:** $S_{2}$ commits the coloring of every vertices to be zero for all $c_{k}^{\prime}$ where $k \neq i, j . c_{i}^{\prime}$ and $c_{j}^{\prime}$ are still computed honestly using $w$.
    - **Step 2:** Get $e_{i, j}$
    - **Step 3:** Now if $e_{i, j} \neq e_{i, j}^{\prime}$ go to Step 1. Else open $c_{i}^{\prime}, c_{j}^{\prime}$ and if $c_{i}^{\prime} \neq c_{j}^{\prime}$ return accept, else return reject.

Output the transcript $\tau_{2}$.

- $H_{3}$ Algorithm $S$ is the simulator with code of $V^{*}$. $S$ guesses a random edge $e_{i, j}^{\prime}$.

    - **Step 1:** $S$ commits the coloring of every vertices to be zero for all $c_{k}^{\prime}$ where $k \neq i, j . c_{i}^{\prime}$ and $c_{j}^{\prime}$ are computed randomly.
    - **Step 2:** Get $e_{i, j}$
    - **Step 3:** Now if $e_{i, j} \neq e_{i, j}^{\prime}$ go to Step 1. Else open $c_{i}^{\prime}, c_{j}^{\prime}$ and if $c_{i}^{\prime} \neq c_{j}^{\prime}$ return accept, else return reject.

Output the transcript $\tau_{3}$.

$H_{0}$ is indistinguishable from $H_{1}$ as the only difference is that $H_{1}$ randomly chooses $e_{i, j}^{\prime}$ until $e_{i, j}$ is found. Thus the two transcripts have identical distribution.

$H_{1}$ is indistinguishable from $H_{2}$ from the following lemma and informal proof.

**Lemma 2** Distribution of $\tau_{1}=$ Distribution of $\tau_{2}$ 

The proof follows from the hiding of commitment scheme. The basic idea is that suppose somebody comes along that can distinguish between $\tau_{1}$ and $\tau_{2}$. Then they can create a distinguisher algorithm that breaks the hiding idea. As a result all commitments which are not opened can be seen externally by this algorithm and this algorithm would output $\tau_{1}$ or $\tau_{2}$, however this would contradict the commitment scheme.

$H_{2}$ is indistinguishable from $H_{3}$ since the only difference between $H_{2}$ and $H_{3}$ is that $H_{3}$ assigns a random coloring to $v_{i}$ and $v_{j}$ instead of utilizing the witness $w$. However since in the first step we permute the witnessed colors of the vertices, then the witness commits $c_{i}$ and $c_{j}$ randomly. Thus the distribution of $\tau_{2}=$ distribution of $\tau_{3}$.

## 3. Round Complexity and Efficiency

Let's talk about the round complexity, here we described a basic protocol with three steps but you have to repeat in sequentially many times to reach the soundness that we want. In this section we question how to achieve a zero-knowledge protocol with better round efficiency. What if we repeat the protocol in parallel, rather than sequential. In other words if we have many copies of this basic protocol, $\pi_{1}, \ldots \pi_{n}$ then every copy starts with a random permutation of the witness. In particular what makes this problem unique is that the actions of the Verifier in $\pi_{2}$ could depend on the first message of the Verifier in $\pi_{1}$. We ask if it would still remain zero-knowledge and which is difficult to prove.

## 4. Fiat-Shamir Transformation

Now we go back to the random algorithm model to construct a zero knowledge protocol. Let $\Sigma_{1}, \Sigma_{2}, \Sigma_{3}$ be the three messages sent between the Prover and the Verifier.

- **Step 1:** The message $\Sigma_{1}$ is sent to the Verifier.
- **Step 2:** The message $\Sigma_{2}$ is sent to the Prover based on the random picking of an edge which is viewed as a string. The edge selected would be $e_{i, j}=r \bmod |E|$. $\Sigma_{2}$ is computed as $\Sigma_{2}=H\left(\Sigma_{1}\right)$. Where $H($.$)$ is a public hash function.
- **Step 3:** Since $H($.$)$ is a public hash function, the computation can actually be done by the Prover. Thus the Prover sends $\Sigma_{1}, H\left(\Sigma_{1}\right), \Sigma_{3}$.


## 5. Blockchains and Zero Knowledge Proofs

In this section we look at the utilization of Zero Knowledge protocols in Zero Coin and ZCash. Zero coin is the precursor of Zcash. There is a bulletin board which is part of the public ledger and at any given time, this bulletin board has a bunch of coins that are publicly posted.

$$
\text { Coins }=\left[\begin{array}{ccc}
c_{1}^{1} & \ldots & c_{n}^{1} \\
\vdots & \ldots & \vdots \\
c_{1}^{m} & \ldots & c_{n}^{m}
\end{array}\right]
$$

Then we have a list of coins which have already been spent. This spent list is smaller than the bulletin.

$$
\text { Spent }=\left\{s_{1}, \ldots, s_{k}\right\}
$$

- **Minting** If you want to convert 1 bitcoin to 1 ZeroCoin, the process works as shown below.

    - **Step 1:** The User computes $c=C O M_{r}(s)$ which is a commitment scheme on the serial number of a bitcoin to be minted. The User sends this $c$ to the miner. Note we ignore the transaction fee.
    - **Step 2:** The Miner burns this $c$ value and will put the $c$ value onto the bulletin board, coin.
    - **Step 3:** If user $A$ wishes to spend this coin, and send the coin to $B, A$ just sends $(s, r)$ to $B$.

- **Coin Collection** If $B$ wishes to collect a coin $c$, the following steps occur.

    - **Step 1:** $B$ creates a new coin $c^{\prime}=C O M_{r^{\prime}}\left(s^{\prime}\right)$
    - **Step 2:** $B$ sends $\left(s, c^{\prime}\right)$ and $\pi$, a Zero-Knowledge Protocol with a statement defined as $s$ is an opening of one of the commitments in the bulletin board Coin. Note that this is an NP statement.
    - **Step 3:** The Miner runs the following check. It checks if $\pi$ is a valid Zero-Knowledge Protocol. It then checks if $s$ is not already on the spent list. If so, $s$ is put on the spent list and $c^{\prime}$ is put on the coin list.

Note that this Minting and Coin Collection is crucial as the anonymity of each transaction is hidden. For example in BitCoin, if $A$ sends a coin to $B$ followed by a second coin, the anonymity of $A$ 's transaction is lost since the coin itself is being directly transferred. On the other hand with ZeroCoin, $A$ does not send a coin to $B$ but instead simply sends $(s, r)$. Thus although $c$ is publicly announced on Coin, it is impossible to track which coin in Coin is retrieved by $B$.

