# StreamingDataProcessing

Here, This repo will summarize the streaming book, called Streaming Systems The What, Where, When, and How of Large-Scale Data Processing
by Tyler Akidau, Slava Chernyak, and Reuven Lax.

The book provides examples with implementation codes written in Java language. Hence, in this repo, Python version of code will be implemented.

# Chapter 1
Streaming 101, which covers the basics of stream processing, establishing some terminology, discussing the capabilities of streaming systems, distinguishing between two important domains of time (processing time and event time), and finally looking at some common data processing patterns.

## Streaming

Streaming data processing is a big deal in big data these days, and for good reasons; among them are the following:
* Businesses crave ever-more timely insights into their data, and switching to streaming is a good way to achieve lower latency
* The massive, unbounded datasets that are increasingly common in modern business are more easily tamed using a system designed forsuch never-ending volumes of data.
* Processing data as they arrive spreads workloads out more evenly over time, yielding more consistent and predictable consumption of resources.

Streaming system is an engine designed with an infinite dataset. However, the dataset shape differs in two categories.

* Bounded data - A type of dataset that is finite in size
* Unbounded data - A type of dataset that is infinite size

These are called cardinality. Constitution of dataset, its physical manifestation. The way to interact with data.
2 primary constitution.

#### Table
A holistic view of a dataset at a specific point a time, SQL systems deal with.
#### Stream
An element by element view over time. Map reduce lineage deals with it traditionally.

### Limitation on streaming
Streaming systems historically provide low latency, inaccurate or more capable batch systems provide corrections eventually. In other words **Lambda architecture**.
Ideally, Lambda architecture is that you run a streaming system and batch system, both perform the same calculation.
But you need to build, maintain 2 independent pipelines and then somehow merge results from 2 of the pipelines.
Hence lambda architecture is the savior. New architecture has arisen which is Kappa architecture. The issue of repeatability to address using a replayable system like kafka.
* Correctness
**  At the core, correctness boils down to consistent storage. Streaming systems need a method for checkpointing persistent state over time
* Tools for reasoning time
** This gets you beyond batch. Good tools for reasoning about time are essential for dealing with unbounded, unordered data of varying event-time skew. An increasing number of modern datasets exhibit these characteristics, and existing batch systems

There’s no reason the types of clever insights that make batch
systems the efficiency heavyweights they are today couldn’t be incorporated into a system designed for unbounded data, providing users flexible choice between what we typically consider to be high-latency,higher-efficiency “batch” processing and low-latency, lower-efficiency“streaming” processing

### Event time vs Processing time
Event time is when an event actually occurred.
Processing time is the time events observed in a system.

In reality, processing-time lag and event-time skew at any given point in time are identical; they’re just two ways of looking at the same thing. The Important takeaway regarding lag/skew is this: Because the overall mapping between event time and processing time is not static.

