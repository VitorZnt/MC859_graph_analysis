## Visualized properties

In the graph, the following properties were created:

incomingDegree
pagerank
pagerankOrder
btwnWeight


The incoming degree does not consider weight. This metric was the base to the Freeman centrality calculation.

The pagerank algorithm considers weight, and was calculated with 30 iterations and a damping factor of 0.85. A diverse range of iterations were experimented (from 10 up to 1000), but anything above 30 had little to no impact on the node scores.

The weighted betweenness centrality was calculated with multiple Djikstra iterations. It used all nodes as samples, thus giving an exact answer. See more details at Neo4J's GDS documentation.
