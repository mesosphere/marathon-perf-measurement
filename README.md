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