## Data Processing Patterns
We look at both types of processing and,where relevant, within the context of the two main types of engines we care about (batch and streaming, where in this context, I’m essentially lumping microbatch in with streaming because the differences between the two aren't terribly important at this level).

### Bounded Data
Bounded DataProcessing bounded data is conceptually quite straightforward, and likely familiar to everyone. In Figure 1-2, we start out on the left with a dataset full of entropy. We run it through some data processing engine (typically batch,though a well-designed streaming engine would work just as well), such asMapReduce, and on the right side end up with a new structured dataset with greater inherent value.

### Unbounded Data: Batch
Batch engines, though not explicitly designed with unbounded data in mind,have nevertheless been used to process unbounded datasets since batch systems were first conceived. As you might expect, such approaches revolve
around slicing up the unbounded data into a collection of bounded datasets appropriate for batch processing.

#### Fixed window
around slicing up the unbounded data into a collection of bounded datasets appropriate for batch processing.Fixed windowsThe most common way to process an unbounded dataset using repeated runs of a batch engine is by windowing the input data into fixed-size windows andthen processing each of those windows as a separate, bounded data source(sometimes also called tumbling windows),

#### Sessions
This approach breaks down even more when you try to use a batch engine to process unbounded data into more sophisticated windowing strategies, likesessions. Sessions are typically defined as periods of activity (e.g., for a specific user) terminated by a gap of inactivity. When calculating sessions using a typical batch engine, you often end up with sessions that are split across batches, as indicated by the red marks in Figure 1-4. We can reduce the number of splits by increasing batch sizes, but at the cost of increased latency.

### Unbounded Data: Streaming
Contrary to the ad hoc nature of most batch-based unbounded data processing approaches, streaming systems are built for unbounded data. As we talked about earlier, for many real-world, distributed input sources, you not only find yourself dealing with unbounded data, but also data such as the following:
* Highly unordered with respect to event times, meaning that you need some sort of time-based shuffle in your pipeline if you want to analyze the data in the context in which they occurred.
* Of varying event-time skew, meaning that you can’t just assume you'll always see most of the data for a given event time X withinsome constant epsilon of time Y

#### Time-agnostic
Time-agnostic processing is used for cases in which time is essentially irrelevant; that is, all relevant logic is data driven. Because everything about such use cases is dictated by the arrival of more data, there’s really nothing special a streaming engine has to support other than basic data delivery. As a result, essentially all streaming systems in existence support time-agnostic use cases out of the box (modulo system-to-system variances in consistency guarantees, of course, if you care about correctness). Batch systems are also well suited for time-agnostic processing of unbounded data sources by simply chopping the unbounded source into an arbitrary sequence of bounded datasets and processing those datasets independently

#### Filtering
A very basic form of time-agnostic processing is filtering, an example of which is rendered in Figure 1-5. Imagine that you’re processing web traffic logs and you want to filter out all traffic that didn’t originate from a specific domain. You would look at each record as it arrived, see if it belonged to the domain of interest, and drop it if not.

#### Inner joins
Another time-agnostic example is an inner join, diagrammed in Figure 1-6.When joining two unbounded data sources, if you care only about the results of a join when an element from both sources arrive, there’s no temporal element to the logic. Upon seeing a value from one source, you can simply buffer it up in a persistent state; only after the second value from the other source arrives do you need to emit the joined record. (In truth, you’d likely want some sort of garbage collection policy for unremitted partial joins, which would likely be time based.

#### Approximation algorithms
The second major category of approaches is approximation algorithms, such as approximate Top-N, streaming k-means, and so on. They take an unbounded source of input and provide output data that, if you squint at them,look more or less like what you were hoping to get, as in Figure 1-7. The Upside of approximation algorithms is that, by design, they are low overhead and designed for unbounded data. The downsides are that a limited set of them exist, the algorithms themselves are often complicated.

#### Windowing
The remaining two approaches for unbounded data processing are both variations of windowing. Before diving into the differences between them, Ishould make it clear exactly what I mean by windowing, insomuch as wetouched on it only briefly in the previous section. Windowing is simply the notion of taking a data source (either unbounded or bounded), and chopping it up along temporal boundaries into finite chunks for processing. Figure 1-8shows three different windowing patterns.

#### Fixed windows(aka tumbling windows)
The remaining two approaches for unbounded data processing are both variations of windowing. Before diving into the differences between them, Ishould make it clear exactly what I mean by windowing, insomuch as wetouched on it only briefly in the previous section. Windowing is simply the notion of taking a data source (either unbounded or bounded), and chopping it up along temporal boundaries into finite chunks for processing. Figure 1-8shows three different windowing patterns.Figure 1-8. Windowing strategies. Each example is shown for three different keys,highlighting the difference between aligned windows (which apply across all the data) and unaligned windows (which apply across a subset of the data).Let’s take a closer look at each strategy:Fixed windows (aka tumbling windows)We discussed fixed windows earlier. Fixed windows slice time into segments with a fixed-size temporal length. Typically (as shown inFigure 1-9), the segments for fixed windows are applied uniformly across the entire dataset, which is an example of aligned windows.

#### Sliding windows (aka hopping windows)
A generalization of fixed windows, sliding windows are defined by a fixed length and a fixed period. If the period is less than the length, thewindows overlap. If the period equals the length, you have fixed windows.

#### Sessions
An example of dynamic windows, sessions are composed of sequences of events terminated by a gap of inactivity greater than some timeout.Sessions are commonly used for analyzing user behavior over time, by grouping together a series of temporally related events (e.g., a sequence of videos viewed in one sitting). Sessions are interesting because their lengths cannot be defined a priori; they are dependent upon the actual data involved

#### Windowing by processing time
When windowing by processing time, the system essentially buffers upincoming data into windows until some amount of processing time has passed. For example, in the case of five-minute fixed windows, the system would buffer data for five minutes of processing time, after which it would treat all of the data it had observed in those five minutes as a window and send them downstream for processing.

Properties:
* It’s simple. The implementation is extremely straightforward because you never worry about shuffling data within time. You just buffer things as they arrive and send them downstream when the window closes.
* There is no need to be able to deal with “late” data in any way when windowing by processing time
* If you’re wanting to infer information about the source as it is observed, processing-time windowing is exactly what you want.
Good points aside, there is one very big downside to processing-time windowing: if the data in question have event times associated with them,those data must arrive in event-time order if the processing-time windows are to reflect the reality of when those events actually happened.

#### Windowing by event time
Event-time windowing is what you use when you need to observe a datasource in finite chunks that reflect the times at which those events actually happened.
if these data had been windowed into processing-time windows for a use case that cared about event times, the calculated results would have been incorrect. As you would expect, event-time correctness is one nice thing about using event-time windows.

##### Drawbacks
* Buffering
** Due to extended window lifetimes, more buffering of data is required.Thankfully, persistent storage is generally the cheapest of the resourcetypes most data processing systems depend on. As such, this problem is typically much less of a concern than you might think when using any well-designed data processing system with a strongly consistent persistent state and a decent in-memory caching layer. Also, many useful aggregations do not require the entire input set to be buffered (e.g., sum or average),but instead can be performed incrementally, with a much smaller,intermediate aggregate stored in persistent state.
* Completeness
** Given that we often have no good way of knowing when we’ve seen all of the data for a given window, how do we know when the results for the window are ready to materialize? But for cases in which absolute correctness is paramount (again, think billing), the only real option is to provide a way for the pipeline builder to express when they want results for windows tobe materialized and how those results should be refined over time.

# Chapter 2. The What, Where,When, and How Of Data Processing
which covers in detail the core concepts of robust stream processing over out-of-order data, each analyzed within the context of a concrete running example and with animated diagrams to highlight the dimension of time.

we’re now going to look closely at three more:

* Triggers
** A trigger is a mechanism for declaring when the output for a window should be materialized relative to some external signal. Triggers provide flexibility in choosing when outputs should be emitted. In some sense,you can think of them as a flow control mechanism for dictating whenresults should be materialized. Another way of looking at it is that triggers are like the shutter-release on a camera, allowing you to declare when to take snapshots in time of the results being computed.
* Watermarks
** A watermark is a notion of input completeness with respect to event times. A watermark with value of time X makes the statement: “all input data with event times less than X have been observed.” As such,watermarks act as a metric of progress when observing an unbounded data source with no known end.
* Accumulation
** An accumulation mode specifies the relationship between multiple results that are observed for the same window. Those results might be completely disjointed; that is, representing independent deltas over time,or there might be overlap between them.

the structure of answering four questions, all of which I propose are critical to every unbounded data processing problem:
* **What** results are calculated? This question is answered by the types of transformations within the pipeline. This includes things like computing sums, building histograms, training machine learning models, and so on. It’s also essentially the question answered by classic batch processing
* **Where** In event time are results calculated? This question is answered by the use of event-time windowing within the pipeline.This includes the common examples of windowing from Chapter 1(fixed, sliding, and sessions); use cases that seem to have no notion of windowing (e.g., time-agnostic processing; classic batch processing also generally falls into this category); and other, more complex types of windowing, such as time-limited auctions. Also Note that it can include processing-time windowing, as well, if you assign ingress times as event times for records as they arrive at the system.
* **When** in processing time are results materialized? This question is answered by the use of triggers and (optionally) watermarks. There Are infinite variations on this theme, but the most common patterns are those involving repeated updates (i.e., materialized view semantics), those that utilize a watermark to provide a single output per window only after the corresponding input is believed to be complete.
* **How** Do refinements of results relate? This question is answered by the type of accumulation used: discarding (in which results are all independent and distinct), accumulating (in which later results build upon prior ones), or accumulating and retracting (in which both the accumulating value plus a retraction for the previously triggered value(s) are emitted).

## Batch Foundations: What and Where
### What: Transformations
The transformations applied in classic batch processing answer the question:“What results are calculated?”

In the rest of this chapter (and indeed, through much of the book), we look at a single example: computing keyed integer sums over a sample dataset consisting of nine values. Let’s imagine that we’ve written a team-based mobile game and we want to build a pipeline that calculates team scores by summing up the individual scores reported by users’ phones. If we were to capture our nine example scores in a SQL table named “UserScores,” it might look something like this:

| Name  | Team  | Score | EventTime | ProcTime |
|----|---|---|---|---|
| Julie | TeamX |     5 |  12:00:26 | 12:05:19 |
| Frank | TeamX |     9 |  12:01:26 | 12:08:19 |
| Ed    | TeamX |     7 |  12:02:26 | 12:05:39 |
| Julie | TeamX |     8 |  12:03:06 | 12:07:06 |
| Amy   | TeamX |     3 |  12:03:39 | 12:06:13 |
| Fred  | TeamX |     4 |  12:04:19 | 12:06:39 |
| Naomi | TeamX |     3 |  12:06:39 | 12:07:19 |
| Becky | TeamX |     8 |  12:07:26 | 12:08:39 |
| Naomi | TeamX |     1 |  12:07:46 | 12:09:00 |

* ScoreThe
** individual user score associated with this event
* EventTime
** The event time for the score; that is, the time at which the score occurred
* ProcTime
** The processing for the score; that is, the time at which the score was observed by the pipeline

Preceding each example is a short snippet of Apache Beam Java SDKpseudocode to make the definition of the pipeline more concrete.

* PCollections
** These represent datasets (possibly massive ones) across which parallel transformations can be performed (hence the “P” at the beginning of the name)
* PTransforms
** These are applied to PCollections to create new PCollections.PTransforms may perform element-wise transformations, they may group/aggregate multiple elements together, or they may be a composite combination of other PTransforms.


For the purposes of our examples, we typically assume that we start out with a preloaded PCollection<KV<Team, Integer>> named “input” (that is, aPCollection composed of key/value pairs of Teams and Integers, where the Teams are just something like Strings representing team names, and theIntegers are scores from any individual on the corresponding team). In areal-world pipeline, we would’ve acquired input by reading in aPCollection<String> of raw data (e.g., log records) from an I/O source and then transforming it into a PCollection<KV<Team, Integer>> by parsing the log records into appropriate key/value pairs. For the sake of clarity in this first example, I include pseudocode for all of those steps, but in subsequent examples, I elide the I/O and parsing.Thus, for a pipeline that simply reads in data from an I/O source, parsesteam/score pairs, and calculates per-team sums of scores, we’d have something like that shown in Example 2-1.
 
```
  PCollection<String> raw = IO.read(...);
  PCollection<KV<Team, Integer>> input = raw.apply(new ParseFn());
  PCollection<KV<Team, Integer>> totals =  input.apply(Sum.integersPerKey());
```

Key/value data are read from an I/O source, with a Team (e.g., String of the team name) as the key and an Integer (e.g., individual team member scores)as the value. The values for each key are then summed together to generate per-key sums (e.g., total team score) in the output collection.

As the pipeline observes values, it accumulates them in its intermediate state and eventually materializes the aggregate results as output. State and outputs are represented by rectangles (gray for state, blue for output), with the aggregate value near the top, and with the area covered by the rectangle representing the portions of event time and processing time accumulated into
the result. For the pipeline in Example 2-1, it would look something like that shown in Figure 2-3 when executed on a classic batch engine.

### Where: Windowing
windowing is the process of slicing up a data 00:00 / 00:00
source along temporal boundaries. Common windowing strategies include fixed windows, sliding windows, and sessions windows, as demonstrated in Figure 2-4.

Let's take our integer summation pipeline and window it into fixed, two-minute windows.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)))  
.apply(Sum.integersPerKey());
```
Figure 2-5

As before, inputs are accumulated in state until they are entirely consumed,after which output is produced. In this case, however, instead of one output,we get four: a single output, for each of the four relevant two-minute event-time windows.

### Going Streaming: When and How

We just observed the execution of a windowed pipeline on a batch engine.But, ideally, we’d like to have lower latency for our results, and we’d also like to natively handle unbounded data sources. Switching to a streaming engine is a step in the right direction, but our previous strategy of waiting until our input has been consumed in its entirety to generate output is no longer feasible. Enter triggers and watermarks.

### When: Trigger
Triggers provide the answer to the question: “When in processing time are results materialized?” Triggers declare when output for a window should happen in processing time (though the triggers themselves might make those decisions based on things that happen in other time domains, such as watermarks progressing in the event-time domain, as we’ll see in a few moments). Each specific output for a window is referred to as a pane of the window.

conceptually there are only two generally useful types of triggers,and practical applications almost always boil down using either one or a combination of both:

* Repeated update triggers 
** These periodically generate updated panes for a window as its contents evolve. These updates can be materialized with every new record, or they can happen after some processing-time delay, such as once a minute. The Choice of period for a repeated update trigger is primarily an exercise in balancing latency and cost.
* Completeness triggers
**  These materialize a pane for a window only after the input for that window is believed to be complete to some threshold. This type of trigger is most analogous to what we’re familiar with in batch processing: only after the input is complete do we provide a result. The difference in the trigger-based approach is that the notion of completeness is scoped to the context of a single window, rather than always being bound to the completeness of the entire input.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(Repeatedly(AfterCount(1))));  .apply(Sum.integersPerKey());
```
Figure 2-6

You can see how we now get multiple outputs (panes) for each window: onceper corresponding input. This sort of triggering pattern works well when the output stream is being written to some sort of table that you can simply pollfor results. Any time you look in the table, you’ll see the most up-to-datevalue for a given window, and those values will converge toward correctness over time.

One downside of per-record triggering is that it’s quite chatty. When Processing large-scale data, aggregations like summation provide a nice opportunity to reduce the cardinality of the stream without losing information.

The nice side effect of using processing-time delays is that it has an equalizing effect across high-volume keys or windows: the resulting stream ends up being more uniform cardinality-wise.

There are two different approaches to processing-time delays in triggers:aligned delays (where the delay slices up processing time into fixed regions that align across keys and windows) and unaligned delays (where the delay relative to the data observed within a given window). A pipeline with unaligned delays might look like Example 2-4, the results of which are shown in Figure 2-7.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(Repeatedly(AlignedDelay(TWO_MINUTES)))  .apply(Sum.integersPerKey());
```

This sort of aligned delay trigger is effectively what you get from amicrobatch streaming system like Spark Streaming. The nice thing about it is predictability; you get regular updates across all modified windows at the same time. That’s also the downside: all updates happen at once, which results in bursty workloads that often require greater peak provisioning to properly handle the load. The alternative is to use an unaligned delay. That Would look something Example 2-5 in Beam. Figure 2-8 presents the results.

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(Repeatedly(UnalignedDelay(TWO_MINUTES))  
.apply(Sum.integersPerKey());
```

Contrasting the unaligned delays in Figure 2-8 to the aligned delays inFigure 2-6, it’s easy to see how the unaligned delays spread the load out more evenly across time. The actual latencies involved for any given window differ between the two, sometimes more and sometimes less, but in the end the average latency will remain essentially the same. From that perspective,unaligned delays are typically the better choice for large-scale processing because they result in a more even load distribution over time.

Repeated update triggers are great for use cases in which we simply want periodic updates to our results over time and are fine with those updates converging toward correctness with no clear indication of when correctness is achieved. However, as we discussed in Chapter 1, the vagaries of distributed systems often lead to a varying level of skew between the time an event happens and the time it’s actually observed by your pipeline, which means it can be difficult to reason about when your output presents an accurate and complete view of your input data. For cases in which input completeness matters, it’s important to have some way of reasoning about completeness rather than blindly trusting the results calculated by whichever subset of data happen to have found their way to your pipeline. Enter watermarks.

### When: Watermarks

Watermarks are a supporting aspect of the answer to the question: “When inprocessing time are results materialized?” Watermarks are temporal notions of input completeness in the event-time domain. Worded differently, they are the way the system measures progress and completeness relative to the eventtimes of the records being processed in a stream of events (either bounded or unbounded, though their usefulness is more apparent in the unbounded case).

Recall this diagram from Chapter 1, slightly modified in Figure 2-9, in whichI described the skew between event time and processing time as an ever-changing function of time for most real-world distributed data processing systems.

That meandering red line that I claimed represented reality is essentially the watermark; it captures the progress of event-time completeness as processing time progresses. Conceptually, you can think of the watermark as a function,F(P) → E, which takes a point in processing time and returns a point in eventtime. That point in event time, E, is the point up to which the system believes all inputs with event times less than E have been observed. In other words, it’s an assertion that no more data with event times less than E will ever be seen again. Depending upon the type of watermark, perfect or heuristic, that assertion can be a strict guarantee or an educated guess,respectively:

* Perfect watermarks
** For the case in which we have perfect knowledge of all of the input data,it’s possible to construct a perfect watermark. In such a case, there is no such thing as late data; all data are early or on time.
* Heuristic watermarks
    ** For many distributed input sources, perfect knowledge of the input data is impractical, in which case the next best option is to provide a heuristic watermark. Heuristic watermarks use whatever information is available about the inputs (partitions, ordering within partitions if any, growth rate of files, etc.) to provide an estimate of progress that is as accurate as possible. In many cases, such watermarks can be remarkably accurate in their predictions. Even so, the use of a heuristic watermark means that it might sometimes be wrong, which will lead to late data

Because they provide a notion of completeness relative to our inputs,watermarks form the foundation for the second type of trigger mentioned previously: completeness triggers.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(AfterWatermark()))  .apply(Sum.integersPerKey());
```

Figure 2-10

A great example of a missing-data use case is outer joins. Without a notion of completeness like watermarks, how do you know when to give up and emit a partial join rather than continue to wait for that join to complete? You don’t.And basing that decision on a processing-time delay, which is the common approach in streaming systems that lack true watermark support, is not a safeway to go, because of the variable nature of event-time skew we spoke about in Chapter 1: as long as skew remains smaller than the chosen processing-time delay, your missing-data results will be correct, but any time skew grows beyond that delay, they will suddenly become incorrect. From this perspective, event-time watermarks are a critical piece of the puzzle for many real-world streaming use cases which must reason about a lack of data in the input, such as outer joins, anomaly detection, and so on.

Now, with that said, these watermark examples also highlight two shortcomings of watermarks (and any other notion of completeness),specifically that they can be one of the following:

* Too slow
** When a watermark of any type is correctly delayed due to known unprocessed data (e.g., slowly growing input logs due to network bandwidth constraints), that translates directly into delays in output of advancement of the watermark is the only thing you depend on for stimulating results.
*  Too fast
** When a heuristic watermark is incorrectly advanced earlier than it should be, it’s possible for data with event times before the watermark to arrive some time later, creating late data.

You simply cannot get both low latency and correctness out of a system that relies solely on notions of completeness. So, for cases for which you do want the best of both worlds, what’s a person to do? Well, if repeated update triggers provide low-latency updates but no way to reason about completeness, and watermarks provide a notion of completeness but variable and possible high latency, why not combine their powers together?

### When: Early/ On-Time / Late Triggers 

We’ve now looked at the two main types of triggers: repeated update triggers and completeness/watermark triggers. In many cases, neither of them alone is sufficient, but the combination of them together is. Beam recognizes this fact by providing an extension of the standard watermark trigger that also supports repeated update triggering on either side of the watermark. This is known as the early/on-time/late trigger because it partitions the panes that are materialized by the compound trigger into three categories:

* Zero or more early panes, which are the result of a repeated update trigger that periodically fires up until the watermark passes the end of the window. The panes generated by these firings containspeculative results, but allow us to observe the evolution of the window over time as new input data arrive. This compensates for the shortcoming of watermarks, sometimes being too slow.
* A single on-time pane, which is the result of the completeness/watermark trigger firing after the watermark passes the end of the window. This firing is special because it provides an assertion that the system now believes the input for this window to be complete. This means that it is now safe to reason about missing data; for example, to emit a partial join when performing an outer join.
* Zero or more late panes, which are the result of another (possibly different) repeated update trigger that periodically fires any time latedata arrive after the watermark has passed the end of the window. Inthe case of a perfect watermark, there will always be zero late panes.But in the case of a heuristic watermark, any data the watermark failed to properly account for will result in a late firing. This Compensates for the shortcoming of watermarks being too fast.

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(AfterWatermark()
.withEarlyFirings(AlignedDelay(ONE_MINUTE))
.withLateFirings(AfterCount(1))))  
.apply(Sum.integersPerKey());
```

This version has two clear improvements over Figure 2-9.

* For the “watermarks too slow” case in the second window, [12:02,12:04): we now provide periodic early updates once per minute. The Difference is most stark in the perfect watermark case, for which time-to-first-output is reduced from almost seven minutes down to three and a half; but it’s also clearly improved in the heuristic case,as well. Both versions now provide steady refinements over time(panes with values 7, 10, then 18), with relatively minimal latency between the input becoming complete and materialization of the final output pane for the window.

* For the “heuristic watermarks too fast” case in the first window,00:00 / 00:00
[12:00, 12:02): when the value of 9 shows up late, we immediately incorporate it into a new, corrected pane with value of 14.

One interesting side effect of these new triggers is that they effectively normalize the output pattern between the perfect and heuristic watermark versions. Whereas the two versions in Figure 2-10 were starkly different, the two versions here look quite similar. They also look much more similar to the various repeated update versions from Figures 2-6 through 2-8, with one important difference: thanks to the use of the watermark trigger, we can also reason about input completeness in the results we generate with the early/on-time/late trigger. This allows us to better handle use cases that care about missing data, like outer joins, anomaly detection, and so on.

The biggest remaining difference between the perfect and heuristic early/on-time/late versions at this point is window lifetime bounds. In the perfect watermark case, we know we’ll never see any more data for a window after the watermark has passed the end of it, hence we can drop all of our state for the window at that time. In the heuristic watermark case, we still need to hold on to the state for a window for some amount of time to account for late data.But as of yet, our system doesn’t have any good way of knowing just howlong state needs to be kept around for each window. That’s where allowed lateness comes in.

### When: Allowed Lateness(i.e., Garbage Collection)
the persistent state for each window lingers around for the entire lifetime of the example; this is necessary to allow us to appropriately deal with late data when/if they arrive. But while it would be great to be able to keep around all of our persistent state until the end of time, in reality, when dealing with an unbounded data source, it's often not practical to keep state (including metadata) for a given window indefinitely; we’ll eventually run out of disk space (or at the very least tire of paying for it, as the value for older data diminishes over time).As a result, any real-world out-of-order processing system needs to provide some way to bound the lifetimes of the windows it’s processing. A clean and concise way of doing this is by defining a horizon on the allowed lateness within the system; that is, placing a bound on how late any given record maybe (relative to the watermark) for the system to bother processing it; any data that arrives after this horizon are simply dropped. After you’ve bounded how late individual data may be, you’ve also established precisely how long thestate for windows must be kept around: until the watermark exceeds the lateness horizon for the end of the window. But in addition, you’ve also given the system the liberty to immediately drop any data later than the horizon as soon as they’re observed, which means the system doesn’t waste resources processing data that no one cares about.

Because the interaction between allowed lateness and the watermark is a little subtle, it’s worth looking at an example. Let’s take the heuristic watermark pipeline from Example 2-7/Figure 2-11 and add in Example 2-8 a lateness horizon of one minute (note that this particular horizon has been chosen strictly because it fits nicely into the diagram; for real-world use cases, larger horizon would likely be much more practical):

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(
AfterWatermark()
.withEarlyFirings(AlignedDelay(ONE_MINUTE))
.withLateFirings(AfterCount(1)))
.withAllowedLateness(ONE_MINUTE)) 
.apply(Sum.integersPerKey());
```
Figure 2-12.

Two final side notes about lateness horizons:

* To be absolutely clear, if you happen to be consuming data from sources for which perfect watermarks are available, there’s no need to deal with late data, and an allowed lateness horizon of zero seconds will be optimal. This is what we saw in the perfect watermark portion of Figure 2-10
* One noteworthy exception to the rule of needing to specify lateness horizons, even when heuristic watermarks are in use, would be something like computing global aggregates over all time for atractably finite number of keys (e.g., computing the total number of 00:00 / 00:00
visits to your site over all time, grouped by web browser family). In This case, the number of active windows in the system is bounded by the limited keyspace in use. As long as the number of keys remains manageable low, there’s no need to worry about limiting the lifetime of windows via allowed lateness.

### How: Accumulation

When triggers are used to produce multiple panes for a single window overtime, we find ourselves confronted with the last question: “How do refinements of results relate?” In the examples we’ve seen so far, each successive pane is built upon the one immediately preceding it. However,there are actually three different modes of accumulation:

* Discarding
** Every time a pane is materialized, any stored state is discarded. This Means that each successive pane is independent from any that came before. Discarding mode is useful when the downstream consumer is performing some sort of accumulation itself; for example, when sending integers into a system that expects to receive deltas that it will sum together to produce a final count.

* Accumulating
** As in Figures 2-6 through 2-11, every time a pane is materialized, any stored state is retained, and future inputs are accumulated into the existing state. This means that each successive pane builds upon the previous panes. Accumulating mode is useful when later results can simply overwrite previous results, such as when storing output in a key/value store like HBase or Bigtable.

* Accumulating and retracting
** This is like accumulating mode, but when producing a new pane, it also produces independent retractions for the previous pane(s). Retractions (combined with the new accumulated result) are essentially an explicit way of saying “I previously told you the result was X, but I was wrong.Get rid of the X I told you last time, and replace it with Y.” There are two cases for which retractions are particularly helpful:

When consumers downstream are regrouping data by a different dimension, it’s entirely possible the new value may end up keyed differently from the previous value and thus end up in a different group. In that case, the new value can’t just overwrite the old value;you instead need the retraction to remove the old value
When dynamic windows (e.g., sessions, which we look at more closely in a few moments) are in use, the new value might be replacing more than one previous window, due to window merging.In this case, it can be difficult to determine from the new window alone which old windows are being replaced. Having explicit retractions for the old windows makes the task straightforward. Wesee an example of this in detail in Chapter 8

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(
AfterWatermark()
.withEarlyFirings(AlignedDelay(ONE_MINUTE))
.withLateFirings(AtCount(1)))
.discardingFiredPanes())  
.apply(Sum.integersPerKey());
```

