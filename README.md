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
Event-time windowing is what you use when you need to observe a datasource in finite chunks that reflect the times at which those events actually happened.
if these data hadbeen windowed into processing-time windows for a use case that cared aboutevent times, the calculated results would have been incorrect. As you would expect, event-time correctness is one nice thing about using event-time windows.

##### Drawbacks
* Buffering 
** Due to extended window lifetimes, more buffering of data is required.Thankfully, persistent storage is generally the cheapest of the resourcetypes most data processing systems depend on. As such, this problem is typicallymuch less of a concern than you might think when using any well-designed data processing system with strongly consistent persistent stateand a decent in-memory caching layer. Also, many useful aggregationsdo not require the entire input set to be buffered (e.g., sum or average),but instead can be performed incrementally, with a much smaller,intermediate aggregate stored in persistent state.
* Completeness
** Given that we often have no good way of knowing when we’ve seen allof the data for a given window, how do we know when the results for thewindow are ready to materialize? But for cases in which absolute correctness isparamount (again, think billing), the only real option is to provide a wayfor the pipeline builder to express when they want results for windows tobe materialized and how those results should be refined over time.

# Chapter 2. The What, Where,When, and Howof Data Processing
which covers in detail the core concepts of robust stream processingover out-of-order data, each analyzed within the context of aconcrete running example and with animated diagrams to highlightthe dimension of time.

we’re now going to look closely at three more:

* Triggers
** A trigger is a mechanism for declaring when the output for a windowshould be materialized relative to some external signal. Triggers provideflexibility in choosing when outputs should be emitted. In some sense,you can think of them as a flow control mechanism for dictating whenresults should be materialized. Another way of looking at it is thattriggers are like the shutter-release on a camera, allowing you to declarewhen to take a snapshots in time of the results being computed.
* Watermarks
** A watermark is a notion of input completeness with respect to eventtimes. A watermark with value of time X makes the statement: “all inputdata with event times less than X have been observed.” As such,watermarks act as a metric of progress when observing an unboundeddata source with no known end.
* Accumulation
** An accumulation mode specifies the relationship between multiple resultsthat are observed for the same window. Those results might becompletely disjointed; that is, representing independent deltas over time,or there might be overlap between them.

the structure of answering four questions, all of which I propose are critical toevery unbounded data processing problem:
* **What** results are calculated? This question is answered by the typesof transformations within the pipeline. This includes things likecomputing sums, building histograms, training machine learningmodels, and so on. It’s also essentially the question answered byclassic batch processing
* **Where** in event time are results calculated? This question isanswered by the use of event-time windowing within the pipeline.This includes the common examples of windowing from Chapter 1(fixed, sliding, and sessions); use cases that seem to have no notionof windowing (e.g., time-agnostic processing; classic batchprocessing also generally falls into this category); and other, morecomplex types of windowing, such as time-limited auctions. Alsonote that it can include processing-time windowing, as well, if youassign ingress times as event times for records as they arrive at the system.
* **When** in processing time are results materialized? This question isanswered by the use of triggers and (optionally) watermarks. Thereare infinite variations on this theme, but the most common patternsare those involving repeated updates (i.e., materialized viewsemantics), those that utilize a watermark to provide a single outputper window only after the corresponding input is believed to be complete.
* **How** do refinements of results relate? This question is answered bythe type of accumulation used: discarding (in which results are allindependent and distinct), accumulating (in which later results buildupon prior ones), or accumulating and retracting (in which both the accumulating value plus a retraction for the previously triggeredvalue(s) are emitted).

## Batch Foundations: What and Where
### What: Transformations
The transformations applied in classic batch processing answer the question:“What results are calculated?”

In the rest of this chapter (and indeed, through much of the book), we look ata single example: computing keyed integer sums over a simple datasetconsisting of nine values. Let’s imagine that we’ve written a team-basedmobile game and we want to build a pipeline that calculates team scores bysumming up the individual scores reported by users’ phones. If we were tocapture our nine example scores in a SQL table named “UserScores,” it mightlook something like this:

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
** The processing for the score; that is, the time at which the score wasobserved by the pipeline

