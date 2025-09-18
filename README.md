
## Classification of map‐matching algorithms (Huang et al., 2021)

### a) Based on sampling frequency 


| Classification                        | High‐frequency sampling data‐based algorithm                           | Low‐frequency sampling data‐based algorithm                                |
|---------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Sampling frequency**                | 1–10 seconds                                                           | More than 30 seconds (ultra‐low: >2 minutes)                                |
| **Features of data sets**             | Dense trajectory points, small change of heading angle and smooth curve | Trajectory points sparsity and large curve fluctuation                      |
| **Advantage**                         | Dense points reduce computation by narrowing candidate segments. Small heading angle changes help detect junctions. Curve smoothing preserves trajectory integrity, lowering difficulty. | Probability/statistics methods perform better with sparse trajectories.     |


### b) Based on data information

| Algorithm type          | Key principle / methods                                                                 | Typical scope         |
|-------------------------|------------------------------------------------------------------------------------------|-----------------------|
| **Geometric principle** | Distance / shape similarity (point–line, curve–curve). Least squares, search regions (ellipse), Fréchet distance. | Incremental (point-by-point) |
| **Topology**            | Road network graph (nodes & segments). Uses connectivity, loops, and rules for matching; cooperative localization possible. | Incremental |
| **Probability statistics** | Maximum-probability path. Bayesian models, search trees, correlation, fuzzy inference (speed, angle, error). | Global (whole trajectory) |
| **Advanced models**     | AI & optimization: fuzzy logic, D–S theory, SVM, neural nets, reinforcement learning, genetic algorithms, ant colony. | Global |


## Evaluation
| Metric   | Formula                                       | Meaning / Usage                                                                |
|----------|-----------------------------------------------|--------------------------------------------------------------------------------|
| CMP      | $CMP = \frac{Num_c}{Num_{all}} \times 100\%$  | % of trajectory points matched correctly. Good for point-level evaluation.     |
| TE       | $TE = T_o - T_s$                              | Measures computational cost                                                    |
| Recall   | $Recall = \frac{Num_c}{Num_c + Num_l} \times 100\%$ | Proportion of correct matches out of all matches attempted .  Good for point-level evaluation.                 |
| LenCMP   | $LenCMP = \frac{Len_c}{Len_{all}} \times 100\%$ | Ratio of correctly matched path length to actual driven path length. Good for path-based evaluation. |
| Circuity | $Circuity = \frac{Len_e}{Len_c + Len_e} \times 100\%$ | Measures detours: mismatched path length vs total matched+mismatched length.  ood for path-based evaluation.  |



## Berjisian et al., 2022-Evaluation of map-matching algorithms for smartphone-based active travel data



### Comparison of Six Map-Matching Algorithms for Active Travel Data

| Algorithm                    | Category   | Original Purpose | Implementation | Open-source? | Key Features | Strengths | Weaknesses |
|------------------------------|------------|------------------|----------------|--------------|--------------|-----------|------------|
| **Schuessler & Axhausen**    | GIS-based (Geometric principle + Topology) | Car trips (Multiple Hypothesis Technique) | R | No | Considers geometry, topology, and length similarity; evaluates multiple candidate routes | Good accuracy when routes found | High failure rate (~29% no solution); very long runtime (~8.1 s/record) |
| **Dalumpines & Scott**       | GIS-based  | Transport research (shortest path in buffer) | ArcGIS + R | No | Shortest path within buffer around GPS trajectory | Lowest average distance error; efficient (~0.06 s/record) | High failure rate (~38% no solution); struggles with loop trips |
| **Li**                       | GIS-based  | Cycling route choice (Toronto) | ArcGIS + R | No | Assigns GPS points by proximity; voting among neighboring points | Works in dense urban networks; simple | Conservative (misses links), discontinuous routes; ~0.24 s/record |
| **Schweizer et al.**         | GIS-based  | Cycling trips (Bologna) | ArcGIS + R | No | Shortest path weighted by cycling facilities | Highlights bike infrastructure | Over-emphasizes facilities; produces erroneous long routes; runtime ~4.85 s/record |
| **Millard-Ball et al. (PgMapMatch)** | Advanced (probabilistic/statistical) | Urban GPS (poor quality data) | Python | Yes | Combines geometric + topological + temporal info; logistic regression reliability score | Best overall performer: high accuracy (F ≈ 0.82); routes for all trips; moderate runtime (~0.43 s/record) | Sensitive to wrong-way travel on one-way streets |
| **Perrine et al.**           | Advanced (shortest path projection) | Multimodal networks (transit, probe vehicles) | Python | Yes | Projects GPS to links and connects via shortest paths | High reported accuracy in original paper; simple | Very poor accuracy on active travel data (over-matching, false links); ~0.71 s/record |