Figure 2-13, Figure 2-14

# Chapter 3. Watermarks

Consider any pipeline that ingests data and outputs results continuously. We Wish to solve the general problem of when it is safe to call an event-time window closed, meaning that the window does not expect any more data. Todo so we would like to characterize the progress that the pipeline is making relative to its unbounded input.

One naive approach for solving the event-time windowing problem would beto simply base our event-time windows on the current processing time. As wesaw in Chapter 1, we quickly run into trouble—data processing and transport is not instantaneous, so processing and event times are almost never equal.

n most cases, we can take the time of the original event’s occurrence as its logical event timestamp. With all input messages containing an event timestamp, we can then examine the distribution of such timestamps in any pipeline. Such a pipeline might be distributed to process in parallel over many agents and consuming input messages with no guarantee of ordering between individual shards. Thus, the set of event timestamps for active in-flight messages in this pipeline will forma distribution, as illustrated in Figure 3-1.

There is a key point on this distribution, located at the leftmost edge of the“in-flight” distribution, corresponding to the oldest event timestamp of any unprocessed message of our pipeline. We use this value to define the watermark:

The watermark is a monotonically increasing timestamp of the oldest work not yet completed.

There are two fundamental properties that are provided by this definition that make it useful:

* Completeness
    ** If the watermark has advanced past some timestamp T, we are guaranteed by its monotonic property that no more processing will occur for on-time(non lte data) events at or before T. Therefore, we can correctly emit any aggregations at or before T. In other words, the watermark allows us to know when it is correct to close a window.