Preceding each example is a short snippet of Apache Beam Java SDKpseudocode to make the definition of the pipeline more concrete.

* PCollections
** These represent datasets (possibly massive ones) across which parallel transformations can be performed (hence the “P” at the beginning of thename)
* PTransforms 
** These are applied to PCollections to create new PCollections.PTransforms may perform element-wise transformations, they maygroup/aggregate multiple elements together, or they may be a composite combination of other PTransforms.

For the purposes of our examples, we typically assume that we start out witha pre-loaded PCollection<KV<Team, Integer>> named “input” (that is, aPCollection composed of key/value pairs of Teams and Integers, wherethe Teams are just something like Strings representing team names, and theIntegers are scores from any individual on the corresponding team). In areal-world pipeline, we would’ve acquired input by reading in aPCollection<String> of raw data (e.g., log records) from an I/O source andthen transforming it into a PCollection<KV<Team, Integer>> by parsingthe log records into appropriate key/value pairs. For the sake of clarity in thisfirst example, I include pseudocode for all of those steps, but in subsequentexamples, I elide the I/O and parsing.Thus, for a pipeline that simply reads in data from an I/O source, parsesteam/score pairs, and calculates per-team sums of scores, we’d havesomething like that shown in Example 2-1.
 
```
  PCollection<String> raw = IO.read(...);
  PCollection<KV<Team, Integer>> input = raw.apply(new ParseFn());
  PCollection<KV<Team, Integer>> totals =  input.apply(Sum.integersPerKey());
```

Key/value data are read from an I/O source, with a Team (e.g., String of theteam name) as the key and an Integer (e.g., individual team member scores)as the value. The values for each key are then summed together to generateper-key sums (e.g., total team score) in the output collection.

As the pipeline observes values, it accumulates them in its intermediate stateand eventually materializes the aggregate results as output. State and outputare represented by rectangles (gray for state, blue for output), with theaggregate value near the top, and with the area covered by the rectanglerepresenting the portions of event time and processing time accumulated into
the result. For the pipeline in Example 2-1, it would look something like thatshown in Figure 2-3 when executed on a classic batch engine.

### Where: Windowing
windowing is the process of slicing up a data00:00 / 00:00
source along temporal boundaries. Common windowing strategies includefixed windows, sliding windows, and sessions windows, as demonstrated in Figure 2-4.

let’s take ourinteger summation pipeline and window it into fixed, two-minute windows.

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
**  These materialize a pane for a window only after the input for that window is believed to be complete to some threshold. This type of trigger is most analogous to what we’re familiar with in batch processing: only after the input is complete do we provide a result. The difference in thetrigger-based approach is that the notion of completeness is scoped to thecontext of a single window, rather than always being bound to thecompleteness of the entire input.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(Repeatedly(AfterCount(1))));  .apply(Sum.integersPerKey());
```
Figure 2-6

You can see how we now get multiple outputs (panes) for each window: onceper corresponding input. This sort of triggering pattern works well when theoutput stream is being written to some sort of table that you can simply pollfor results. Any time you look in the table, you’ll see the most up-to-datevalue for a given window, and those values will converge toward correctness over time.

One downside of per-record triggering is that it’s quite chatty. Whenprocessing large-scale data, aggregations like summation provide a niceopportunity to reduce the cardinality of the stream without losinginformation.

The nice sideeffect of using processing-time delays is that it has an equalizing effect acrosshigh-volume keys or windows: the resulting stream ends up being moreuniform cardinality-wise.

There are two different approaches to processing-time delays in triggers:aligned delays (where the delay slices up processing time into fixed regionsthat align across keys and windows) and unaligned delays (where the delay isrelative to the data observed within a given window). A pipeline withunaligned delays might look like Example 2-4, the results of which are shownin Figure 2-7.

```
PCollection<KV<Team, Integer>> totals = input  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(Repeatedly(AlignedDelay(TWO_MINUTES)))  .apply(Sum.integersPerKey());
```

This sort of aligned delay trigger is effectively what you get from amicrobatch streaming system like Spark Streaming. The nice thing about it ispredictability; you get regular updates across all modified windows at thesame time. That’s also the downside: all updates happen at once, whichresults in bursty workloads that often require greater peak provisioning toproperly handle the load. The alternative is to use an unaligned delay. Thatwould look something Example 2-5 in Beam. Figure 2-8 presents the results.

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(Repeatedly(UnalignedDelay(TWO_MINUTES))  
.apply(Sum.integersPerKey());
```

