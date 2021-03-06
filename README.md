# Retrieve & Rank Custom Scorers
## Description
This project enables the usage of custom features within the Retrieve & Rank service on Bluemix. This project was built in the
Python programming language and uses the Flask micro-framework. To use this application, there is a Python script called server.py,
which exposes two endpoints to be consumed by the application that uses it.
## Application Architecture 
The Flask server that is created within the script server.py is intended to run as a "sidecar" within the rest of the application;
that is, the parent application will make REST API calls to this "sidecar" service rather than making direct calls to the
deployed Retrieve & Rank service. The principal difference is that the Flask server will handle the integration/injection of
custom features that have been registered.
## Steps to get set up
There are 4 steps to set up the server to integrate custom features:
* Configure your environment. There is a script with path 'bin/install.sh', which will install the dependencies necessary to start the server; if this is your first time
using this project, you should run the install.sh script
* Identify the custom scorers for your application. A custom scorer is a Python class that extracts a signal that is to be used for the ranker. There are 3 types of scorers supported,
    1.) "QueryDocument" Scorer - Extracts a signal/score based on both the contents of a query and the contents of a Solr Document
    2.) "Document" Scorer - Extracts a signal based on the contents of the Solr Document alone
    3.) "Query" Scorer - Extracts a signal based on the contents of the query alone
These custom scorers are based on the project 'rr_custom_scorers' as part of the cognitive catalyst project (url here); please see this project for additional details.
To make sure that the proxy service has the most up to date scorers, go to the 'rr_custom_scorers' project, build a wheel file (using the command pip wheel), 
and then copy that wheel file to the lib directory here. Re-run the bin/install.sh script to make sure your Python environment has the most up to date dependencies.
* Create a configuration file (see configs/sample_features.json as an example) to configure your application to consume these scorers. The configuration file must be a json file and
must contain a "scorers" field, which is a list of individual scorer configurations. For each of the scorers that you identified in the previous step, the scorer configuration is a JSON object
that must define the following:
    * an 'init_args' json object, whose fields are the arguments to the constructor for the scorer
    * a 'type' field, which should be either 'query', 'document' or 'query_document', depending on the type of scorer. The type is used to identify the package
    within the 'rr_scorers' project that contains the appropriate scorer
    * a 'module' field, which is the name of the python module which contains the scorer
    * a 'class' field, which is the name of the scorer class
For comparison, the config/sample_features.json contains a single QueryDocument scorer, in the module nlc_intent_scorer, with the class QuestionDocumentIntentAlignmentScorer
* Start the Flask server by running the command 'python server.py ... ' with the proper parameters. Alternatively, there is a shell script called 
'run.sh', which takes a properties/configuration file (for the underlying retrieve and rank service)
* Make calls to the endpoint that is configured when starting the server.py. By default, the host will be localhost and the port will be 9216