* VIsibility
    ** If a message is stuck in our pipeline for any reason, the watermark cannot advance. Furthermore, we will be able to find the source of the problem by examining the message that is preventing the watermark from advancing.

## Source Watermark Creation
Where do these watermarks come from? To establish a watermark for a datasource, we must assign a logical event timestamp to every message entering the pipeline from that source. As Chapter 2 informs us, all watermark creation falls into one of two broad categories: perfect or heuristic.

#### Perfect Watermark Creation
Perfect watermark creation assigns timestamps to incoming messages in such way that the resulting watermark is a strict guarantee that no data withevent times less than the watermark will ever be seen again from this source.Pipelines using perfect watermark creation never have to deal with late data;that is, data that arrive after the watermark has advanced past the event times of newly arriving messages. However, perfect watermark creation requires perfect knowledge of the input, and thus is impractical for many real-world distributed input sources. Here are a couple of examples of use cases that can create perfect watermarks:

* Ingress timestamping
** A source that assigns ingress times as the event times for data entering the system can create a perfect watermark. In this case, the source watermark simply tracks the current processing time as observed by the pipeline.

The downside, of course, is that the watermark has no correlation to the event times of the data themselves; those event times were effectively discarded, and the watermark instead merely tracks the progress of data relative to its arrival in the system.

* Static set of time-ordered logs
** A statically sized input source of time-ordered logs (e.g., an ApacheKafka topic with a static set of partitions, where each partition of the source contains monotonically increasing event times) would be relatively straightforward source atop which to create a perfect watermark. To do so, the source would simply track the minimum eventtime of unprocessed data across the known and static set of source partitions (i.e., the minimum of the event times of the most recently readrecord in each of the partitions).

### Heuristic Watermark Creation
Heuristic watermark creation, on the other hand, creates a watermark that is merely an estimate that no data with event times less than the watermark willever be seen again. Pipelines using heuristic watermark creation might need to deal with some amount of late data.

For many real-world, distributed input sources, it’s computationally or operationally impractical to construct a perfect watermark, but still possible to build a highly accurate heuristic watermark by taking advantage of structural features of the input data source. Following are two example for which heuristic watermarks (of varying quality) are possible:

* Dynamic sets of time-ordered logs
** Consider a dynamic set of structured log files (each individual file containing records with monotonically increasing event times relative to other records in the same file but with no fixed relationship of event times between files), where the full set of expected log files (i.e., partitions, inKafka parlance) is not known at runtime. Such inputs are often found in global-scale services constructed and managed by a number of independent teams. In such a use case, creating a perfect watermark over the input is intractable, but creating an accurate heuristic watermark is quite possible.By tracking the minimum event times of unprocessed data in the existing set of log files, monitoring growth rates, and utilizing external information like network topology and bandwidth availability, you can create a remarkably accurate watermark, even given the lack of perfect knowledge of all the inputs. This type of input source is one of the most common types of unbounded datasets found at Google, so we have extensive experience with creating and analyzing watermark quality for such scenarios and have seen them used to good effect across a number of use cases.

