![license](https://img.shields.io/github/license/federatedsecure/client-r)

# Usage

```r
source_url("https://raw.githubusercontent.com/federatedsecure/client-r/main/src/federatedsecure.r")
```

# Example

This example computes the sum of the three secret inputs of Alice, Bob, and Charlie.

Alice, Bob, and Charlie each run a Federated Secure Computing server with protocol Simon and share the following script:

**shared.r**

```r
URL_ALICE <- "http://127.0.0.1:55501"
URL_BOB <- "http://127.0.0.1:55502"
URL_CHARLIE <- "http://127.0.0.1:55503"
 
NODES <- c(URL_ALICE, URL_BOB, URL_CHARLIE)
TOKEN <- "387a7282-c380-44c9-aede-08da7e931931"
```

The token is any UUID. It is used to identify this particular computation, as the servers might be running many calculations at the same time. (If you set up this example for yourself on your own servers, you do not need to change this random UUID.)

Alice, Bob, and Charlie then run the following scripts **simultaneously**:

**alice.r**

```r
library(devtools)
source_url("https://raw.githubusercontent.com/federatedsecure/client-r/main/r/federatedsecure.r")
 
source("shared.r")
 
SECRET_ALICE <- 42.0
NETWORK_ALICE <- list(nodes=NODES, uuid=TOKEN, myself=0)
 
api <- Api(URL_ALICE)
microservice <- api$create(kwargs=list(protocol="Simon"))
compute <- microservice$attribute("compute")
result <- compute$call(list(), list(microprotocol="SecureSum", data=SECRET_ALICE, network=NETWORK_ALICE))
print(api$download(result))
```

**bob.r**

```r
library(devtools)
source_url("https://raw.githubusercontent.com/federatedsecure/client-r/main/r/federatedsecure.r")
 
source("shared.r")
 
SECRET_BOB <- 16.0
NETWORK_BOB <- list(nodes=NODES, uuid=TOKEN, myself=1)
 
api <- Api(URL_BOB)
microservice <- api$create(kwargs=list(protocol="Simon"))
compute <- microservice$attribute("compute")
result <- compute$call(list(), list(microprotocol="SecureSum", data=SECRET_BOB, network=NETWORK_BOB))
print(api$download(result))
```

**charlie.r**

```r
library(devtools)
source_url("https://raw.githubusercontent.com/federatedsecure/client-r/main/r/federatedsecure.r")
 
source("shared.r")
 
SECRET_CHARLIE <- 99.0
NETWORK_CHARLIE <- list(nodes=NODES, uuid=TOKEN, myself=2)
 
api <- Api(URL_CHARLIE)
microservice <- api$create(kwargs=list(protocol="Simon"))
compute <- microservice$attribute("compute")
result <- compute$call(list(), list(microprotocol="SecureSum", data=SECRET_CHARLIE, network=NETWORK_CHARLIE))
print(api$download(result))
```

So Alice's secret input was 42, Bob's secret input was 16, and Charlie's secret input was 99. The result is:

```json
{
  'inputs': 3,
  'result':
  {
    'samples': 3,
    'sum': 157.0
  }
}
```
