# 1
Hello and welcome! My name is Aleksander Lisiecki and I work at Erlang Solutions, as an Erlang and Elixir consultant. On the first slide you can see how to find me on social media.

# 2
In this presentation, I am going to talk about performance of computer programs:
- How to measure it?
- How to identify issues?
- And, how to approach solving them?

# 3
But, first thing first, we need to somehow, intuitively understand what performance is.
Try thinking about it as an ability of a system to handle certain load.
For example, there are only two people visiting my hypothetical blog, me and my mum, my server can handle 3 users so it performs well. Assuming that there is more then one person watching this talk, if you all suddenly open this, not existing, blog you would notice that, for some of you, this website just would not work. This is because you would exceed capacity of the server that host this blog.

# 4
Before we dive, head first into the main subject, I have 2 disclaimers. First one is that I am not sponsored by any of the companies to promote their products. I share with you, what works for me.

# 5
And the second disclaimer is, that I try to keep my presentation mostly technology-agnostic, that means that no matter if you code in Python, Java, Scala, Erlang or any other programming language, I hope this talk will be of some use for you. This also means I will not discuss technology-specific options that are there, like language specific profilers, compiler options etc.

# 6
I hope most, if not all of you recognize this meme, which is also one of the most important ideas of mechanical engineering. Now let's modify that slightly to fit load testing purpose.

# 7
This is the version that we do not disserve, but we need.

# 8
Conveniently, this is also an agenda of this talk. In a TLDR ot "too long didn't read" summary, we are going to talk how we can monitor our system, and then fix whatever does not meet the expectations performance-wise.

# 9
Before we jump into optimizing every single peace of code, it is highly recommended, to define a measurable and clear goal that we want to achieve, otherwise, we may easily end up working on something that already meets, or even exceeds the expectations. For example it makes no practical sense to improve loading time of a single component from current 20 milliseconds to 10 milliseconds if all other components take about 100 milliseconds to load. Moreover, notice that all of those loading times are literally shorter then blink of an eye of an average person. So there is no point is such optimization.

# 10
To determine if a service performs well enough, there is a need of a possibility to look into its internals. To achieve that, you need to add metrics reporting to your system. Metric is a measurement of some kind within your system. It can be query execution time, CPU utilization, connected users count and so on...

# 11
There are purpose build databases created for collecting and storing metrics. They can be divided into 2 main categories, based on which side initializes metrics data transfer. The pushing once, are the databases that are waiting for the metrics update to be pushed to them. You can think of them like a simple HTTP server, which receives requests with updated version of metrics. Some example databases that work this way are Graphene and InfluxDB.
The pulling databases are periodically scheduling scraping the metrics from your services. Think of them as HTTP client getting the metrics data from your service by sending a get request every 15 seconds. An example of such database is Prometheus.

# 12
Both of those approaches have their pluses and minuses. The main advantage of pushing databases is that they can store actual data points. However this may also lead to accidental DoS (Denali of Service) on such database. On the other hand pulling provides back pressure, as in this case database determines it's own working paste. However the data needs to be accumulated for each period on the service side, this means that instead of actual measurements it is much more common to do for example bucketing of the data. Bucketing is dividing measured metric into ranges, aka buckets and counting how many elements are in each bucket. For example, let's assume that we measure some query's execution time, we define 4 buckets for it:
 first one is for all the execution times that took less then 10 milliseconds,
 second one is for those between 10 and 50 milliseconds,
 third one is for those between 50 and 100 milliseconds,
 and fourth one is for those that took more then 100 milliseconds.
 Now every time the query is executed, time it and add 1 to the respective bucket.

# 13
Ok, "add metrics", easier said then done. How to determine what to measure? I would start with OS parameters like CUP, RAM, etc and then add metric that measures total execution time for each of the request types that you get.


# 14
Collecting raw data is only part of success, as it is equally important to present it in a clear and understandable way. For that, I would recommend Grafana, which does great job in drawing all kinds of graphs and plots.

 # 15