* Google Cloud Pub / Sub
** Cloud Pub/Sub is an interesting use case. Pub/Sub currently makes no guarantees on in-order delivery; even if a single publisher publishes two messages in order, there’s a chance (usually small) that they might be delivered out of order (this is due to the dynamic nature of the underlying architecture, which allows for transparent scaling up to very high levels of throughput with zero user intervention). As a result, there’s no way to guarantee a perfect watermark for Cloud Pub/Sub. The Cloud Dataflowteam has, however, built a reasonably accurate heuristic watermark by taking advantage of what knowledge is available about the data in CloudPub/Sub.

### Watermark Propagation

So far, we have considered only the watermark for the inputs within the context of a single operation or stage. However, most real-world pipelines consist of multiple stages. Understanding how watermarks propagate across independent stages is important in understanding how they affect the pipeline as a whole and the observed latency of its results.

We can define watermarks at the boundaries of any single operation, or stage, in the pipeline. This is useful not only in understanding the relative progress that each stage in the pipeline is making, but for dispatching timely results independently and as soon as possible for each individual stage. We give the following definitions for the watermarks at the boundaries of stages:

* An input watermark, which captures the progress of everything upstream of that stage (i.e., how complete the input is for that stage).For sources, the input watermark is a source-specific function creating a watermark for the input data. For non source stages, the input watermark is defined as the minimum of the output watermarks of all shards/partitions/instances of all of its upstream sources and stages.

* An output watermark, which captures the progress of the stage itself,and is essentially defined as the minimum of the stage’s input watermark and the event times of all non lte data active messages within the stage. Exactly what “active” encompasses is somewhat dependent upon the operations a given stage actually performs, and the implementation of the stream processing system. It typically includes data buffered for aggregation but not yet materialized downstream, pending output data in flight to downstream stages, and so on.

One nice feature of defining an input and output watermark for a specific stage is that we can use these to calculate the amount of event-time latency introduced by a stage. Subtracting the value of a stage’s output watermark from the value of its input watermark gives the amount of event-time latency or lag introduced by the stage. This lag is the notion of how far delayed behind real time the output of each stage will be.


Processing within each stage is also not monolithic. We can segment the processing within one stage into a flow with several conceptual components,each of which contributes to the output watermark. As mentioned previously,the exact nature of these components depends on the operations the stage performs and the implementation of the system. Conceptually, each such component serves as a buffer where active messages can reside until some operation has completed. For example, as data arrives, it is buffered for processing. Processing might then write the data to state for later delayed aggregation. Delayed aggregation, when triggered, might write the results toan output buffer awaiting consumption from a downstream stage, as shown inFigure 3-3.

We can track each such buffer with its own watermark. The minimum of the watermarks across the buffers of each stage forms the output watermark of the stage. Thus the output watermark could be the minimum of the following:

* Per-source watermark—for each sending stage.
* Per-external input watermark—for sources external to the pipeline
* Per-state component watermark—for each type of state that can be written
* Per-output buffer watermark—for each receiving stage

Making watermarks available at this level of granularity also provides better visibility into the behavior of the system. The watermarks track locations of messages across various buffers in the system, allowing for easier diagnosis of stuckness.

#### Understanding Watermark Propagation

#### Tricky case of Overlapping Windows

However, if output timestamps are chosen to be the timestamp of the first inline element in the pane, what actually happens is the following:
* The first window completes in the first stage and is emitted downstream.
* The first window in the second stage remains unable to complete because its input watermark is being held up by the output watermark of the second and third windows upstream. Those Watermarks are rightly being held back because the earliest element timestamp is being used as the output timestamp for those windows.
* The second window completes in the first stage and is emitted downstream.
* The first and second windows in the second stage remain unable to complete, held up by the third window upstream. 
* The third window completes in the first stage and is emitted downstream.
* The first, second, and third windows in the second stage are now available to complete, finally emitting all three in one swoop

Although the results of this windowing are correct, this leads to the results being materialized in an unnecessarily delayed way. Because of this, Beamhas special logic for overlapping windows that ensures the output timestamp for window N+1 is always greater than the end of window N.

#### Percentile watermarks
Instead of considering the minimum point of the distribution, we could take any percentile of the distribution and say that we are guaranteed to have processed this percentage of all events with earlier timestamps.What is the advantage of this scheme? If the business logic “mostly '' is sufficient, percentile watermarks provide a mechanism by which the watermark can advance more quickly and more smoothly than if we were tracking the minimum event time by discarding outliers in the long tail of the distribution from the watermark. Figure 3-9 shows a compact distribution of event times where the 90 percentile watermark is close to the 100 percentile.

#### Processing-Time Watermarks


# Advanced Windowing

We first look at processing-time windowing, which is an interesting mix of both where and when, to understand better how it relates to event-time windowing and get a sense for times when it’s actually the right approach to take. We then dive into some more advanced event-time windowing concepts, looking at session windows in detail, and finally making a case forwhy generalized custom windowing is a useful (and surprisingly straightforward) concept by exploring three different types of custom windows: unaligned fixed windows, per-key fixed windows, and bounded sessions windows.

## When/Where: Processing-Time Windows
Processing-time windowing is important for two reasons:
* For certain use cases, such as usage monitoring (e.g., web service traffic QPS), for which you want to analyze an incoming stream of data as it’s observed, processing-time windowing is absolutely the appropriate approach to take.
* For use cases for which the time that events happened is important(e.g., analyzing user behavior trends, billing, scoring, etc.)processing-time windowing is absolutely the wrong approach to take, and being able to recognize these cases is critical.

there are two methods that you can use to achieve processing-time windowing:

* Triggers
** Ignore event time (i.e., use a global window spanning all of event time)and use triggers to provide snapshots of that window in the processing-time axis.
* Ingress time
** Assign ingress times as the event times for data as they arrive, and use normal event-time windowing from there on. This is essentially what something like Spark Streaming 1.x does.

### Processing-Time Windowing via Triggers
There are three aspects to making processing-time “windowing” work in this manner:

* Windowing 
** We use the global event-time window because we’re essentially emulating processing-time windows with event-time panes.
* Triggering
** We trigger periodically in the processing-time domain based on the desired size of the processing-time windows.
* We use discarding mode to keep the panes independent from one another,thus letting each of them act like an independent processing-time“window.”

Figure 4-3.

### Processing-Time Windowing via Ingress Time
Lastly, let’s look at processing-time windowing achieved by mapping the event times of input data to their ingress times. Code-wise, there are four aspects worth mentioning here:

* Time-shifting 
** When elements arrive, their event times need to be overwritten with the time of ingress. We can do this in Beam by providing a new DoFn that sets the timestamp of the element to the current time via theoutputWithTimestamp method.
* Windowing
** Return to using standard event-time fixed windowing.
* Triggering
** Because ingress time affords the ability to calculate a perfect watermark,we can use the default trigger, which in this case implicitly fires exactly once when the watermark passes the end of the window.
* Accumulation mode
** Because we only ever have one output per window, the accumulation mode is irrelevant.

Because perfect watermarks are possible when using ingress time,the actual watermark matches the ideal watermark, ascending up and to the right with a slope of one.

Note that the two methods are more or less equivalent, although they differ slightly in the case of multistage pipelines: in the triggers version, multistage pipeline will slice the processing-time “windows” independently at each stage, so, for example, data in window N for one stage might instead end up in window N–1 or N+1 in the following stage; in the ingress-timeversion, after a datum is incorporated into window N, it will remain in window N for the duration of the pipeline due to synchronization of progress between stages via watermarks (in the Cloud Dataflow case), microbatch boundaries (in the Spark Streaming case), or whatever other coordinating factor is involved at the engine level.

As I’ve noted to death, the big downside of processing-time windowing is that the contents of the windows change when the observation order of the inputs changes

### Where: Session Windows

Now we’re going to look at one of my favorite features: the dynamic, data-driven windows called sessions.

Sessions are a special type of window that captures a period of activity in the data that is terminated by a gap of inactivity. They’re particularly useful in data analysis because they can provide a view of the activities for a specific user over a specific period of time during which they were engaged in some activity. This allows for the correlation of activities within the session,drawing inferences about levels of engagement based on the lengths of the sessions and so on.

From a windowing perspective, sessions are particularly interesting in two ways:
* They are an example of a data-driven window: the location and sizes of the windows are a direct consequence of the input data themselves, rather than being based on some predefined pattern within time, as are fixed and sliding windows.
* They are also an example of an unaligned window; that is, a window that does not apply uniformly across the data, but instead only to specific subset of the data (e.g., per user). This is in contrast to align windows like fixed and sliding windows, which typically apply uniformly across the data.

Figure 4-5.
They key insight in providing general session support is that a complete session window is, by definition, a composition of a set of smaller,overlapping windows, each containing a single record, with each record in the sequence separated from the next by a gap of inactivity no larger than a predefined timeout. Thus, even if we observe the data in the session out of order, we can build up the final session simply by merging together any overlapping windows for individual data as they arrive.

