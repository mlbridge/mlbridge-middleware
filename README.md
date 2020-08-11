# MLBridge-Middleware

This repository contains the middleware for the MLBridge project, which allows
the user to provide machine learning capabilities to languages and platforms 
that generally don't have such capabilities. 

The current use case is the identification of the DNS requests ,via machine 
learning, for the records of the domains that could be used by malicious hackers
and other computer criminals. Upon the identification of the requests the system 
would either alert the sysadmin formanual vetting or block the requests and 
responses.

## Installation

Clone the repository:
```
git clone https://github.com/mlbridge/mlbridge-middleware.git
```

Go to the `mlbridge-middleware` directory and install the dependencies:
```
cd mlbridge-middleware
pip install -r requirements.txt
```

Install Elasticsearch by following the instructions from this 
[link](https://phoenixnap.com/kb/install-elasticsearch-ubuntu). Start the 
Elasticsearch server and then run the `middleware.py` app:
```
cd mlbridge-ui/mlbridge_middleware/src
python3 ui.py
```