Having the metrics plugged in, now is the time to see how your system behaviors under the load. I obviously encourage you to observe production environment, but not necessarily run your tests there, unless you work in mBank, then you do it already anyways. For running the load test, prepare a separate test environment, and add load to it by implementing clients simulator, which will emulate whatever your clients do.

# 16
For the client's emulation I would recommend using Erlang or Elixir. This is because, each client can be emulated by a separate process. Since these processes are running on VM level and not OS level, they are extremely lightweight and it is almost effortless to start and potentially run literally millions of them in parallel or at least concurrently. If you want to know more about load testing there is a talk by my college Nelson Vides from FOSDEM 2023 titled "The actor model as a load testing framework".

# 17
Assuming that you have established your performance goals, implemented and ran load test or even multiple tests, there are two options. First option is that it works well enough then your job is done here, but if system does not work fast enough what do you do? For the technology of your choice find a library that implements collecting flame graph data and plug it to the most used execution paths in your program.

# 18
Here is a sample flame graph, it allows to identify the functions that take the longest to execute in no time. Start your investigation with parts of code that take comparatively the longest time to execute.

# 19
When you introduce some potential improvements to your code, you might either see significant improvement or not see it at all. If you do not see sufficient improvement at first glance, does it mean that there is no improvement at all? Well, not necessarily. If you struggle to tell, if there is improvement or not, use T-test or Wilcoxon test, both for two unpaired samples to determine that. I will not dive into maths behind it, but I recommend using either python or R studio to do the maths for you. Just remember that there are some assumptions to be met, for the method to give correct results.

# 20
There is horizontal and vertical scaling. Remember that there are some extra factors, eg cost of such solution, that need to be taken into account when picking the ways of potential improvement.

# 21
Given a choice of Fast, Cheap, or Right, you can always pick up to two of those.

# 22
There are some low hanging fruits when trying to improve performance. 
Vertical scaling is one of them. It is just a matter fo running your program on machine with more resources. Faster CUP, more RAM and voila!

# 23
Horizontal scaling in much harder to explain and implement in general case, but the rule of thumb is, to replace a simple instance of something with multiple once. For example instead of single postgeSQL use cockroachDB which do the horizontal scaling for you.

# 24
Cache is component that stores data so future requests for that data can be served faster. The data stored in a cache might be the result of an earlier computation, or the duplicate of data stored elsewhere. So, whenever same result is needed, one can directly use the value which is already computed and stored somewhere, so that no need to do further computation of same thing.

# 25
Another good idea is to upgrading all components of your system to the latest version. From my own field just upgrading Erlang OTP version, recompiling the same code to the new VM and running it there usually brings performance improvements.
For example in 2020 the OTP team introduced JIT Just in time compiler, which makes wonders performance wise without any need of changes in your code!

# 26
Sometimes there is just no way around execution time and a user just needs to wait. If a user needs to wait for something, make sure that they are under the impression that the work is happening and it is not just that the program is hanging. You probably know it as a waiting circle, or some kind of loading bar. 

# 27
As a developer you do not always have influence on what the requirement are and how the final product will look like, but sometimes you do have a saying in it. I would like to inspire you by gaming industry which is known for all sorts of creative solutions and workarounds. I do not remember the game title, bu I recall that in one of more popular games, riding on a horse was implemented as wearing a hat. In other words, a horse was considered a special kind of hat. Which is both genius and madness in the same time.

# 28
Sometimes we need to ask if something is really needed, or can it dropped completely or somehow modified to have smaller impact? For example, let's think of profile photos on Facebook, Instagram, Linkedin or Twitter. Do you need them to be in Full HD or 4k or os 400 by 400 pixels sufficient enough? As they are always displayed small anyways?

# 29
And last but not least if everything fails it remains nothing but to find a new job, where you can start your performance crusade from square one.

# 30
Thank you for your attention!