# Marathon performance test setup

This small project should visualize the performance measurements between the marathon versions 1.3.12, 1.4.2 and current 1.5-SNAPSHOT.

# Test setup
For this testing setup we use the following setup:

We do have 800 applications with each one running instance. We do update N random apps every 30 seconds and measure the response time of `/v2/apps` every second over the entire test period. This is shown in the `response time` graph.

In the meantime we attach to the running marathon process with a profiler and publish the `memory usage`, `cpu usage`, `thread count` and `memory footprint` graph.

