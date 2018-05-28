## This Repo has two main components
1. Pachyderm pipeline example
2. Pachyderm one-pager marketing example. 


### Pachyderm pipeline example

The goal of this app/pipeline is to try and predict flight delays for all US flights by carrier using historical data that's automatically scraped from the Bureau of Transportation statistics. While the application is meant to analyze an entire year's worth of data, for the sake of time I've configured it to only import a months worth of data into the dataframe, and then a regression done on a specific day for a specific airport. This cuts down compute wait time for the result.   

At its current state the application behavior is as follows: 

1. Once a day at 3pm, a cron pachyderm job is kicked off to check and see if there's a new months data to be gathered and stored. This is handled through the [ingest_flights](https://flights-dot-flightdata-201923.appspot.com/) deployed in google app engine and source is located in `monthlyupdate/`.
	* Ingest_flights is also rudimentary ETL process. It fetches the file with the required params needed to train the model and then downloads the zip from the BTS site. Then, it's extracted from zip and all extra commas and parentheses are removed. Finally, the file is renamed YEAR/Month.csb (2018/01), then uploaded to gs bucket for pachyderm to use.   

2. Once a day at 4pm, The `flights.py` application located in `model/` will then get kicked off and do a linear regression of the dataset using sklearn. This will result in a plot being returned of the prediction.

3. Output of flightdelayprediction.py (png graph) will then be stored in /pfs/out which is an [egress located here](https://storage.googleapis.com/2017_2018_flights/output/output.png).

#### TODO:
1. Currently, the application is hardcoded to investigate 01/18 data. Instead the application needs to automatically determine the lastest complete month and then execute on the corresponding dataset. There should also be a webform for a user to input airport and carrier ID to return specific results. 

2. The application doesn't currently take full advantage of the parallelism that pachyderm is capable of. Next steps are to group the data into two dataum's: alltime and current month. Then execute two seperate predictions in parallel. Currently strangeness with GCS, GKE, and pachyderm is not allowing the cross pipeline to function properly. I'll continue to look into this in the future. 

3. The flight prediction application only does a simple linear regression and should be capable of more advanced machine learning prediction methods. 


#### Notes:
I was originally implementing my own version of this application in tensorflow, with the ultimate goal to leverage distributed tensorflow and pachyderm. However, I was running into too many tensorflow specific issues that would have caused me to exceed the time limit set for this project. Therefore, I had to follow/borrow from these sources to edit up with a similar result: 

1. [https://github.com/GoogleCloudPlatform/data-science-on-gcp](https://github.com/GoogleCloudPlatform/data-science-on-gcp)
2. [https://www.kaggle.com/fabiendaniel/predicting-flight-delays-tutorial](https://www.kaggle.com/fabiendaniel/predicting-flight-delays-tutorial)


### Pachyderm One Pager 
This is an one-pager marketing asset I created (aka product datasheet) that is meant to explain the value of Pachyderm. The audience I had in mind was that of an IT decision maker(manager/director) who might have heard about pachyderm from one of his employees and wants to quickly find out what it is and the value it provides. 
 
**Important:** When writing material such as this, it's important to base the content on what your customers find most valuable to ensure relevance. Since I don't have any internal knowledge of Pachyderms customers, I had to write the draft using my experiences with data science teams in the field as a primary source. All of this to say, if the problem statement doesn't resonate with what you've witnessed with your customers, its because I wasn't able to leverage that information :) 