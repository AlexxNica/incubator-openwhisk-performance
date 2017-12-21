# :electric_plug: Apache OpenWhisk - Performance Tests
A few simple but efficient test suites for determining the maximum throughput and end-user latency of the Apache OpenWhisk system.

## Workflow
- A standard OpenWhisk system is deployed. Note that the edge NGINX router and API Gateway are currently left out. As a consequence, the tests talk directly to the controller.
- All limits are set to 999999, which for our cases means "No throttling at all".
- The deployment is using the docker setup proposed by the OpenWhisk development team: `overlay` driver and HTTP API enabled via a UNIX port.

The load is driven by either the beautiful [`loadtest`](https://www.npmjs.com/package/loadtest) module or the blazingly fast [`wrk`](https://github.com/wg/wrk).

### Travis Machine Setup
The [machine provided by Travis](https://docs.travis-ci.com/user/ci-environment/#Virtualization-environments) has ~2 CPU cores (likely shared through virtualization) and 7.5GB memory.

## Suites

### Latency test
The latency test determines the end-to-end latency a user experiences when doing a blocking invocation. The action used is a noop so the numbers returned are plain overhead of the OpenWhisk system.

- 1 HTTP request at a time (concurrency: 1)
- 10.000 samples with a single user
- noop action

**Note:** The throughput number has a 100% correlation with the latency in this case. This test does not serve to determine the maximum throughput of the system.

### Throughput test
The throughput test determines the maximum throughput a user can get out of the system while using a single action. The action used again is a noop, so the numbers are plain OpenWhisk overhead. Note that the throughput does not directly correlate to end-to-end latency here, as the system does more processing in the background as it shows to the user in a blocking invocation.

- 4 HTTP requests at a time (concurrency: 4) (basically CPU cores * 2 to exploit some buffering)
- 10.000 samples with a single user
- noop action

## Running tests against your own system is simple!
All you have to do is use the corresponding script located in `/tests` folder, remembering that the parameters are defined inline.
