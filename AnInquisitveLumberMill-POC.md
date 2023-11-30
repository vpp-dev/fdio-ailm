# GenAI Hackathon 2023 : An Inquisitive Lumber Mill

The team’s goal is to explore the use of GenAI LLM models to consume FD.io Jenkins Job log files and produce a daily report summarizing job failures. Ideally this will provide clarity on testcase failure analysis by reducing the amount of noise during the failure analysis of job failures in the FD.io CI pipeline.

A stretch goal is to add decoded pcap files for failed testcases to the LLM for additional context in failure analysis by the GenAI queries.

Example job files can be found at
  - https://jenkins.fd.io/view/vpp/job/vpp-debug-verify-master-ubuntu2204-x86_64/
  - https://jenkins.fd.io/view/vpp/job/vpp-cov-verify-master-ubuntu2004-x86_64/

# Development Plan

1.	Pick the LLM model to use
2.	Determine how to feed it the Jenkins Job log files stored in s3-logs.fd.io
- Training may require annotating the log file data
- Investigate automating daily ingestion of new log files
3.	Experiment on interactively generating queries to provide useful information
4.	Identify how to create/process useful queries programmatically
5.	Create a daily job that queries the LLM model and produces a job failure summary report

# Day 1 Proof-Of-Concept

This repo was cloned and the [03_QuestionAnswering/00_qa_w_bedrock_titan](./03_QuestionAnswering/00_qa_w_bedrock_titan.ipynb) notebook was cloned and modified to provide a portion of a failed log file as context and sample questions formulated to evaluate the quality of the answers produced.

The modified notebook [03_QuestionAnswering/00_csit_qa_w_bedrock_titan.ipynb](./03_QuestionAnswering/00_csit_qa_w_bedrock_titan.ipynb) successfully demonstrated useful query results

## Day 1 RESULTS
### Question 1 : "What test failed?"
test_flowprobe.py::test_resolve_arp

### Question 2 : "What caused the test failure?"
Timeout - capture file still nowhere

### Question 3 : "How can I get the failed test to pass?"
The test is timing out waiting for a packet to be captured on port pg1.

One way to fix this is to increase the timeout value. You can do this by adding the following line
to your test case:

``` Python
self.timeout = 60
```

This will increase the timeout value to 60 seconds.

Another way to fix this is to disable packet capturing on port pg1. You can do this by adding the
following line to your test case:

``` Python
self.pg1.disable_capture()
```

This will disable packet capturing on port pg1, and the test should proceed without timing out.

### Question 4 : "How can I get the failed test to pass without increasing the timeout?"
The timeout is set to 1 second, which is too short for the test to complete. You can increase the
timeout to a longer value, such as 10 seconds, to allow the test to complete.

You can also check if the ARP request is being received on the port pg1. If the ARP request is not
being received, you can try increasing the timeout or checking the network configuration.

Another option is to use a packet generator to send ARP requests to the interface. This can help to
ensure that the ARP request is being received and processed by VPP.

Here's an example of how you can use a packet generator to send ARP requests to the interface:

``` Python
import time
import vpp

# Create a packet generator
pg = vpp.packet_generator()

# Enable packet capture on the interface
pg.enable_capture(pg1)

# Send ARP requests to the interface
for i in range(10):
    pg.send(pg1, '02:01:00:00:ff:02', '172.16.2.1')
    time.sleep(1)

# Disable packet capture on the interface
pg.disable_capture(pg1)

# Clear the trace
pg.clear_trace()

# Disable packet generator
pg.disable()

```

This will send 10 ARP requests to the interface and then disable packet capture. You can then clear
the trace and disable the packet generator.

By increasing the timeout or using a packet generator, you should be able to get the test to pass
without increasing the timeout.

### Question 5 : "What pcap file was used for the failed test?"
/tmp/vpp-unittest-Flowprobe/pg1_out.pcap

### Question 6 : "Are there any issues with the pcap file?"
No ARP received on port pg1

### Question 7 : "What VPP command line args were used?"
['/w/workspace/vpp-debug-verify-master-ubuntu2204-x86_64/build-root/install-vpp_debug-
native/vpp/bin/vpp', 'unix', '{', 'nodaemon', '', 'full-coredump', 'coredump-size unlimited',
'runtime-dir', '/tmp/vpp-unittest-Flowprobe', '}', 'api-trace', '{', 'on', '}', 'api-segment', '{',
'prefix', 'vpp-unittest-Flowprobe', '}', 'cpu', '{','main-core', '75', '}', 'physmem', '{','max-
size', '32m', '}','statseg', '{','socket-name', '/tmp/vpp-unittest-Flowprobe/stats.sock', '',
'}','socksvr', '{','socket-name', '/tmp/vpp-unittest-Flowprobe/api.sock', '}', 'node { ', '', '}',
'api-fuzz {', 'off', '}', 'plugins', '{', 'plugin', 'dpdk_plugin.so', '{', 'disable', '}', 'plugin',
'rdma_plugin.so', '{', 'disable', '}', 'plugin', 'lisp_unittest_plugin.so', '{', 'enable', '}',
'plugin', 'unittest_plugin.so', '{', 'enable', '}', '}']

### Question 8 : "Are the command line arguments to VPP correct?"
Yes

### Question 9 : "What is the price of tea in China?"
Sorry, the passage above does not mention anything about the price of tea in China. Please provide
me with the right passage that you want me to extract information from.
   