To look at this another way, consider the example we’ve been using so far. Ifwe specify a session timeout of one minute, we would expect to identify two sessions in the data, delineated in Figure 4-6 by the dashed black lines. Each Of those sessions captures a burst of activity from the user, with each event in the session separate by less than one minute from at least one other event in the session.

* Custom Windows
* Unaligned windows

* Assignment 
** Each element is initially placed into a proto-session window that begins at the element’s timestamp and extends for the gap duration.
* Merging
** At grouping time, all eligible windows are sorted, after which any overlapping windows are merged together.


# Chapter 6. Exactly-Once and Side effects

Streaming systems often talk about exactly-once processing; that is, ensuring that every record is processed exactly one time

## Why Exactly Once Matters
It almost goes without saying that for many users, any risk of dropped records or data loss in their data processing pipelines is unacceptable. Evenso, historically many general-purpose streaming systems made no guarantees about record processing—all processing was “best effort” only. Other Systems provided at-least-once guarantees, ensuring that records were always processed at least once, but records might be duplicated (and thus result in inaccurate aggregations); in practice, many such at-least-once systems performed aggregations in memory, and thus their aggregations could still be lost when machines crashed. These systems were used for low-latency,speculative results but generally could guarantee nothing about the veracity of these results.

Evenso, historically many general-purpose streaming systems made no guarantees about record processing—all processing was “best effort” only. Other Systems provided at-least-once guarantees, ensuring that records were always processed at least once, but records might be duplicated (and thus result in inaccurate aggregations); in practice, many such at-least-once systems performed aggregations in memory, and thus their aggregations could still be lost when machines crashed. These systems were used for low-latency,speculative results but generally could guarantee nothing about the veracity of these results.

### Accuracy Versus Completeness
Whenever a Beam pipeline processes a record for a pipeline, we want to ensure that the record is never dropped or duplicated. However, the nature of streaming pipelines is such that records sometimes show up late, after aggregates for their time windows have already been processed. The BeamSDK allows the user to configure how long the system should wait for latedata to arrive; any (and only) records arriving later than this deadline are dropped. This feature contributes to completeness, not to accuracy: all records that showed up in time for processing are accurately processed exactly once,whereas these late records are explicitly dropped.

### Side effects
One characteristic of Beam and Dataflow is that users inject custom code that is executed as part of their pipeline graph. Data Flow does not guarantee that this code is run only once per record, whether by the streaming or batch runner. It might run a given record through a user transform multiple times,or it might even run the same record simultaneously on multiple workers; this is necessary to guarantee at-least-once processing in the face of worker failures. Only one of these invocations can “win” and produce output further down the pipeline.

As a result, non idempotent side effects are not guaranteed to execute exactly once; if you write code that has side effects external to the pipeline, such as contacting an outside service, these effects might be executed more than once for a given record. This situation is usually unavoidable because there is no way to atomically commit Dataflow’s processing with the side effect on the external service. Pipelines do need to eventually send results to the outside world, and such calls might not be idempotent. As you will see later in thechapter, often such sinks are able to add an extra stage to restructure the callinto an idempotent operation first.

This pipeline computes two different windowed aggregations. The first counts how many events came from each individual user over the course of a minute, and the second counts how many total events came in each minute.Both aggregations are written to unspecified streaming sinks.

Remember that Dataflow executes pipelines on many different workers in parallel. After each GroupByKey (the Count operations use GroupByKeyunder the covers), all records with the same key are processed on the same machine following a process called shuffle. The Dataflow workers shuffle data between themselves using Remote Procedure Calls (RPCs), ensuring that records for a given key all end up on the same machine.

Figure 5-1.

The Count.perKey shuffles all the data for each user onto a given worker, whereas the Count.globally shuffles all these partial count to a single worker to calculate the global sum.

For Dataflow to accurately process data, this shuffle process must ensure that every record is shuffled exactly once. As you will see in a moment, the distributed nature of shuffle makes this a challenging problem.

This pipeline also both reads and writes data from and to the outside world,so Dataflow must ensure that this interaction does not introduce any inaccuracies. Dataflow has always supported this task—what Apache Sparkand Apache Flink call end-to-end exactly once—for sources and sinks whenever technically feasible.

The focus of this chapter will be on three things:

* Shuffle
** How Dataflow guarantees that every record is shuffled exactly once.
* Sources
** How Dataflow guarantees that every source record is processed exactlyonce.
* Sinks
** How Dataflow guarantees that every sink produces accurate output.

### Ensuring Exactly Once in Shuffle
As just explained, Dataflow’s streaming shuffle uses RPCs. Now, any time you have two machines communicating via RPC, you should think long and hard about data integrity. First of all, RPCs can fail for many reasons. The Network might be interrupted, the RPC might time out before completing, or the receiving server might decide to fail the call. To guarantee that records are not lost in shuffle, Dataflow employs upstream backup. This simply means that the sender will retry RPCs until it receives positive acknowledgment of receipt. Dataflow also ensures that it will continueretrying these RPCs even if the sender crashes. This guarantees that every record is delivered at least once.

Now, the problem is that these retries might themselves create duplicates.Most RPC frameworks, including the one Dataflow uses, provide the sender with a status indicating success or failure. In a distributed system, you need to be aware that RPCs can sometimes succeed even when they have appeared tofail. There are many reasons for this: race conditions with the RPC timeout,positive acknowledgment from the server failing to transfer even though theRPC succeeded, and so on. The only status that a sender can really trust is a successful one.

An RPC returning a failure status generally indicates that the call might or might not have succeeded. Although specific error codes can communicate unambiguous failure, many common RPC failures, such as Deadline

Exceeded, are ambiguous. In the case of streaming shuffle, retrying an RPCthat really succeeded means delivering a record twice! Dataflow needs some way of detecting and removing these duplicates.

At a high level, the algorithm for this task is quite simple (see Figure 5-2):every message sent is tagged with a unique identifier. Each receiver stores acatalog of all identifiers that have already been seen and processed. Everytime a record is received, its identifier is looked up in this catalog. If it is found, the record is dropped as a duplicate. Because Data Flow is built on top of a scalable key/value store, this store is used to hold the deduplication catalog.

### Addressing Determinism

Making this strategy work in the real world requires a lot of care, however.One immediate wrinkle is that the Beam Model allows for user code to produce non deterministic output. This means that a ParDo can execute twice on the same input record (due to a retry), yet produce different output on each retry. The desired behavior is that only one of those outputs will commit into the pipeline; however, the nondeterminism involved makes it difficult to guarantee that both outputs have the same deterministic ID. Even trickier, aParDo can output multiple records, so each of these retries might produce a different number of outputs!

Our experience is that in practice, many pipelines require nondeterministic transforms And all too often, pipeline authors do not realize that the code they wrote is nondeterministic.

And even if the user code is purely deterministic, anyevent-time aggregation that allows for late data might have non deterministic inputs.

Dataflow addresses this issue by using checkpointing to make non deterministic processing effectively deterministic. Each output from a transform is checkpointed, together with its unique ID, to stable storage before being delivered to the next stage. Any retries in the shuffle delivery simply replay the output that has been checkpointed—the user's nondeterministic code is not run again on retry. To put it another way, the user's code may be run multiple times but only one of those runs can “win.''Furthermore, Dataflow uses a consistent store that allows it to prevent duplicates from being written to stable storage.

### Performance

To implement exactly-once shuffle delivery, a catalog of record IDs is stored in each receiver key. For every record that arrives, Dataflow looks up thecatalog of IDs already seen to determine whether this record is a duplicate.Every output from step to step is checkpointed to storage to ensure that the generated record IDs are stable.However, unless implemented carefully, this process would significantly degrade pipeline performance for customers by creating a huge increase in reads and writes. Thus, for exact-once processing to be viable for Dataflowusers, that I/O has to be reduced, in particular by preventing I/O on every record.Data Flow achieves this goal via two key techniques: graph optimization and bloom filters.

#### Graph Optimization

The Dataflow service runs a series of optimizations on the pipeline graph before executing it. One such optimization is fusion, in which the servicefuses many logical steps into a single execution stage. Figure 5-3 shows some simple examples.

All fused steps are run as an in-process unit, so there’s no need to store exactly-once data for each of them. In many cases, fusion reduces the entire graph down to a few physical steps, greatly reducing the amount of data transfer needed (and saving on state usage, as well).

#### Bloom filters

In a healthy pipeline, most arriving records will not be duplicates. We can use that fact to greatly improve performance via Bloom filters, which are compact data structures that allow for quick set-membership checks. Bloom Filters have a very interesting property: they can return false positives but never false negatives. If the filter says “Yes, the element is in the set,” we know that the element is probably in the set (with a probability that can be calculated). However, if the filter says an element is not in the set, it definitely isn’t. This function is a perfect fit for the task at hand.

The implementation in Dataflow works like this: each worker keeps a Bloom Filter of every ID it has seen. Whenever a new record ID shows up, it looks it up in the filter. If the filter returns false, this record is not a duplicate and the worker can skip the more expensive lookup from stable storage. It needs to do that second lookup only if the Bloom filter returns true, but as long as thefilter’s false-positive rate is low, that step is rarely needed.

