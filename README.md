# Marathon performance test setup

This small project should visualize the performance measurements between the Marathon versions 1.3.12, 1.4.2 and current 1.5-SNAPSHOT.

# Test setup
The test was run on a Intel NUC skull canyon with a `Intel i7-6770HQ-2` CPU with SSD and 32GB of memory running ubuntu.

The Mesos setup contained 1 Mesos master and 5 Mesos agents, as well as 1 Marathon instance. Marathon had 6GB of heap reserved.

## Packaging
Marathon was packaged as jar/zip before running the test to be independent from sbt. For marathon 1.5 `sbt universal:packageZipTarball` was used and for marathon 1.4 and 1.3 `sbt assembly` was used. You can find the 

## Starting Marathon
Marathon 1.5 was started with `./marathon -J-Xmx6G -J-Xms6G --disable_store_cache --master 127.0.0.1:5050`, 1.4 with `java -Xmx6G -Xms6G -jar marathon-assembly-1.4.2.jar --master 127.0.0.1:5050` and 1.3 with `java -Xmx6G -Xms6G -jar marathon-assembly-1.3.12.jar --master 127.0.0.1:5050`.

## Starting Mesos
The Mesos cluster was started with the commands below:

```
alias mmaster="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-master.sh --ip=127.0.0.1 --work_dir=/home/junterstein/runtimes/mesos/master --acls=file:///home/junterstein/runtimes/mesos/acls --credentials=file:///home/junterstein/runtimes/mesos/credentials"
alias mslave="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-agent.sh --runtime_dir=/home/junterstein/runtimes/mesos/agent1-runtimedir --master=127.0.0.1:5050 --port=5051 --work_dir=/home/junterstein/runtimes/mesos/agent1"
alias mslave2="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-agent.sh --runtime_dir=/home/junterstein/runtimes/mesos/agent2-runtimedir --master=127.0.0.1:5050 --port=5052 --work_dir=/home/junterstein/runtimes/mesos/agent2"
alias mslave3="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-agent.sh --runtime_dir=/home/junterstein/runtimes/mesos/agent3-runtimedir --master=127.0.0.1:5050 --port=5053 --work_dir=/home/junterstein/runtimes/mesos/agent3"
alias mslave4="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-agent.sh --runtime_dir=/home/junterstein/runtimes/mesos/agent4-runtimedir --master=127.0.0.1:5050 --port=5054 --work_dir=/home/junterstein/runtimes/mesos/agent4"
alias mslave5="sudo /home/junterstein/workspaces/mesos/build/bin/mesos-agent.sh --runtime_dir=/home/junterstein/runtimes/mesos/agent5-runtimedir --master=127.0.0.1:5050 --port=5055 --work_dir=/home/junterstein/runtimes/mesos/agent5"
```

## Restart between all tests
Between all test runs, marathon and Mesos was restarted and zookeeper data deleted.


# Test scenario
For this testing setup we use the following setup:

We do have 800 applications with each one running instance. We do update N random apps every 30 seconds and measure the response time of `/v2/apps` every second over the entire test period. This is shown in the `response time` graph.

In the meantime we attach to the running Marathon process with a profiler and publish the `memory usage`, `cpu usage`, `thread count` and `memory footprint` graph.


# Marathon master (1.5-SNAPSHOT) of 9/6/17

## Observations
The first test run was with 100 random applications which were restarted every 30 seconds. In this scenario Marathon was able to serve all deployments for around 10 minutes and then marathon started to respond with `HTTP response 500: {“message”:“GroupManager queue may not exceed 500 entries”}`, which indicates that Marathon gets too many new deployments and finished too few at the same time. Stopping the test which triggers new deployments leads to the state, that Marathon recovers and was able to operate normally after around 2 minutes. Therefore the test below updates 30 random application every 30 seconds.

## Deployment durations
### 800 applications with 1 instance each
- 62s to deploy 800 sleepy tasks
- 65s to deploy 800 sleepy tasks

### 500 applications with 1 instance each
- 34s to deploy 500 sleepy tasks
- 32s to deploy 500 sleepy tasks

## Graphs
### Profiler
![Master Profiler](master.png)

### Memory sampler
![Master Memory Profiler](master-sampler.png)

### Response times of v2/apps in the meantime
![Master Memory Profiler](master-v2.png)

# Marathon 1.4.2

## Observations
The first test run was with 30 random applications which were restarted every 30 seconds. In this scenario Marathon was able to serve all deployments for around 3 to 5 minutes and then marathon started to respond with `HTTP response 409: {“message”:“App is locked by one or more deployments. ...”}` and/or `Futures timed out after [10000 milliseconds]` , which indicates that Marathon gets too many new deployments and finished too few at the same time. Stopping the test which triggers new deployments leads to the state, that Marathon recovers and was able to operate normally after around 3 minutes. In one run Marathon was not able to recover and responeded persistently with `HTTP 500: Futures timed out after [10000 milliseconds]`, compare [MARATHON-7400](https://jira.mesosphere.com/browse/MARATHON-7400).

## Deployment durations
### 800 applications with 1 instance each
- 62s to deploy 800 sleepy tasks
- 65s to deploy 800 sleepy tasks

### 500 applications with 1 instance each
- 34s to deploy 500 sleepy tasks
- 32s to deploy 500 sleepy tasks

## Graphs
### Profiler
![Master Profiler](14.png)

### Memory sampler
![Master Memory Profiler](14-sampler.png)

### Response times of v2/apps in the meantime
![Master Memory Profiler](14-v2.png)