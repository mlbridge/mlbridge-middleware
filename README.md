# ML Bridge - Middleware
[![Build Status](https://travis-ci.org/mlbridge/mlbridge-middleware.svg?branch=master)](https://travis-ci.org/mlbridge/mlbridge-middleware)
[![codecov](https://codecov.io/gh/mlbridge/mlbridge-middleware/branch/master/graph/badge.svg)](https://codecov.io/gh/mlbridge/mlbridge-middleware)
[![GitHub issues](https://img.shields.io/github/issues/mlbridge/mlbridge-middleware)](https://github.com/mlbridge/mlbridge-middleware/issues)
[![GitHub license](https://img.shields.io/github/license/mlbridge/mlbridge-middleware)](https://github.com/mlbridge/mlbridge-middleware/blob/master/LICENSE)

![GSoC Icon](https://developers.google.com/open-source/gsoc/resources/downloads/GSoC-logo-horizontal-200.png)




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
cd mlbridge-middleware/mlbridge_middleware/src
python middleware.py
```

## Features

The middleware is a Python Flask Server that receives the request along with 
other metadata. The Flask Server infers whether the request was malicious or 
benign, via a pre-trained TensorFlow Model, and then sends back a response based 
on this result, to the plugin. It also stores the result along with other 
metadata, to a database.

### Application Middleware with Flask

The middleware is a Python Flask Server that contains the pre-trained 
Convolutional Neural Network. The Flask Server receives the domain name queried 
as well as the IP address of the machine used to query that particular domain 
name, as a JSON message, via HTTP POST requests from the plugin.  

Once the Flask Server receives the domain name and the IP address, the domain 
name is preprocessed and then passed to the pre-trained deep learning model. The
deep learning model then classifies whether the domain name is of a malicious 
website or not and then sends the same back to plugin as a JSON message.

The classification result as well as other metadata such as the IP address, the 
date and time of the request are stored in a NoSQL database, namely 
Elasticsearch, due to which storing and querying the classification result and 
the metadata is a fast process. 

Before running the Flask Server, it is recommended that the Elasticsearch server
is running in the background. To install Elasticsearch, please follow the 
instructions found on this 
[page](https://phoenixnap.com/kb/install-elasticsearch-ubuntu). Once 
Elasticsearch is installed, `cd` into it and enter `bin/elasticsearch` to run the 
Elasticsearch server. 

### Machine Learning

__Learning Dataset__

The deep-learning model is trained on a COVID-19 Cyber Threat Coalition 
Blacklist for malicious domains that can be found 
[here](https://blacklist.cyberthreatcoalition.org/vetted/domain.txt) and on a 
list of benign domains from DomCop that can be found 
[here](https://www.domcop.com/top-10-million-domains). 

Currently, the pre-trained model has been trained on the top 500 domain names 
from both these datasets. The final version of the pre-trained model will be 
trained on the entirety of both the datasets.  

__Learning Process__

Data Preprocessing: Each domain name is converted into a unicode code point 
representation and then extended to a numpy array of a length 256. The dataset 
was created by combining the malicious domains as well as the non-malicious. 
The dataset was split as follows:
- Train Set: 80% of the dataset.
- Validation Set: 10 % of the dataset
- Test Set: 10% of the dataset

Training: The deep-learning model is a Convolutional Neural Net that is 
trained using batch gradient descent with the Adam optimizer.

### TensorFlow Model

#### TensorFlow Model Definition

The pre-trained deep learning model is a Convolutional Neural Net whose input is
a (16, 16, 1) shaped array and the output is a single value lying in between 0 
and 1. If the output value is less than 0.5 the domain name is considered benign
, else it is considered malicious. 

The model summary can be found below:


| Layer      | Output Shape          | Activation   | Number of Parameters |
|:----------:|:---------------------:|:------------:|:--------------------:|
| Input      | (None, 16, 16, 1 )    | -            |0                     |
| Conv2D     | (None, 15, 15, 16)    | Relu         |80                    |
| MaxPooling | (None, 7, 7, 16)      | -            |0                     |
| Conv2D     | (None, 6, 6, 16)      | Relu         |1040                  |
| MaxPooling | (None, 3, 3, 16)      | -            |0                     |
| Conv2D     | (None, 2, 2, 8 )      | Relu         |520                   |
| Flatten    | (None, 32)            | -            |0                     |
| Dense      | (None, 8 )            | Relu         |264                   |
| Dense      | (None, 1 )            | Sigmoid      |9                     |

#### TensorFlow Model Visualization

The model can be visualized as follows: 

![image info](./readme-assets/model_.png)

#### Results

The accuracy for the Train Set, Validation Set and Test Set is as follows:

| Metric   | Train Set   | Validation Set | Test Set |  
|----------|-------------|----------------|----------|
| Accuracy | 99.25 %     | 98.00 %        | 98.00 %  |




