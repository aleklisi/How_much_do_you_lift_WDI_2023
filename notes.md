# 1
Hello and welcome! My name is Aleksander Lisiecki and I work as Erlang and Elixir consultant at Erlang Solutions.

# 2
In this presentation, I am going to talk about performance of computer programs:
- How to measure it?
- How to identify issues?
- And, how to approach solving them?

# 3
First thing first, we need to somehow intuitively understand what performance is.
Try thinking about it as an ability of a system to handle certain load.
For example, there only two people visiting my hypothetical blog, me and my mum, my server can handle 3 users so it performs well.

# 4
Before we dive, head first into the main subject, I have 2 disclaimers. First one is that I am not sponsored by any of the companies to promote their products. I share with you what works for me.

# 5
And the second disclaimer is that, I try to keep my presentation technology agnostic, that is no matter if you code in Python, Java, Scala, Erlang or any other programming language, I hope this talk will be of some use for you. This also means I will not discuss technology-specific options that are there, like language specific profilers, compiler options etc.

# 6
I hope most, if not all of you recognize this meme, which is also one of the most important ideas of mechanical engineering. Now let's modify that slightly to fit load testing purpose.

# 7
This is the version that we do not disserve, but we need.

# 8
Conveniently, this is also an agenda of this talk. In a too long didn't read summary, we are going to talk how we can monitor our system, and then fix whatever does not meet the expectations performance-wise.

# 9
Before we jump into optimizing every single peace of code, it is highly recommended, to define a measurable and clear goal that we want to achieve, otherwise, we may easily end up working on something that already meets , or even exceeds the expectations. For example it makes no practical sense to improve loading time of a single component from current 20 milliseconds to 10 milliseconds if all other components take about 100 milliseconds to load. Moreover, notice that all of those loading times are literally shorter then blink of an eye of an average person.

# 10
To determine if a service performs well enough there is a need of a possibility to look into this service. To achieve that, you need to add metrics reporting to your system. Metric is a measurement of some kind within your system. It can be query execution time, CPU utilization, connected users count and so on...

# 11
There are purpose build databases created for storing metrics. They can be divided into 2 main categories, based on which side initializes metrics data transfer. The pushing once are the databases that are waiting for the metrics update to be pushed to them. You can think of them like a simple HTTP server, which receives request with updated version of metric. Some example databases that work this way are Graphene and InfluxDB.
The pulling databases are periodically scheduling scraping the metrics from your services. Think of them as HTTP client getting the metrics data from your service by sending a get request every 15 seconds. An example of such database is Prometheus.

# 12
Both of those approaches have their pluses and minuses. The main advantage of pushing databases is that they can store actual data points. However this may also lead to accidental DoS (Denali of Service) on such database. On the other hand pulling provides back pressure, as it determines it's own working paste. However the data needs to be accumulated for each period on the service side, this means that instead of actual measurements it is much more common to do bucketing of the data. Bucketing is dividing measured metric into ranges, aka buckets and counting how many elements are in each bucket. For example, let's assume that we measure some query's execution time, we define 4 buckets:
 less then 10 milliseconds, between 10 and 50 milliseconds, between 50 and 100 milliseconds, and more then 100 milliseconds. Now every time the query is executed, time it and add 1 to the respective bucket.

# 13
Collecting raw data is only part of success, as it is equally important to present it in a clear and understandable way. For that I would recommend Grafana, which does great job in drawing all kinds of graphs and plots.

 # 14
Having the metrics plugged in, no is the time to see how your system behaviors under the load. I obviously encourage you to observe production, but not necessarily test the production system, unless you work in mBank, then you do it already anyways. For running the load test, prepare a separate test environment, and add load to it by implementing clients simulator which will emulate whatever your clients do.

# 15
For the client's emulation I would recommend using Erlang or Elixir. This is because, each client can be emulated by a separate process. Since these processes are running on VM level and not OS level, they are extremely lightweight and it is almost effortless to start and potentially run literally millions of them in parallel or at least concurrently.

# 16
Assuming that you have established your performance goals, implemented and ran load test or even multiple tests, there are two options. First option is that it works well enough then your job is done here, but if system does not work fast enough what do you do? For the technology of your choice find a library that implements collecting flame graph data and plug it to the most used execution paths in your program.

# 17
Here is a sample flame graph, it allows to identify the functions that take the longest to execute in no time. Start your investigation with parts of code that take comparatively the longest time to execute.

# 18
When you introduce some potential improvements to your code, you might either see significant improvement or not see it. If you do not see sufficient improvement at first glance, does it mean that there is no improvement at all? Well, not necessarily. If you struggle to tell, if there is improvement or not, use T-test or Wilcoxon test, both for two unpaired samples to determine that. I will not dive into maths behind it, but I recommend using either python or R studio to do the maths for you. Just remember that there are some assumptions to be met, for the method to give correct results.

# 19
There are some low hanging fruits when trying to improve performance. There is horizontal and vertical scaling. Slide also shows that in the long run horizontal scaling should be cheaper.

# 20
Vertical scaling is the simpler one. It is just a matter fo running your program on machine with more resources. Faster CUP, more RAM and voila!

# 21
Horizontal scaling in much harder to explain and implement in general case, but the general rule is to replace a simple instance of something with multiple once. Eg instead of single postgeSQL use cockroachDB which do the horizontal scaling for you.

# 22
Cache is component that stores data so future requests for that data can be served faster. The data stored in a cache might be the result of an earlier computation, or the duplicate of data stored elsewhere. So, whenever same result is needed, one can directly use the value which is already computed and stored somewhere, so that no need to do further computation of same thing.

# 23
Upgrading all components to the latest version. From my own field just upgrading Erlang OTP version, recompiling the same code to the new VM and tunning it there usually brings performance improvements.
Eg in 2020 the OTP tea introduced JIT Just in time compiler. 

# 24
And last but not least if everything fails it remains nothing but to find a new job.

# 25
Thank you for your attention!