# Probability statistics methods: 

## Xiong et al., 2021 – Map-Matching Using Hidden Markov Model and Path Choice Preferences under Sparse Trajectory


Tackles the challenge of map-matching with low-frequency and noisy GPS trajectories, where multiple possible routes exist between sparse points. 

**For Car & Low-Frequency**

**Baseline**: HMM map-matching algorithm (Newson & Krumm, 2009).


**Enhancement**: Change the transition probability so it doesn’t only check for shortest paths, but also accounts for drivers’ real preferences

Original transition probability:
$$
P(s_t \mid s_{t-1}) \propto 
\exp \!\left( \theta_1 \cdot \text{Length} 
+ \theta_2 \cdot \text{TravelTime} 
+ \theta_3 \cdot \text{RoadClassPreference} 
+ \theta_4 \cdot \text{ClassChangePenalty} \right)
$$

New Proposed:
$$
P(s_t \mid s_{t-1}) \propto 
\exp \!\left( \theta_1 \cdot \text{Length} 
+ \theta_2 \cdot \text{TravelTime} 
+ \theta_3 \cdot \text{RoadClassPreference} 
+ \theta_4 \cdot \text{ClassChangePenalty} \right)
$$



**Limitation:**

1) Computational efficiency is not optimal; More time than standard HMM approaches.

2) The assumption of uniform driver preferences (all drivers treated identically) may not capture individual variability.

3) Accuracy gains (~1% improvement at >30s sampling intervals) are modest, especially in sparse data scenarios.




**Future research directions:**

1) Explore more efficient path inference strategies to reduce time consumption.

2) Integrate richer behavioral factors (beyond shortest path and simple preferences) to better capture real-world decisions.

3) Adapt and validate the approach in different cities.

4) Personalize route choice models rather than assuming identical preferences.

---

# Non-Probability statistics methods: 

## Gao et al., 2021-Map-matching for cycling travel data in urban area

Addresses the problem of errors in GPS cycling data, especially in dense urban areas where tall buildings and complex networks 
reduce accuracy. Existing map-matching methods, often designed for cars, perform poorly for bicycles due to device errors, ambiguous road
networks, and inaccuracies in OpenStreetMap.

**Baseline**: Classical GIS-based map-matching framework (Reenfeld, 2002 & Quddus et al. 2003 GIS-based framework. 

**Enhancement**: 

1) A refined classification of roads accessible to cyclists (main streets and secondary roads with cycling facilities or reasonable safety.Highways, motorways, and limited-access roads not usable by cyclists.
2) Built a multi-factor scoring system that evaluates each candidate segment/path instead of relying only on geometric distance


**Limitation:** 

The method struggles with evaluating road availability at circular intersections, which can be misclassified as loops.
![img.png](img.png)
Ground truth data for bicycles is often unavailable, making it difficult to fully validate performance.

The method is designed with Rotterdam as the case study, so infrastructure and usage patterns in other cities may require adaptation.



**Future research directions:**

Apply machine learning techniques (with enough network samples) to improve identification of roads with low cyclist availability.

Develop new evaluation metrics tailored to datasets without ground truth, since many real-life cycling datasets lack labeled trajectories.

Incorporate additional road properties (e.g., slope/grade, pedestrian interference) into the cost functions.

Adapt the method to different cities by adjusting road classification and cost functions to local cycling infrastructure and behaviors

---





### General Hidden Markov Model (HMM)

An HMM is a tuple:

$\lambda = (S, O, A, B, \pi)$

- **States**:  
  $S = \{ s_1, s_2, \dots, s_N \}$  
  The set of hidden states.


- **Observations**:  
  $O = (o_1, o_2, \dots, o_T)$  
  The sequence of observations.


- **Transition probabilities**:  
  $A = [a_{ij}], \quad a_{ij} = P(s_j \mid s_i), \quad \sum_j a_{ij} = 1$  
  Probability of moving from state $s_i$ to state $s_j$.


- **Emission probabilities**:  
  $B = \{ b_j(o) \}, \quad b_j(o_t) = P(o_t \mid s_j)$  
  Probability of observing $o_t$ given hidden state $s_j$.


- **Initial distribution**:  
  $\pi = (\pi_1, \dots, \pi_N)$  
  Where $\pi_i = P(s_i \text{ at } t=1)$.


### Three classical HMM problems

1. **Evaluation**: compute probability of an observation sequence  

   $$
   P(O \mid \lambda) = \sum_{S} \pi_{s_1} \, b_{s_1}(o_1) \prod_{t=2}^{T} a_{s_{t-1}, s_t} \, b_{s_t}(o_t)
   $$  

   (solved efficiently by the *forward algorithm*).

---

