# StreamingDataProcessing

Here, This repo will summarize the streaming book, called Streaming Systems The What, Where, When, and How of Large-Scale Data Processing
by Tyler Akidau, Slava Chernyak, and Reuven Lax.

The book provides examples with implementation codes written in Java language. Hence, in this repo, Python version of code will be implemented.

# Chapter 1
Streaming 101, which covers the basics of stream processing, establishing some terminology, discussing the capabilities of streaming systems, distinguishing between two important domains of time (processing time and event time), andfinally looking at some common data processing patterns.

## Streaming 

Streaming data processing is a big deal in big data these days, and for goodreasons; among them are the following:
* Businesses crave ever-more timely insights into their data, and switching to streaming is a good way to achieve lower latency
* The massive, unbounded datasets that are increasingly common inmodern business are more easily tamed using a system designed forsuch never-ending volumes of data.
* Processing data as they arrive spreads workloads out more evenlyover time, yielding more consistent and predictable consumption ofresources.

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
**  At the core, correctness boils down toconsistent storage. Streaming systems need a method for checkpointingpersistent state over time
* Tools for reasoning time
** This gets you beyond batch. Good tools for reasoning about time areessential for dealing with unbounded, unordered data of varying event-time skew. An increasing number of modern datasets exhibit thesecharacteristics, and existing batch systems

There’s no reason the types of clever insights that make batch
systems the efficiency heavyweights they are today couldn’t beincorporated into a system designed for unbounded data, providing usersflexible choice between what we typically consider to be high-latency,higher-efficiency “batch” processing and low-latency, lower-efficiency“streaming” processing

### Event time vs Processing time
Event time is event is actually occured.
Processing time is the time events observed in system.

In reality, processing-time lag and event-time skew at any given point in timeare identical; they’re just two ways of looking at the same thing. Theimportant takeaway regarding lag/skew is this: Because the overall mappingbetween event time and processing time is not static.

## Data Processing Patterns
We look at both types of processing and,where relevant, within the context of the two main types of engines we careabout (batch and streaming, where in this context, I’m essentially lumpingmicrobatch in with streaming because the differences between the two aren’tterribly important at this level).

### Bounded Data
Bounded DataProcessing bounded data is conceptually quite straightforward, and likelyfamiliar to everyone. In Figure 1-2, we start out on the left with a dataset fullof entropy. We run it through some data processing engine (typically batch,though a well-designed streaming engine would work just as well), such asMapReduce, and on the right side end up with a new structured dataset withgreater inherent value.

### Unbounded Data: Batch
Batch engines, though not explicitly designed with unbounded data in mind,have nevertheless been used to process unbounded datasets since batchsystems were first conceived. As you might expect, such approaches revolve
around slicing up the unbounded data into a collection of bounded datasetsappropriate for batch processing.

#### Fixed window
around slicing up the unbounded data into a collection of bounded datasetsappropriate for batch processing.Fixed windowsThe most common way to process an unbounded dataset using repeated runsof a batch engine is by windowing the input data into fixed-size windows andthen processing each of those windows as a separate, bounded data source(sometimes also called tumbling windows),

#### Sessions
This approach breaks down even more when you try to use a batch engine toprocess unbounded data into more sophisticated windowing strategies, likesessions. Sessions are typically defined as periods of activity (e.g., for aspecific user) terminated by a gap of inactivity. When calculating sessionsusing a typical batch engine, you often end up with sessions that are splitacross batches, as indicated by the red marks in Figure 1-4. We can reducethe number of splits by increasing batch sizes, but at the cost of increasedlatency.

### Unbounded Data: Streaming
Contrary to the ad hoc nature of most batch-based unbounded data processingapproaches, streaming systems are built for unbounded data. As we talkedabout earlier, for many real-world, distributed input sources, you not onlyfind yourself dealing with unbounded data, but also data such as thefollowing:
* Highly unordered with respect to event times, meaning that you needsome sort of time-based shuffle in your pipeline if you want toanalyze the data in the context in which they occurred.
* Of varying event-time skew, meaning that you can’t just assumeyou’ll always see most of the data for a given event time X withinsome constant epsilon of time Y

