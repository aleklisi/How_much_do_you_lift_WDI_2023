class: middle, center

# How much do you lift? - 101 of load testing.

Aleksander Lisiecki

.right[AlekLisiecki @ Twitter]
.right[aleklisi @ GitHub]
.right[aleksander.lisiecki@erlang-solutions.com]

---
# How much do you lift?

.scale80[![meme](images/spanchbob_lift_meme.png)]

---
# What is performance?

Performance is an ability of a system to work under certain load.

---
# Disclaimer 1

.scale60[![meme](images/works_on_my_computer_meme.jpeg)]

---
# Disclaimer 2

.scale60[![meme](images/deep_water_meme.jpeg)]

---
# Do I have a performance problem?

.scale80[![meme](images/does_it_move_meme.jpeg)]

---
# Do I have a performance problem?

.scale80[![meme](images/decision_tree_1.png)]

---
# Agenda

.scale80[![meme](images/Agenda.png)]

---
# ...well **enough**

.scale80[![meme](images/good_enough_meme.jpeg)]

---
# How to determine of a service is good **enough**?

Monitoring - observe and check the progress or quality of (something) over a period of time; keep under systematic review.

Metric - a system or standard of measurement.

---
# Metric reporting

Pushing:
 - Graphene
 - InfluxDB

Pulling:
 - Prometheus

---
# Pulling vs pushing

.scale80[![meme](images/pull_push_image.jpeg)]

---
# Data visualization - Grafana

.scale80[![meme](images/grafana.jpeg)]

---
# Load testing

.scale80[![meme](images/bridge.webp)]

---

.scale80[![meme](images/erlang_stronger_meme.jpeg)]

---
# Where to improve?
.scale60[![meme](images/dog_search.webp)]

---
# Flame graph
.scale100[![meme](images/sample_flame_graph.svg)]

---
# How to compare not obvious metics samples?

- Unpaired two-samples t-test (compare the mean of two independent groups).*
- Unpaired two-samples Wilcoxon test (used when your data are not normally distributed).

*Note that, unpaired two-samples t-test can be used only under certain conditions:
 - when the two groups of samples (A and B), being compared, are normally distributed. This can be checked using Shapiro-Wilk test.
 - and when the variances of the two groups are equal. This can be checked using F-test.

---
# Scaling application

- Horizontal
- Vertical

.scale100[![meme](images/horizosntal_vs_verical-scaling.png)]

---
# Vertical scaling
.scale100[![meme](images/bigger_computer.jpeg)]

---
# Horizontal scaling
.scale100[![meme](images/dogscience.png)]

---
# Cache
.scale100[![meme](images/cache.png)]

---
class: middle, center
# System components upgrade

---
# If everything else fails,...
.scale100[![meme](images/open-candidates-blog-share.jpg)]

---
class: middle, center
# Thank you for your attention!
