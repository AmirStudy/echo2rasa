# Table of contents

- [Introduction](#introduction)
- [Python](#python)
- [Rasa](#rasa)
- [Example Skill](#example-skill)
- [Setup](#setup)
  * [config.yml](#configyml)
  * [echoconnector.py](#echoconnectorpy)
    + [EchoConnector](#echoconnector)
    + [EchoNLUMapper](#echonlumapper)
- [Configurations](#configurations)
  * [credentials.yml](#credentialsyml)
- [Train and test the model without Echo support (optional)](#train-and-test-the-model-without-echo-support--optional-)
  * [Setup Duckling](#setup-duckling)
- [Train the model for Alexa/Echo usage](#train-the-model-for-alexa-echo-usage)
- [Start the service](#start-the-service)
  * [start_echo_service.bat](#start-echo-servicebat)
    + [Test Service Locally](#test-service-locally)
  * [Expose Service (ngrok)](#expose-service--ngrok-)
    + [Test Service via Internet](#test-service-via-internet)
- [Setup Alexa Skill](#setup-alexa-skill)
  * [Generate JSON Configuration](#generate-json-configuration)
    + [echo_domain.yml](#echo-domainyml)
    + [genEchoDefinition](#genechodefinition)
      - [Default parameter values](#default-parameter-values)
  * [Create new Alexa Skill](#create-new-alexa-skill)
  * [Test Your Skill](#test-your-skill)
- [Next Steps](#next-steps)
- [License](#license)
  * [echo2rasa module and according configuration parts](#echo2rasa-module-and-according-configuration-parts)
  * [RASA and used form bot examples](#rasa-and-used-form-bot-examples)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# Introduction

This repository contains the complete code of the "echo2rasa" example project which gives a working example of an Amazon Alexa Skill implementd with the [RASA](https://rasa.com/ "RASA Home") framework.
The [NLU](https://en.wikipedia.org/wiki/Natural-language_understanding "NLU Wiki entry") part of the skill will be performed on the Alexa side while the complete dialogue handling is done by the [RASA Core](https://rasa.com/docs/core/ "RASA core documentation").
The basics of [RASA connectors](https://rasa.com/docs/rasa/1.1.4/user-guide/connectors/custom-connectors/ "RASA connectors") are explained [here](https://rasa.com/docs/rasa/1.1.4/user-guide/connectors/custom-connectors/ "RASA connectors").

# Python 
This example is based on Python version 3.7.3 which has been installed in an Anaconda managed virtual environment. Used conda version was 4.6.14.

In an anaconda command prompt you may create a virtual environment like this.
~~~
conda create -n echodemo python=3.7.3
conda activate echodemo
~~~
# Rasa
Latest version of Rasa-X has been used. This offers the ability to use the Rasa-X training facility to improve your skill from real converstations. 
~~~ 
pip install rasa-x --extra-index-url https://pypi.rasa.com/simple
~~~
# Example Skill
The example used in this project is the restaurant bot example taken from the [Rasa project on GitHub](https://github.com/RasaHQ/rasa/tree/master/examples/formbot "Rasa Formbot Example"). The complete example is contained in this repository

# Setup
The complete Alexa echo relevant files are stored within subdirectory [echo2rasa](./echo2rasa).

## config.yml

The [config.yml](./echo2rasa/config.yml) contains the configuration for the Rasa NLU and also the Rasa Core parts. Only the NLU part is usually different from the config.yml in the project root directory.

## echoconnector.py
[echoconnector.py](./echo2rasa/echoconnector.py) is the module defining the EchoConnector and the EchoNLUMapper classes.

### EchoConnector
Provides REST endpoints to our server that the Alexa skill will call to deliver messages.

### EchoNLUMapper
Maps the Intents, Slots and Entities recognized and delivered by Alexa to corresponding Rasa objects. 

# Configurations
## credentials.yml
The following configuration has been made within the [credentials.yml](credentials.yml)
~~~
echoconnector.EchoConnector:
  # username: "dummy"
~~~

# Train and test the model without Echo support (optional)
To train the model for local tests we may use the original  config.yml file. This will train the nlu and core models. Be aware, the nlu part of the model is only relevant to perform local tests. In the later Alexa/Echo scenaorio, nlu will be not be performed by RASA. 

Training will be performed via
~~~
rasa train
~~~

## Setup Duckling
To run the example skill without Alexa/Echo nlu recognition, the Duckling library is used. It will parse text into structured data. Rasa provides a [Docker image to use Duckling](https://hub.docker.com/r/rasa/duckling).

# Train the model for Alexa/Echo usage
Before we may start the service, we have to train the core model. Training of nlu is also necessary even if the actual nlu will be performed on Alexa side. Training is performed by [train_echo_model.bat](train_echo_model.bat)

![Training the model](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/trainRasa.jpg)

# Start the service
## start_echo_service.bat
[start_echo_service.bat](start_echo_service.bat) starts the action server and the echo connector service.

![Start Service](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/startService.jpg)
### Test Service Locally
Within browser or via curl you may perform local test of service.
~~~
http://localhost:5005/webhooks/echo
~~~
C:\Users\Heiko\PY_PROJECTS\rasa\echodemo\echo2rasa\resource\test_localservice.![Local test](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/test_localservice.jpg)

## Expose Service (ngrok)
For testing purposes the newly created service will be exposed via [ngrok](https://ngrok.com/download "Download ngrok")
~~~
ngrok http -host-header="localhost:5005" 5005
~~~
![ngrok Service](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/ngrokStart.jpg)

To use as Alexa service endpoint use the ssl securede https endpoint (i.e. https://c3110a33.ngrok.io)

### Test Service via Internet
Within browser or via curl you may perform internet test of service.
~~~
https://a97ed28f.ngrok.io/webhooks/echo
~~~
C:\Users\Heiko\PY_PROJECTS\rasa\echodemo\echo2rasa\resource\test_localservice.![Local test](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/test_ngrok.jpg)

# Setup Alexa Skill
## Generate JSON Configuration
### echo_domain.yml
The [echo_domain.yml](./echo2rasa/echo_domain.yml) contains the configuration to provide the slot type information needed by Alexa/Echo nlu.
### genEchoDefinition
The python script [genEchoDefinition.py](./echo2rasa/tools/genEchoDefinition.py) performs the dump of the Alexa/Echo skill needed json configuration file. Run with the --help option to get the list of parameters.
~~~
echodemo\echo2rasa\tools>python genEchoDefinition.py --help
usage: genEchoDefinition.py [-h] [-i INVOCATION] [-d DOMAIN] [-n NLU]
                            [-e ECHOCONF] [-o OUTPUT]

optional arguments:
  -h, --help            show this help message and exit
  -i INVOCATION, --invocation INVOCATION
                        Echo skill invocation name
  -d DOMAIN, --domain DOMAIN
                        domain definition file
  -n NLU, --nlu NLU     nlu training file
  -e ECHOCONF, --echoconf ECHOCONF
                        echo related configurations
  -o OUTPUT, --output OUTPUT
                        output path to echo configuration file
~~~

If you put all the configuration files in their default locations, you may simply run the script without any parameters. 

~~~
echodemo>cd echo2rasa\tools

echodemo\echo2rasa\tools>python genEchoDefinition.py
Alexa/Echo model dumped to echoSkillConfiguration.json
~~~

The script will generate the Alexa/Echo configuration within file [echoSkillConfiguration.json](./echo2rasa/tools/echoSkillConfiguration.json) 

#### Default parameter values
| Parameter | Parameter Name| Pararmeter Value |
| --------- | :-----------: | ---------------: |
| Echo skill invocation name | -i, --invocation | rasademo |
| Domain definition file | -d, --domain | domain.yml |
| Nlu training file | -n, --nlu | data\nlu.md |
| Echo related configurations | -e, --echoconf | echo2rasa\echo_domain.yml |
| Output path to echo configuration json file | -o, --output | echoSkillConfiguration.json |



## Create new Alexa Skill
On the Alexa developper side https://developer.amazon.com/alexa/console/ask choose "Create Skill" to create a new skill.
![Create new Alexa Skill](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_createSkill.jpg)

Choose custom model and provisioning of own service.
![Type of model and provisioning](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_skillType.jpg)

Choose "Start from scratch" as your template.
![Choose template "From Scratch"](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_skillType_2.jpg)

Do not perform any manual configurations but go directly to the json editor.
![Open json editor](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_configuration_json.jpg)

Paste the content of the generated configuration file [echoSkillConfiguration.json](./echo2rasa/tools/echoSkillConfiguration.json) into the json editor and press "Build Model".

![Paste json configuration and build](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_configuration_build.jpg)

Your model will be saved and build. After the successfull build you will be informed with a corresponding message.

Now configure your endpoint. This will be the ngrok exposed https port of your local machine (e.g. https://e7f679dc.ngrok.io/webhooks/echo/).
![Choose Endpoint](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_configure_endpoint.jpg)


![Configure Endpoint](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_configure_endpoint_2.jpg)

## Test Your Skill
Go to the Alexa test page and enable testing of your skill.
![Enable skill testing](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_test.jpg)

Invoke your skill
![Invoke your skill](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_dialog_01.jpg)

The result will be something like the following.
![Example dialogue](https://github.com/BadaBoomi/echo2rasa/blob/master/echo2rasa/resource/echo_dialog_02.jpg)

# Next Steps
This project provides the technical breakthrough to use Rasa for Alexa/Echo skills. It is however far from beeing complete. As a next improvement the ability to define Alexa/Echo specific utterance annotations (e.g. to give additional utterances for the user reprompting).

# License
## echo2rasa module and according configuration parts
Copyright 2019 HeiooDo

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

## RASA and used form bot examples
- see [RASAHQ](https://github.com/RasaHQ/rasa)
- see the [Rasa project on GitHub](https://github.com/RasaHQ/rasa/tree/master/examples/formbot "Rasa Formbot Example")
