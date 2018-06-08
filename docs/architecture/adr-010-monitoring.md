# ADR 010: Monitoring

## Changelog

08-06-2018: Initial draft

## Context

In order to bring more visibility into Tendermint, we would like it to report
metrics and, maybe later, traces of transactions and RPC queries. See
https://github.com/tendermint/tendermint/issues/986.

A few solutions were considered:

1. [Prometheus](https://prometheus.io)
2. [go-kit metrics package](https://github.com/go-kit/kit/tree/master/metrics)
3. [telegraf](https://github.com/influxdata/telegraf)
4. new service, which will listen to events emitted by pubsub and report metrics
5. [OpenCensus](https://opencensus.io/go/index.html)

### Prometheus

Prometheus seems to be the most popular product out there for monitoring. It has
a Go client library, powerful queries, alerts.

We can commit to using Prometheus in Tendermint, but I think Tendermint users
should be free to choose whatever monitoring tool they feel will better suit
their needs (if they don't have existing one already).

### go-kit metrics package

metrics package provides a set of uniform interfaces for service
instrumentation and offers adapters to popular metrics packages:

https://godoc.org/github.com/go-kit/kit/metrics#pkg-subdirectories

Comparing to Prometheus, we're losing customisability and control, but gaining
freedom in choosing any instrument from the above list.

### telegraf

Unlike Prometheus or go-kit, telegraf does not require modifying Tendermint
source code. You create something called an input plugin, which polls
Tendermint RPC every second and calculates the metrics itself.

While it may sound good, but some metrics we want to report are not exposed via
RPC or pubsub, therefore can't be accessed externally.

### service, listening to pubsub

Same issue as the above.

### opencensus

opencensus provides both metrics and tracing, which may be important in the
future. It's API looks different from go-kit and Prometheus, but looks like it
covers everything we need.

### List of metrics

| A | height                                  | Counter |                                                                               |
| A | validators:<height>                     | Gauge   | Number of validators who signed                                               |
| A | missing_validators:<height>             | Gauge   | Number of validators who did not sign                                         |
| A | byzantine_validators:<height>           | Gauge   | Number of validators who tried to double sign                                 |
| A | block_interval                          | Timing  | Time between this and last block (Block.Header.Time)                          |
|   | block_time                              | Timing  | Time to create a block (from creating a proposal to commit)                   |
|   | time_between_blocks                     | Timing  | Time between committing last block and (receiving proposal creating proposal) |
| A | rounds:<height>                         | Counter | Number of rounds                                                              |
|   | prevotes:<height>:<round>               | Counter |                                                                               |
|   | precommits:<height>:<round>             | Counter |                                                                               |
|   | prevotes_total_power:<height>:<round>   | Counter |                                                                               |
|   | precommits_total_power:<height>:<round> | Counter |                                                                               |
| A | num_txs:<height>                        | Counter |                                                                               |
|   | total_txs                               | Counter |                                                                               |
|   | block_size:<height>                     | Gauge   | In bytes                                                                      |
|   | peers                                   | Gauge   | Number of peers node's connected to                                           |
|   | power                                   | Gauge   |                                                                               |

`A`	- will be implemented in the fist place.

**Proposed solution**

## Status

## Consequences

### Positive

### Negative

### Neutral