2. **Decoding**: find the most likely hidden state sequence  

   $$
   \hat{S} = \arg\max_{S} P(S \mid O, \lambda)
   $$  

   (solved by the *Viterbi algorithm*).

---

3. **Learning**: estimate parameters $(A, B, \pi)$ from data  

   (solved by the *Baum–Welch / EM algorithm*).





### HMM for Map Matching

We map HMM components to the road/GPS problem:

- **States (hidden)**: road segments  
  $$
  S = \{ r_1, r_2, \dots, r_{N_r} \}
  $$

- **Observations**: GPS points  
  $$
  O = (z_1, z_2, \dots, z_T), \quad z_t \in \mathbb{R}^2
  $$

- **Emission probabilities**: likelihood of GPS point given road  
  $$
  P(z_t \mid r_i) = \frac{1}{2\pi\sigma_z^2} \exp\!\left(-\frac{\| z_t - x_{t,i} \|^2}{2\sigma_z^2}\right)
  $$
  where $x_{t,i}$ is the closest point on road $r_i$ to GPS point $z_t$.

- **Transition probabilities**: likelihood of moving between road segments  
  $$
  P(r_j \mid r_i) \propto \exp\!\left(-\frac{\big| d_{\text{route}}(x_{t,i}, x_{t+1,j}) - d_{\text{gc}}(z_t, z_{t+1}) \big|}{\beta}\right)
  $$
  - $d_{\text{route}}(\cdot)$ = shortest-path distance along road network (topology).  
  - $d_{\text{gc}}(\cdot)$ = great-circle distance between GPS points.  
  - If roads $r_i$ and $r_j$ are not connected → $P(r_j \mid r_i) = 0$.

- **Initial distribution**:  
  $$
  \pi_i = P(r_i \mid z_1) \propto P(z_1 \mid r_i)
  $$

---

### Path Inference (Decoding)

The probability of a full path $R = (r_1, r_2, \dots, r_T)$ is:

$$
P(R \mid O) \propto \pi_{r_1} \, P(z_1 \mid r_1) \prod_{t=2}^{T} P(r_t \mid r_{t-1}) \, P(z_t \mid r_t)
$$

The best-matching road sequence is found by Viterbi:

$$
\hat{R} = \arg\max_{R} P(R \mid O, \lambda)
$$



### Probabilistic Graph-based (PG) Map Matching

- **Road network**:  
  $G = (V, E)$ where  
  $V$ = intersections (nodes),  
  $E$ = road segments (edges).  

- **Observations (GPS points)**:  
  $Z = (z_1, z_2, \dots, z_T), \quad z_t \in \mathbb{R}^2$  

- **Candidate road segments** for each GPS point:  
  $C_t \subseteq E$ (within search radius of $z_t$).  

- **Path hypothesis**:  
  $R = (r_1, r_2, \dots, r_T), \quad r_t \in C_t$  
  (a sequence of candidate road segments).

---

#### 1. Emission probability (GPS → road segment)

$$
P(z_t \mid r_t) = \frac{1}{2\pi\sigma^2} \exp\!\left(-\frac{\| z_t - x_{t,r_t} \|^2}{2\sigma^2}\right)
$$

where $x_{t,r_t}$ = closest point on segment $r_t$ to GPS point $z_t$,  
and $\sigma$ = GPS noise (standard deviation).  

---

#### 2. Transition probability (graph-based)

For two consecutive road candidates $r_t$ and $r_{t+1}$:

$$
P(r_{t+1} \mid r_t) \propto 
\exp\!\left(-\frac{\big| d_{\text{route}}(x_{t,r_t}, x_{t+1,r_{t+1}}) - d_{\text{gc}}(z_t, z_{t+1}) \big|}{\beta}\right)
$$

- $d_{\text{route}}(\cdot)$ = shortest-path distance in the road graph $G$  
- $d_{\text{gc}}(\cdot)$ = great-circle distance between GPS points  
- $\beta$ = scaling parameter  
- If no path in $G$ exists, then $P(r_{t+1} \mid r_t) = 0$  

---

#### 3. Path probability

For a full candidate path $R$:

$$
P(R \mid Z) \propto 
\prod_{t=1}^T P(z_t \mid r_t) \cdot 
\prod_{t=2}^T P(r_t \mid r_{t-1})
$$

---

#### 4. Path inference

Find the most likely road sequence:

$$
\hat{R} = \arg\max_{R \in \mathcal{P}(G,Z)} P(R \mid Z)
$$

where $\mathcal{P}(G,Z)$ = all feasible paths in $G$ connecting the candidate sets $(C_1, C_2, \dots, C_T)$.  

This optimization is usually solved with **shortest-path algorithms** (e.g., Dijkstra, A*)  
using **negative log-probabilities as edge weights**.  
