#### Time-agnostic
Time-agnostic processing is used for cases in which time is essentiallyirrelevant; that is, all relevant logic is data driven. Because everything aboutsuch use cases is dictated by the arrival of more data, there’s really nothingspecial a streaming engine has to support other than basic data delivery. As aresult, essentially all streaming systems in existence support time-agnosticuse cases out of the box (modulo system-to-system variances in consistencyguarantees, of course, if you care about correctness). Batch systems are alsowell suited for time-agnostic processing of unbounded data sources by simplychopping the unbounded source into an arbitrary sequence of boundeddatasets and processing those datasets independently

#### Filtering
A very basic form of time-agnostic processing is filtering, an example ofwhich is rendered in Figure 1-5. Imagine that you’re processing web trafficlogs and you want to filter out all traffic that didn’t originate from a specificdomain. You would look at each record as it arrived, see if it belonged to thedomain of interest, and drop it if not. 

#### Inner joins
Another time-agnostic example is an inner join, diagrammed in Figure 1-6.When joining two unbounded data sources, if you care only about the resultsof a join when an element from both sources arrive, there’s no temporalelement to the logic. Upon seeing a value from one source, you can simplybuffer it up in persistent state; only after the second value from the othersource arrives do you need to emit the joined record. (In truth, you’d likelywant some sort of garbage collection policy for unemitted partial joins, whichwould likely be time based. 

#### Approximation algorithms
The second major category of approaches is approximation algorithms, suchas approximate Top-N, streaming k-means, and so on. They take anunbounded source of input and provide output data that, if you squint at them,look more or less like what you were hoping to get, as in Figure 1-7. Theupside of approximation algorithms is that, by design, they are low overheadand designed for unbounded data. The downsides are that a limited set ofthem exist, the algorithms themselves are often complicated.

#### Windowing
The remaining two approaches for unbounded data processing are bothvariations of windowing. Before diving into the differences between them, Ishould make it clear exactly what I mean by windowing, insomuch as wetouched on it only briefly in the previous section. Windowing is simply thenotion of taking a data source (either unbounded or bounded), and choppingit up along temporal boundaries into finite chunks for processing. Figure 1-8shows three different windowing patterns.

#### Fixed windows(aka tumbling windows)
The remaining two approaches for unbounded data processing are bothvariations of windowing. Before diving into the differences between them, Ishould make it clear exactly what I mean by windowing, insomuch as wetouched on it only briefly in the previous section. Windowing is simply thenotion of taking a data source (either unbounded or bounded), and choppingit up along temporal boundaries into finite chunks for processing. Figure 1-8shows three different windowing patterns.Figure 1-8. Windowing strategies. Each example is shown for three different keys,highlighting the difference between aligned windows (which apply across all the data) andunaligned windows (which apply across a subset of the data).Let’s take a closer look at each strategy:Fixed windows (aka tumbling windows)We discussed fixed windows earlier. Fixed windows slice time intosegments with a fixed-size temporal length. Typically (as shown inFigure 1-9), the segments for fixed windows are applied uniformly acrossthe entire dataset, which is an example of aligned windows. 

#### Sliding windows (aka hopping windows)
A generalization of fixed windows, sliding windows are defined by afixed length and a fixed period. If the period is less than the length, thewindows overlap. If the period equals the length, you have fixedwindows. 

#### Sessions
An example of dynamic windows, sessions are composed of sequences ofevents terminated by a gap of inactivity greater than some timeout.Sessions are commonly used for analyzing user behavior over time, bygrouping together a series of temporally related events (e.g., a sequenceof videos viewed in one sitting). Sessions are interesting because theirlengths cannot be defined a priori; they are dependent upon the actualdata involved

#### Windowing by processing time
When windowing by processing time, the system essentially buffers upincoming data into windows until some amount of processing time haspassed. For example, in the case of five-minute fixed windows, the systemwould buffer data for five minutes of processing time, after which it wouldtreat all of the data it had observed in those five minutes as a window andsend them downstream for processing.

Properties:
* It’s simple. The implementation is extremely straightforwardbecause you never worry about shuffling data within time. You justbuffer things as they arrive and send them downstream when thewindow closes.
* There is no need to be able to dealwith “late” data in any way when windowing by processing time
* If you’re wanting to infer information about the source as it isobserved, processing-time windowing is exactly what you want.
Good points aside, there is one very big downside to processing-timewindowing: if the data in question have event times associated with them,those data must arrive in event-time order if the processing-time windows areto reflect the reality of when those events actually happened.

#### Windowing by event time