Contrasting the unaligned delays in Figure 2-8 to the aligned delays inFigure 2-6, it’s easy to see how the unaligned delays spread the load out moreevenly across time. The actual latencies involved for any given window differbetween the two, sometimes more and sometimes less, but in the end theaverage latency will remain essentially the same. From that perspective,unaligned delays are typically the better choice for large-scale processingbecause they result in a more even load distribution over time.

Repeated update triggers are great for use cases in which we simply wantperiodic updates to our results over time and are fine with those updatesconverging toward correctness with no clear indication of when correctness isachieved. However, as we discussed in Chapter 1, the vagaries of distributedsystems often lead to a varying level of skew between the time an eventhappens and the time it’s actually observed by your pipeline, which means itcan be difficult to reason about when your output presents an accurate andcomplete view of your input data. For cases in which input completenessmatters, it’s important to have some way of reasoning about completenessrather than blindly trusting the results calculated by whichever subset of datahappen to have found their way to your pipeline. Enter watermarks.

### When: Watermarks

Watermarks are a supporting aspect of the answer to the question: “When inprocessing time are results materialized?” Watermarks are temporal notionsof input completeness in the event-time domain. Worded differently, they arethe way the system measures progress and completeness relative to the eventtimes of the records being processed in a stream of events (either bounded orunbounded, though their usefulness is more apparent in the unbounded case).

Recall this diagram from Chapter 1, slightly modified in Figure 2-9, in whichI described the skew between event time and processing time as an ever-changing function of time for most real-world distributed data processingsystems.

That meandering red line that I claimed represented reality is essentially thewatermark; it captures the progress of event-time completeness as processingtime progresses. Conceptually, you can think of the watermark as a function,F(P) → E, which takes a point in processing time and returns a point in eventtime. That point in event time, E, is the point up to which the systembelieves all inputs with event times less than E have been observed. In otherwords, it’s an assertion that no more data with event times less than E will ever be seen again. Depending upon the type of watermark, perfect or heuristic, that assertion can be a strict guarantee or an educated guess,respectively:

* Perfect watermarks
** For the case in which we have perfect knowledge of all of the input data,it’s possible to construct a perfect watermark. In such a case, there is nosuch thing as late data; all data are early or on time.
* Heuristic watermarks
    ** For many distributed input sources, perfect knowledge of the input data isimpractical, in which case the next best option is to provide a heuristicwatermark. Heuristic watermarks use whatever information is availableabout the inputs (partitions, ordering within partitions if any, growth ratesof files, etc.) to provide an estimate of progress that is as accurate aspossible. In many cases, such watermarks can be remarkably accurate intheir predictions. Even so, the use of a heuristic watermark means that itmight sometimes be wrong, which will lead to late data

Because they provide a notion of completeness relative to our inputs,watermarks form the foundation for the second type of trigger mentionedpreviously: completeness triggers.

```
PCollection<KV<Team, Integer>> totals = input  
  .apply(Window.into(FixedWindows.of(TWO_MINUTES)).triggering(AfterWatermark()))  
  .apply(Sum.integersPerKey());
```

Figure 2-10

A great example of a missing-data use case is outer joins. Without a notion ofcompleteness like watermarks, how do you know when to give up and emit apartial join rather than continue to wait for that join to complete? You don’t.And basing that decision on a processing-time delay, which is the commonapproach in streaming systems that lack true watermark support, is not a safeway to go, because of the variable nature of event-time skew we spoke aboutin Chapter 1: as long as skew remains smaller than the chosen processing-time delay, your missing-data results will be correct, but any time skewgrows beyond that delay, they will suddenly become incorrect. From thisperspective, event-time watermarks are a critical piece of the puzzle for manyreal-world streaming use cases which must reason about a lack of data in theinput, such as outer joins, anomaly detection, and so on.

