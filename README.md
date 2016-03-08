# WSO2 Big Data Game Predictor (http://wso2.com/landing/big-data-game/)

- Download WSO2 Machine Learner (WSO2 ML) 1.1.0 from http://wso2.com/products/machine-learner/
- Extract it into a folder
- Copy the [superbowl.war](https://github.com/nirmal070125/Super-Bowl-Predictions/blob/master/superbowl.war) file into ``wso2ml-1.1.0/repository/deployment/server/webapps/`` folder.
- Start WSO2 ML server 
```sh
wso2ml-1.1.0$ ./bin/wso2server.sh 
```
- Login to WSO2 ML UI and upload [this dataset](https://github.com/nirmal070125/Super-Bowl-Predictions/blob/master/super-bowl-training-data.csv) Refer to: https://docs.wso2.com/display/ML110/Exploring+Data
- Create a new ML project, an analysis and a ML model by following https://docs.wso2.com/display/ML110/Generating+Models. While creating the analysis, please use following configurations (**use defaults for all other configurations**);
    - Algorithm name : Random Forest Regression
    - Hyper parameter; Number of trees = 5
    - Hyper parameter; Maximum depth = 30
    - Hyper parameter; Bins = 55
- [**Skip this step, if you created only one ML model**] Models page of an analysis should list all the available models. Use the model name and retrieve the model id by doing [this](https://docs.wso2.com/display/ML110/Retrieving+a+Model+via+REST+API) API call.

API call:
```sh
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4=" -v https://localhost:9443/api/models/random-forest.Model.2016-03-08_10-46-32 -k 
```
Response:
```sh
{"name":"random-forest.Model.2016-03-08_10-46-32","id":3,"error":null,"status":"Complete","tenantId":-1234,"userName":"admin","createdTime":"2016-03-08 10:46:32.251","analysisId":1,"versionSetId":1,"storageType":"file","storageDirectory":"/random-forest.Model.2016-03-08_10-46-32","modelSummary":null,"datasetVersion":null}
```

- [**Skip this step, if you created only one ML model**] Stop the ML server and start it with -Dmodel.id=X
```sh
wso2ml-1.1.0$ ./bin/wso2server.sh -Dmodel.id=3
``` 
- Load the [new super bowl data](https://github.com/nirmal070125/Super-Bowl-Predictions/blob/master/super-bowl-test-data-with-team-name.csv) into the Predict API by invoking following API; 
```sh
curl -X POST -b cookies  https://localhost:9443/superbowl/predictions/load-data -H "Authorization: Basic YWRtaW46YWRtaW4=" -H "Content-Type: multipart/form-data" -F file=@'super-bowl-test-data-with-team-name.csv' -k
```
- Basic set up is ready now and you can start giving calling the super bowl predictive API now and obtain predicted winning percentages of teams.

Command:
```sh
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4=" -v https://127.0.0.1:9443/superbowl/predictions?teams=Arizona%20Cardinals,Atlanta%20Falcons  -k
```
Response:
```sh
[{"probability":62.48958506915514,"team":"Arizona Cardinals"},{"probability":37.51041493084486,"team":"Atlanta Falcons"}]
```

