# Description
This ongoing project contains three distinct graph models - Channel, User, and Chat networks - derived from the "Brazilian Social Media Anti-vaccine Information Disorder Dataset - Telegram (2020-2025)" (doi: 10.25824/redu/5JIVDT). The primary research goals, to which the present dataset is the basis of study, are to identify viral and popular misinformation topics, their spread through time, the relationship between channels and the existence of user bubbles. The original messages were grouped and structured through channels, users, and temporal message clusters (chats), forming three distinct graphs. The **Channel Graph (directed, weighted)** models interactions between the monitored channels via message forwarding; the **User Graph (undirected, weighted)** identifies relationships based on forwards and replies between users; and the **Chat Graph (directed, no weights)** represents temporal "sessions" of messages to track how specific topics spread across the network, with forwards and replies between chats showing how a certain idea propagates.

The data can be found at Mendeley Data repository. As the graphml graphs were exported from Neo4J, they all have by default orientation, but that is how Neo4J works. In order to analyze the User Graph as undirected, it is sufficient to run any Cypher query or processing disregarding the orientation.
To import them on Neo4J, paste them in the "import" folder and, with the APOC import permission, run 'CALL apoc.import.graphml("?_graph.graphml", {readLabels: true})', replacing ? with users, channels or chats.

# Data preprocessing
This dataset is based on the Brazilian Social Media Anti-vaccine Information Disorder Dataset - Telegram (doi: 10.25824/redu/5JIVDT) message jsonl database. The present data was generated using a Python 3.12 ipynb notebook utilizing DuckDB for database management and Pandas for grouping logic. The code and related information can be found at the Github project address attached to this webpage.

* Ingestion and Filtering: Approximately 3.9 million messages were ingested from JSONL format. The data was filtered to retain only text-content messages in Portuguese, English, and Spanish.

* ID Optimization: All string-based identifiers for channels and users were mapped to BIGINT integers to optimize graph processing performance.

Reply messages already contained reference to the original message. However, forwarding messages only referred to the original channels. To address this, the matching of forwarded messages to their original sources was performed through a join operation in DuckDB to trace the original message. A message was identified as an original source if it had an exact text content match with the forwarding message, and its channel corresponded to the one indicated on the forwarded message.

* Matching filter: Forwarded messages that had more than 92 identical matches in the dataset (via method above) were removed. Manual inspection revealed that all these high-frequency messages were channel rules or administrative links rather than talks.

* Chat sessions: Messages within individual channels were grouped into "chats" based on a temporal gap threshold of 30 minutes. Additionally, a hard limit of 3 hours was placed on any single session to prevent the formation of huge nodes.

* Edge Construction:
Channel Edges were built by joining forwarded messages back to their identified source channels within the dataset.
User Edges were constructed by linking authors who engaged in direct forward/reply interactions, with weights representing the total count of such exchanges.
Chat Edges were created by linking conversation sessions that engaged in direct forward/reply interactions

* Export: The resulting tables were exported to CSV and subsequently converted to GraphML format for graph analysis.

# License
This project and its data are licensed under CC BY-NC 4.0. To view a copy of this license, visit https://creativecommons.org/licenses/by-nc/4.0/
