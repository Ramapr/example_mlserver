# example_mlserver

Examples how to use mlserver. from doc


# request 

url set-up
```python 
host = 'localhost'
model_name = 'forecast'
ver = 'v0.0.1'

url = f"http://{host}:8080/v2/models/{model_name}/versions/{ver}/infer"
```

generate test of request
```python
import numpy as np

x = np.random.normal(size=(1, 2038))

inference_request = {
    "inputs": [
        {
          "name": "predict",
          "shape": x.shape,
          "datatype": "FP64",
          "data": x.tolist()
        }
    ]
}
```

request 
```python
import requests
response = requests.post(url, json=inference_request)
output = response.json()
print(output)
```

# kafka 

```python
host = '192...23'
port = 9092 
kafka_server = f'{host}:{port}'

topic_in = "mlinput"
topic_out = "mloutput"
```

## KafkaProducer

```python
import json
from kafka import KafkaProducer

producer = KafkaProducer(bootstrap_servers=kafka_server)
headers = {
    "mlserver-model": b"forecast",
    "mlserver-version": b"v0.0.1",
}

producer.send(topic_in,
         json.dumps(inference_request).encode("utf-8"),
         headers=list(headers.items()))
```

## KafkaConsumer

```python 
from kafka import KafkaConsumer

consumer = KafkaConsumer(
    topic_out, 
    bootstrap_servers=kafka_server,
    auto_offset_reset="earliest")


for msg in consumer:
    print(f"key: {msg.key}")
    print(f"value: {len(msg.value)}\n")
```

# API



