# StreamingDataProcessing

Here, This repo will summarize the streaming book, called Streaming Systems The What, Where, When, and How of Large-Scale Data Processing
by Tyler Akidau, Slava Chernyak, and Reuven Lax.

The book provides examples with implementation codes written in Java language. Hence, in this repo, Python version of code will be implemented.

## Streaming 

Streaming system is the engine designed with infinite dataset. However, the dataset shape differs two categories.

* Bounded data - A type of dataset that is finite in size
* Unbounded data - A type of dataset that is infinite size

These are called cardinality. Constitution of dataset, its physical manifestation. The way interact with data.
2 primary constitution.

#### Table
A holistic view of dataset at a specific point a time, SQL systems deal with.
#### Stream
An element by element view over time. Map reduce lineage deal with it traditionally.

### Limitation on streaming
Streaming system histrocally provide low latency, inaccurate or more capable batch system provides correct eventually. In other words **Lambda architecture**. 
Ideally, Lambda architecture is that you run a streaming system and batch system, both perform same calculation.
But you need build, maintain 2 independent pipeline and then shomehow merge results from 2 the pipelines.
Hnce lambda architecture is unsavior. New architecture has arose which is Kappa architecture. The issue of repeatability to address using a replayable system like kafka.
* Correctness
* Tools for reasoning time

### Event time vs Processing time
Event time is event is actually occured.
Processing time is the time events observed in system.