Bloom filters tend to fill up over time, however, and as that happens, the false-positive rate increases. We also need to construct this Bloom filter anew any time a worker restarts by scanning the ID catalog stored in state.Helpfully, Dataflow attaches a system timestamp to each record. Thus,instead of creating a single Bloom filter, the service creates a separate one for every 10-minute range. When a record arrives, Dataflow queries the appropriate filter based on the system timestamp. This step prevents theBloom filters from saturating because filters are garbage-collected over time,and it also bounds the amount of data that needs to be scanned at startup.

#### Garbage Collection

Every Dataflow worker persistently stores a catalog of unique record IDs it has seen. As Dataflow’s state and consistency model is per-key, in reality each key stores a catalog of records that have been delivered to that key. Wecan’t stop these identifiers forever, or all available storage will eventually fill up. To avoid that issue, you need garbage collection of acknowledged recordIDs.

One strategy for accomplishing this goal would be for senders to tag each record with a strictly increasing sequence number in order to track the earliest sequence number still in flight (corresponding to an unacknowledged record delivery). Any identifier in the catalog with an earlier sequence number could then be garbage-collected because all earlier records have already been acknowledged.

There is a better alternative, however. As previously mentioned, Dataflowalready tags each record with a system timestamp that is used for bucketing exactly-once Bloom filters. Consequently, instead of using sequence numbers to garbage-collect the exactly-once catalog, Dataflow calculates a garbage-collection watermark based on these system timestamps (this is the processing-time watermark discussed in Chapter 3). A nice side benefit of this approach is that because this watermark is based on the amount of physical time spent waiting in a given stage (unlike the data watermark,which is based on custom event times), it provides intuition on what parts of the pipeline are slow. This metadata is the basis for the System Lag metrics shown in the Dataflow WebUI.

### Exactly Once in Sources
Beam provides a source API for reading data into a Dataflow pipeline.Data Flow might retry reads from a source if processing fails and needs to ensure that every unique record produced by a source is processed exactly once.

For most sources Dataflow handles this process transparently; such sources are deterministic. For example, consider a source that reads data out of files.The records in a file will always be in a deterministic order and deterministic byte locations, no matter how many times the file is read. The Filename and byte location uniquely identify each record, so the service can automatically generate unique IDs for each record. Another source that provides similar determinism guarantees is Apache Kafka; each Kafka topic is divided into a static set of partitions, and records in a partition always have a deterministic order. Such deterministic sources will work seamlessly inDataflow with no duplicates.

### Exactly Once in Sinks
At some point, every pipeline needs to output data to the outside world, and a sink is simply a transform that does exactly that. Keep in mind that delivering data externally is a side effect, and we have already mentioned that Dataflowdoes not guarantee exactly-once application of side effects. So, how can a sink guarantee that outputs are delivered exactly once?

The simplest answer is that a number of built-in sinks are provided as part of the Beam SDK. These sinks are carefully designed to ensure that they do not produce duplicates, even if executed multiple times. Whenever possible,pipeline authors are encouraged to use one of these built-in sinks.

However, sometimes the built-ins are insufficient and you need to write your own. The best approach is to ensure that your side-effect operation is idempotent and therefore robust in the face of replay. However, often some component of a side-effect DoFn is nondeterministic and thus might change on replay. For example, in a windowed aggregation, the set of records in the window can also be nondeterministic!

There are other ways nondeterminism can be introduced. The standard way to address this risk is to rely on the fact that Dataflow currently guarantees that only one version of a DoFn’s output can make it past a shuffle boundary.

A simple way of using this guarantee is via the built-in Reshuffle transform.The pattern presented in Example 5-2 ensures that the side-effect operation always receives a deterministic record to output,

The preceding pipeline splits the sink into two steps: PrepareOutputDataand WriteToSideEffect. PrepareOutputData outputs records corresponding to idempotent writes. If we simply ran one after the other, the entire process might be replayed on failure, PrepareOutputData might produce a different result, and both would be written as side effects. Whenwe add the Reshuffle in between the two, Dataflow guarantees this can't happen.

Of course, Data Flow might still run the WriteToSideEffect operation multiple times. The side effects themselves still need to be idempotent, or the sink will receive duplicates. For example, an operation that sets or overwrites a value in a data store is idempotent, and will generate correct output even if it's run several times. An operation that appends to a list is not idempotent; if the operation is run multiple times, the same value will be appended each time.

While Reshuffle provides a simple way of achieving stable input to a DoFn,a GroupByKey works just as well. However, there is currently a proposal that removes the need to add a GroupByKey to achieve stable input into a DoFn.Instead, the user could annotateWriteToSideEffect with a special annotation, @RequiresStableInput, and the system would then ensure stable input to that transform.

# Chapter 6. Streams and Tables

## Stream and Table Basics
The basic idea of streams and tables derives from the database world. Anyone Familiar with SQL is likely familiar with tables and their core properties,roughly summarized as: tables contain rows and columns of data, and eachrow is uniquely identified by some sort of key, either explicit or implicit.

If you think back to your database systems class in college, you’ll probably recall the data structure underlying most databases is an append-only log. Transactions are applied to a table in the database, those transactions are recorded in a log, the contents of which are then serially applied to the table to materialize those updates. In streams and tables nomenclature, that log is effectively the stream,

From that perspective, we now understand how to create a table from astream: the table is just the result of applying the transaction log of updates found in the stream. But how to do we create a stream from a table? It's Essentially the inverse: a stream is a changelog for a table. The motivating example typically used for table-to-stream conversion is materialized views.Materialized views in SQL let you specify a query on a table, which itself is then manifested by the database system as another first-class table. Thismaterialized view is essentially a cached version of that query, which the database system ensures is always up to date as the contents of the sourcetable evolve over time. Perhaps unsurprisingly, materialized views are implemented via the changelog for the original table; any time the source table changes, that change is logged. The database then evaluates that change within the context of the materialized view’s query and applies any resulting change to the destination materialized view table.

Combining these two points together and employing yet another questionable physics analogy, we arrive at what one might call the Special Theory ofStream and Table Relativity:
* Streams → tables
** The aggregation of a stream of updates over time yields a table.
* Tables → streams
** The observation of changes to a table over time yields a stream.


#### General theory of stream and table
* Tables are data at rest.
** This isn’t to say tables are static in any way; nearly all useful tables are continuously changing over time in some way. But at any giventime, a snapshot of the table provides some sort of picture of the dataset contained together as a whole. In that way, tables act as a conceptual resting place for data to accumulate and be observed overtime. Hence, data at rest.
* Streams are data in motion.
** Whereas tables capture a view of the dataset as a whole at a specific point in time, streams capture the evolution of that data over time.Julian Hyde is fond of saying streams are like the derivatives of tables, and tables the integrals of streams, which is a nice way of thinking about it for you math-minded individuals out there.Regardless, the important feature of streams is that they capture the inherent movement of data within a table as it changes. Hence, data in motion.

## Batch Processing VS Streams and Tables

### A Streams and Tables Analysis of MapReduce
To keep our analysis relatively simple, but solidly concrete, as it were, let's look at how a traditional MapReduce job fits into the streams/tables world.As alluded to by its name, a MapReduce job superficially consists of two phases: Map and Reduce. For our purposes, though, it’s useful to look a little deeper and treat it more like six:

* MapRead
** This consumes the input data and preprocesses them a bit into a standard key/value form for mapping.
* Map
** This repeatedly (and/or in parallel) consumes a single key/value pair from the preprocessed input and outputs zero or more key/value pairs.
* MapWrite
** This clusters together sets of Map-phase output values having identical keys and writes those key/value-list groups to (temporary) persistent storage. In this way, the MapWrite phase is essentially a group-by-key-and-checkpoint operation
* ReduceRead
** This consumes the saved shuffle data and converts them into a standard key/value-list form for reduction.
* Reduce
** This repeatedly (and/or in parallel) consumes a single key and its associated value-list of records and outputs zero or more records, all of which may optionally remain associated with that same key.
* ReduceWrite
** This writes the outputs from the Reduce phase to the output datastore.

##### Map as streams/Tables
 We look more closely at how the table is being converted into a stream later, but for now, suffice it to say that the MapRead phase is iterating over the data at rest in the input table and putting them into motion in the form of a stream that is then consumed by the Map phase.

Next up, the Map phase consumes that stream, and then does what? Because The map operation is an element-wise transformation, it’s not doing anything that will halt the moving elements and put them to rest. It might change the effective cardinality of the stream by either filtering some elements out of exploding some elements into multiple elements, but those elements all remain independent from one another after the Map phase concludes. So, it seems safe to say that the Map phase both consumes a stream as well as produces a stream.

