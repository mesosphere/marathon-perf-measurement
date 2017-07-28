# Marathon performance test setup

This small project should visualize the performance measurements between the marathon versions 1.1, 1.3, 1.4 and current 1.5-SNAPSHOT.

# Test setup
For this testing setup we use the following setup:

We do have 100 applications with each one running instance. We do update 10 random apps every 30 seconds and measure the response time of `/v2/apps` every second.
This is shown in the `response time` graph.
