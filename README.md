# Common OSINT Model
**Note:** This is work in progress and probably only covers my specific use case. If you find bugs or know how to
enhance this project, please open an issue or - even better - create a pull request.  
  
This project aims to create an easy to use data model as well as implement converters for commonly used sources. As my
use case often includes HTTP(S), TLS and SSH only, data delivered for other protocols by the given sources might not
show up correctly.

## The data model
**Update:**  Since v0.4.0 this module implements a Pydantic model (see [models.py](common_osint_model/models.py)). This deprecates the previously used methods after it's implemented for all services (Shodan, Censys, BinaryEdge). For now, only Shodan related methods are implemented. In order to use the pydantic model, import classes from `common_osint_model.models` and use the specific `from_` methods, e.g.
```python
from common_osint_model.models import Host

host_data = shodan.host("1.2.3.4")
host = Host.from_shodan(host_data)
```
---
Please see the following examples of the data model - given as json but as it's a python dict, you can use other output
formats:

 - [Original Shodan data for 9.9.9.9](test_data/9.9.9.9_shodan.json) and the [flattened common model conversion](test_data/9.9.9.9_shodan_flattened.json)
 - [Original Censys data for 9.9.9.9](test_data/9.9.9.9_censys.json) and the [flattened common model conversion](test_data/9.9.9.9_censys_flattened.json)
 - [Original Shodan data for github.com](test_data/140.82.118.4_shodan.json) and the [flattened common model conversion](test_data/140.82.118.4_shodan_flattened.json) 
 - [Original Censys data for github.com](test_data/140.82.118.4_censys.json) and the [flattened common model conversion](test_data/140.82.118.4_censys_flattened.json)
 - [Certificate for google.com](test_data/www-google-com.pem) and the [flattened common model conversion](test_data/www-google-com_flattened.pem)
 - [Original Censys Certificate for google.com](test_data/www-google-com_censys.json) and the [flattened common model conversion](test_data/www-google-com_censys_flattened.json) 

Currently, only HTTP(S), TLS and SSH data as well as some meta data will get converted. TLS data includes information about other services using TLS, beside HTTPS, too.

## How to use

### Installation
```bash
pip install common-osint-model
```

### Convert all the things
```python
# Post v0.4.0 (Pydantic model)
from common_osint_model.models import Host
raw_shodan_data = shodan.host("1.2.3.4")
host = Host.from_shodan(raw_shodan_data)
print(host.ip)

for idx, service in enumerate(host.services):
    print(f"HTTP Headers for Service #{idx+1}:")
    if service.http:
        for header, value in service.http.headers.items():
            print(f"\t{header}: {value}")

# Pre v0.4.0 (Dictionary based approach)
from common_osint_model import from_shodan, from_shodan_flattened, from_censys_ipv4, from_censys_ipv4_flattened, from_x509_pem, from_x509_pem_flattened

raw_shodan = get_my_shodan_data()
converted_s = from_shodan(raw_shodan)  # Deprecated
flattened_s = from_shodan_flattened(raw_shodan)  # Deprecated

raw_censys_ipv4 = get_my_censys_ipv4_data()
converted_c = from_censys_ipv4(raw_censys_ipv4)
flattened_c = from_censys_ipv4_flattened(raw_censys_ipv4)

raw_certificate = open('certificate.pem').read()
converted_certificate = from_x509_pem(raw_shodan)
flattened_certificate = from_x509_pem_flattened(raw_shodan)
```