Now, with that said, these watermark examples also highlight twoshortcomings of watermarks (and any other notion of completeness),specifically that they can be one of the following:

* Too slow
** When a watermark of any type is correctly delayed due to knownunprocessed data (e.g., slowly growing input logs due to networkbandwidth constraints), that translates directly into delays in output ifadvancement of the watermark is the only thing you depend on forstimulating results.
*  Too fast
** When a heuristic watermark is incorrectly advanced earlier than it shouldbe, it’s possible for data with event times before the watermark to arrivesome time later, creating late data.

You simply cannot get both low latency and correctness out of asystem that relies solely on notions of completeness. So, for cases for whichyou do want the best of both worlds, what’s a person to do? Well, if repeatedupdate triggers provide low-latency updates but no way to reason aboutcompleteness, and watermarks provide a notion of completeness but variableand possible high latency, why not combine their powers together?

### When: Early/ On-Time / Late Triggers 

We’ve now looked at the two main types of triggers: repeated update triggersand completeness/watermark triggers. In many case, neither of them alone issufficient, but the combination of them together is. Beam recognizes this fact by providing an extension of the standard watermark trigger that alsosupports repeated update triggering on either side of the watermark. This isknown as the early/on-time/late trigger because it partitions the panes that arematerialized by the compound trigger into three categories:

* Zero or more early panes, which are the result of a repeated updatetrigger that periodically fires up until the watermark passes the endof the window. The panes generated by these firings containspeculative results, but allow us to observe the evolution of thewindow over time as new input data arrive. This compensates for theshortcoming of watermarks sometimes being too slow.
* A single on-time pane, which is the result of thecompleteness/watermark trigger firing after the watermark passes theend of the window. This firing is special because it provides anassertion that the system now believes the input for this window tobe complete. This means that it is now safe to reason about missingdata; for example, to emit a partial join when performing an outerjoin.
* Zero or more late panes, which are the result of another (possiblydifferent) repeated update trigger that periodically fires any time latedata arrive after the watermark has passed the end of the window. Inthe case of a perfect watermark, there will always be zero late panes.But in the case of a heuristic watermark, any data the watermarkfailed to properly account for will result in a late firing. Thiscompensates for the shortcoming of watermarks being too fast.

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(AfterWatermark()
.withEarlyFirings(AlignedDelay(ONE_MINUTE))
.withLateFirings(AfterCount(1))))  
.apply(Sum.integersPerKey());
```

This version has two clear improvements over Figure 2-9.

* For the “watermarks too slow” case in the second window, [12:02,12:04): we now provide periodic early updates once per minute. Thedifference is most stark in the perfect watermark case, for whichtime-to-first-output is reduced from almost seven minutes down tothree and a half; but it’s also clearly improved in the heuristic case,as well. Both versions now provide steady refinements over time(panes with values 7, 10, then 18), with relatively minimal latencybetween the input becoming complete and materialization of thefinal output pane for the window.

* For the “heuristic watermarks too fast” case in the first window,00:00 / 00:00
[12:00, 12:02): when the value of 9 shows up late, we immediatelyincorporate it into a new, corrected pane with value of 14.

One interesting side effect of these new triggers is that they effectivelynormalize the output pattern between the perfect and heuristic watermarkversions. Whereas the two versions in Figure 2-10 were starkly different, thetwo versions here look quite similar. They also look much more similar to thevarious repeated update version from Figures 2-6 through 2-8, with oneimportant difference: thanks to the use of the watermark trigger, we can alsoreason about input completeness in the results we generate with the early/on-time/late trigger. This allows us to better handle use cases that care aboutmissing data, like outer joins, anomaly detection, and so on.

The biggest remaining difference between the perfect and heuristic early/on-time/late versions at this point is window lifetime bounds. In the perfectwatermark case, we know we’ll never see any more data for a window afterthe watermark has passed the end of it, hence we can drop all of our state forthe window at that time. In the heuristic watermark case, we still need to holdon to the state for a window for some amount of time to account for late data.But as of yet, our system doesn’t have any good way of knowing just howlong state needs to be kept around for each window. That’s where allowedlateness comes in.

### When: Allowed Lateness(i.e., Garbage Collection)
the persistent state for each windowlingers around for the entire lifetime of the example; this is necessary toallow us to appropriately deal with late data when/if they arrive. But while itwould be great to be able to keep around all of our persistent state until theend of time, in reality, when dealing with an unbounded data source, it’soften not practical to keep state (including metadata) for a given windowindefinitely; we’ll eventually run out of disk space (or at the very least tire of paying for it, as the value for older data diminishes over time).As a result, any real-world out-of-order processing system needs to providesome way to bound the lifetimes of the windows it’s processing. A clean andconcise way of doing this is by defining a horizon on the allowed latenesswithin the system; that is, placing a bound on how late any given record maybe (relative to the watermark) for the system to bother processing it; any datathat arrives after this horizon are simply dropped. After you’ve bounded howlate individual data may be, you’ve also established precisely how long thestate for windows must be kept around: until the watermark exceeds thelateness horizon for the end of the window. But in addition, you’ve also giventhe system the liberty to immediately drop any data later than the horizon assoon as they’re observed, which means the system doesn’t waste resourcesprocessing data that no one cares about.

Because the interaction between allowed lateness and the watermark is a littlesubtle, it’s worth looking at an example. Let’s take the heuristic watermarkpipeline from Example 2-7/Figure 2-11 and add in Example 2-8 a latenesshorizon of one minute (note that this particular horizon has been chosenstrictly because it fits nicely into the diagram; for real-world use cases, alarger horizon would likely be much more practical):

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

* To be absolutely clear, if you happen to be consuming data fromsources for which perfect watermarks are available, there’s no needto deal with late data, and an allowed lateness horizon of zeroseconds will be optimal. This is what we saw in the perfectwatermark portion of Figure 2-10
* One noteworthy exception to the rule of needing to specify latenesshorizons, even when heuristic watermarks are in use, would besomething like computing global aggregates over all time for atractably finite number of keys (e.g., computing the total number of00:00 / 00:00
visits to your site over all time, grouped by web browser family). Inthis case, the number of active windows in the system is bounded bythe limited keyspace in use. As long as the number of keys remainsmanageably low, there’s no need to worry about limiting the lifetimeof windows via allowed lateness.

### How: Accumulation

When triggers are used to produce multiple panes for a single window overtime, we find ourselves confronted with the last question: “How dorefinements of results relate?” In the examples we’ve seen so far, eachsuccessive pane is built upon the one immediately preceding it. However,there are actually three different modes of accumulation:

* Discarding
** Every time a pane is materialized, any stored state is discarded. Thismeans that each successive pane is independent from any that camebefore. Discarding mode is useful when the downstream consumer isperforming some sort of accumulation itself; for example, when sendingintegers into a system that expects to receive deltas that it will sumtogether to produce a final count.

* Accumulating
** As in Figures 2-6 through 2-11, every time a pane is materialized, anystored state is retained, and future inputs are accumulated into the existingstate. This means that each successive pane builds upon the previouspanes. Accumulating mode is useful when later results can simplyoverwrite previous results, such as when storing output in a key/valuestore like HBase or Bigtable.

* Accumulating and retracting
** This is like accumulating mode, but when producing a new pane, it alsoproduces independent retractions for the previous pane(s). Retractions (combined with the new accumulated result) are essentially an explicitway of saying “I previously told you the result was X, but I was wrong.Get rid of the X I told you last time, and replace it with Y.” There are twocases for which retractions are particularly helpful:

When consumers downstream are regrouping data by a differentdimension, it’s entirely possible the new value may end up keyeddifferently from the previous value and thus end up in a differentgroup. In that case, the new value can’t just overwrite the old value;you instead need the retraction to remove the old value
When dynamic windows (e.g., sessions, which we look at moreclosely in a few moments) are in use, the new value might bereplacing more than one previous window, due to window merging.In this case, it can be difficult to determine from the new windowalone which old windows are being replaced. Having explicitretractions for the old windows makes the task straightforward. Wesee an example of this in detail in Chapter 8

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

  
