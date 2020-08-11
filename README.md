# Python SDK for [CloudEvents](https://github.com/cloudevents/spec)

[![PyPI version](https://badge.fury.io/py/cloudevents.svg)](https://badge.fury.io/py/cloudevents)

## Status

This SDK is still considered a work in progress, therefore things might (and
will) break with every update.

This SDK current supports the following versions of CloudEvents:

- v1.0
- v0.3

## Python SDK

Package **cloudevents** provides primitives to work with CloudEvents specification: https://github.com/cloudevents/spec.

## Sending CloudEvents

Below we will provide samples on how to send cloudevents using the popular
[`requests`](http://docs.python-requests.org) library.

### Binary HTTP CloudEvent

```python
from cloudevents.http import CloudEvent, to_binary_http
import requests


# This data defines a binary cloudevent
attributes = {
    "type": "com.example.sampletype1",
    "source": "https://example.com/event-producer",
}
data = {"message": "Hello World!"}

event = CloudEvent(attributes, data)
headers, body = to_binary_http(event)

# POST
requests.post("<some-url>", data=body, headers=headers)
```

### Structured HTTP CloudEvent

```python
from cloudevents.http import CloudEvent, to_structured_http
import requests


# This data defines a structured cloudevent
attributes = {
    "type": "com.example.sampletype2",
    "source": "https://example.com/event-producer",
}
data = {"message": "Hello World!"}
event = CloudEvent(attributes, data)
headers, body = to_structured_http(event)

# POST
requests.post("<some-url>", data=body, headers=headers)
```

You can find a complete example of turning a CloudEvent into a HTTP request [in the samples directory](samples/http-json-cloudevents/client.py).

#### Request to CloudEvent

The code below shows how to consume a cloudevent using the popular python web framework
[flask](https://flask.palletsprojects.com/en/1.1.x/quickstart/):

```python
from flask import Flask, request

from cloudevents.http import from_http

app = Flask(__name__)


# create an endpoint at http://localhost:/3000/
@app.route("/", methods=["POST"])
def home():
    # create a CloudEvent
    event = from_http(request.get_data(), request.headers)

    # you can access cloudevent fields as seen below
    print(
        f"Found {event['id']} from {event['source']} with type "
        f"{event['type']} and specversion {event['specversion']}"
    )

    return "", 204


if __name__ == "__main__":
    app.run(port=3000)
```

You can find a complete example of turning a CloudEvent into a HTTP request [in the samples directory](samples/http-json-cloudevents/server.py).