After the Map phase is done, we enter the MapWrite phase. As I noted earlier, the MapWrite groups records by key and then writes them in that format to persistent storage. The persistent part of the write actually isn't strictly necessary at this point as long as there’s persistence somewhere (i.e.,if the upstream inputs are saved and one can recompute the intermediate results from them in cases of failure, similar to the approach Spark takes withResilient Distributed Datasets [RDDs]). What is important is that the records are grouped together into some kind of datastore, be it in memory, on disk, or what have you. This is important because, as a result of this grouping operation, records that were previously flying past one-by-one in the stream are now brought to rest in a location dictated by their key, thus allowing per-key groups to accumulate as their like-keyed brethren and sistren arrive. Notehow similar this is to the definition of stream-to-table conversion provided earlier: the aggregation of a stream of updates over time yields a table. TheMapWrite phase, by virtue of grouping the stream of records by their keys,has put those data to rest and thus converted the stream back into a table.

We’ve gone from table to stream and back again across three operations.MapRead converted the table into a stream, which was then transformed into a new stream by Map (via the user’s code), which was then converted back into a table by MapWrite. We’re going to find that the next three operations in the MapReduce look very similar, so I’ll go through them more quickly,but I still want to point out one important detail along the way

##### Reduce as streams/tables

It’s basically identical to MapRead, except that the values being read are singleton lists of values instead of singleton values,because the data stored by MapWrite were key/value-list pairs. But it’s still just iterating over a snapshot of a table to convert it into a stream. Nothing New here.

ReduceWrite is the one that’s a bit noteworthy. We know already that thisphase must convert a stream to a table, given that Reduce produces a stream and the final output is a table. But how does that happen? If I told you it was a direct result of key-grouping the outputs from the previous phase into persistent storage, just like we saw with MapWrite, you might believe me,until you remembered that I noted earlier that key-association was an optional feature of the Reduce phase. With that feature enabled, ReduceWriteis essentially identical to MapWrite. But if that feature is disabled and the outputs from Reduce have no associated keys, what exactly is happening to bring those data to rest?

Taken from this perspective, it’s easy to see that stream/table theory isn't remotely at odds with batch processing of bounded data. In fact, it only further supports the idea I’ve been harping on that batch and streaming really aren't that different: at the end of the day, it’s streams and tables all the way down.

### What, Where, When, and How in a Streams andTables World

#### What: Transformations

* Map and Reduce both applied the pipeline author’s element-wise transformation on each key/value or key/value-list pair in the inputstream, respectively, yielding a new, transformed stream.
* MapWrite and ReduceWrite both grouped the outputs from the previous stage according to the key assigned by that stage (possibly implicitly, in the optional Reduce case), and in doing so transformed the input stream into an output table.

Viewed in that light, you can see that there are essentially two types of whattransforms from the perspective of stream/table theory:

* Non Grouping
** These operations (as we saw in Map and Reduce) simply accept a stream of records and produce a new, transformed stream of records on the other side. Examples of non grouping transformations are filters (e.g., removing spam messages), exploders (i.e., splitting apart a larger composite record into its constituent parts), and mutators (e.g., divide by 100), and so on
* Grouping
** These operations (as we saw in MapWrite and ReduceWrite) accept astream of records and group them together in some way, thereby transforming the stream into a table. Examples of grouping transformations are joins, aggregations, list/set accumulation, changelog application, histogram creation, machine learning model training, and so forth.

This is exactly the point being made by the folks championing stream processors as a database (primarily the Kafka and Flink crews): anywhere you have a grouping operation in your pipeline, you’re creating a table that includes what is effectively the output values of that portion of the stage. If Those output values happen to be the final thing your pipeline is calculating,you don’t need to dematerialize them somewhere else if you can read them directly out of that table. Besides providing quick and easy access to results as they evolve over time, this approach saves on compute resources by not requiring an additional sink stage in the pipeline to materialize the outputs,yields disk savings by eliminating redundant data storage, and obviates the need for any engineering work building the aforementioned sink stages. The Only major caveat is that you need to take care to ensure that only the data processing pipeline has the ability to make modifications to the table. If the values in the table can change out from under the pipeline due to external modification, all bets are off regarding consistency guarantees.

#### Where: Windowing


Combined with our earlier experiences, we can thus also infer it must play a role in stream-to-table conversion because grouping is what drives table creation. There are really two aspects of windowing that interact with stream/table theory:

* Window assignment
** This effectively just means placing a record into one or more windows.
* Window merging
** This is the logic that makes dynamic, data-driven types of windows, such as sessions, possible.

The effect of window assignment is quite straightforward. When a record is conceptually placed into a window, the definition of the window is essentially combined with the user-assigned key for that record to create an implicit composite key used at grouping time. Simple.

As you might expect, this looks remarkably similar to Figure 6-4, but with four groupings in the table (corresponding to the four windows occupied by the data) instead of just one. But as before, we must wait until the end of our bounded input is reached before emitting results. We look at how to address this for unbounded data in the next section, but first let’s touch briefly on merging windows.

##### Window merging
Moving on to merging, we’ll find that the effect of window merging is more complicated than window assignment, but still straightforward when you think about the logical operations that would need to happen. When grouping a stream into windows that can merge, that grouping operation has to take into account all of the windows that could possibly merge together.Typically, this is limited to windows whose data all have the same key(because we’ve already established that windowing modifies grouping to not be just by key, but also key and window). For this reason, the system doesn't really treat the key/window pair as a flat composite key, but rather as a hierarchical key, with the user-assigned key as the root, and the window a child component of that root. When it comes time to actually group data together, the system first groups by the root of the hierarchy (the key assigned by the user). After the data have been grouped by key, the system can then proceed with grouping by window within that key (using the child components of the hierarchical composite keys). This act of grouping by window is where window merging happens.

##### When: triggers
We learned in Chapter 3 that we use triggers to dictate when the contents of a window will be materialized (with watermarks providing a useful signal on input completeness for certain types of triggers). After data have been grouped together into a window, we use triggers to dictate when that data should be sent downstream. In streams/tables terminology, we understand that grouping means stream-to-table conversion. From there, it’s a relatively small leap to see that triggers are the complement to grouping; in other words, that “ungrouping” operation we were grasping for earlier. Triggers are what drive table-to-stream conversion.

##### How: Accumulation
in Chapter 2, we learned that the three accumulation modes (discarding,accumulating, accumulating and retracting) tell us how refinements of results relate when a window is triggered multiple times over the course of itslife. Fortunately, the relationship to streams and tables here is pretty straightforward:

* Discarding mode requires the system to either throw away the previous value for the window when triggering or keep around a copy of the previous value and compute the delta the next time the window triggers. (This mode might have better been called Deltamode.)
* Accumulating mode requires no additional work; the current value of the window in the table at triggering time is what is emitted.(This mode might have better been called Value mode.)
* Accumulating and retracting mode requires keeping around copies of all previously triggered (but not yet retracted) values for the window. This list of previous values can grow quite large in the case of merging windows live sessions, but is vital to cleanly reversing the effects of those previous trigger firings in cases where the newvalue cannot simply be used to overwrite a previous value. (Thismode might have better been called Value and Retractions mode.)

### General Theory of Stream and Table Relativity

* Data processing pipelines (both batch and streaming) consist of tables, streams, and operations upon those tables and streams.
* Tables are data at rest, and act as a container for data to accumulate and be observed over time.
* Streams are data in motion, and encode a discretized view of the evolution of a table over time.
* Operations act upon a stream or table and yield a new stream portable. They are categorized as follows:
* stream → stream: Nongrouping (element-wise) operations
* Applying non grouping operations to a stream alters the data in the stream while leaving them in motion, yielding a newstream with possibly different cardinality.
* stream → table: Grouping operations
* Grouping data within a stream brings those data to rest,yielding a table that evolves over time.
* Windowing incorporates the dimension of eventtime into such groupings
* Merging windows dynamically combine over time,allowing them to reshape themselves in response to the data observed and dictating that key remain the unit of atomicity/parallelization, with window being a child component of grouping within thatkey.
* table → stream: Ungrouping (triggering) operations
* Watermarks provide a notion of input completeness relative to event time, which is useful reference point when triggering event-time stamped data, particularly data grouped into event-time windows from unbounded streams.
    * The accumulation mode for the trigger determines the nature of the stream, dictating whether it contains deltas or values, and whether retractions for previous deltas/values are provided.
* table -> table:(none)
    * There are no operations that consume a table and yield table, because it’s not possible for data to go from rest and back to rest without being put into motion. As a result, all modifications to a table are via conversion to a stream and back again

What I love about these rules is that they just make sense. They have a very natural and intuitive feeling about them, and as a result they make it so much easier to understand how data flows (or don’t) through a sequence of operations. They codify the fact that data exist in one of two constitutions at any given time (streams or tables), and they provide simple rules for reasoning about the transitions between those states. They demystify windowing by showing how it’s just a slight modification of a thing everyone already innately understands: grouping. They highlight why grouping operations in general are always such a sticking point for streaming (because they bring data in streams to rest as tables) but also make it very clear what sorts of operations are needed to get things unstuck (triggers; i.e., ungrouping operations). And they underscore just how unified batch and stream processing really are, at a conceptual level.



