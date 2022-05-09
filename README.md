# Project - Sparkify Data Lake
Sparkify, a music streaming startup, has grown their user base and song database even more and want to move their data warehouse to a data lake. Their data resides in S3, in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

In this project, we are tasked with building an ETL pipeline that extracts their data from S3, processes them using Spark, and loads the data back into S3 as a set of dimensional tables. This will allow their analytics team to continue finding insights in what songs their users are listening to.

## Deployement

File `dl.cfg` is not included. This file contains:

```
KEY=YOUR_AWS_ACCESS_KEY
SECRET=YOUR_AWS_SECRET_KEY
```

This project has been deployed and run on EMR in AWS environment with the following specs: 

1. EMR with applications: Spark 2.4.4, Zeppelin 0.8.2, Livy 0.6.0, JupyterHub 1.0.0
2. Release label: emr-5.28.0
3. To install the `configparser` run the below command:<br> 
`sudo pip install configparser` <br>
4. Copy the etl.py to `/home/hadoop` like below:<br>
`scp -i <local_path/PEM_FILE.pem <local_path\etl.py> hadoop@ec2-54-237-205-104.compute-1.amazonaws.com:/home/hadoop/`<br>
5. Run the job by typing the below command: <br> 
`/usr/bin/spark-submit --master yarn ./etl.py`

## ETL Pipeline
    
1.  Read Song and Log data from S3
    
    * Song data:  `s3://udacity-dend/song_data`
    * Log data:  `s3://udacity-dend/log_data`<br>
    
    
2. Use Spark to process the data        
    
    Transform song and log data in to five dimension tables listed below : <br> 
    #### Fact Table
	 **songplays**  - records in log data associated with song plays i.e. records with page `NextSong`<br>
    _songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent_

	#### Dimension Tables
	 **users**  - users in the app -  _user_id, first_name, last_name, gender, level_
		
	 **songs**  - songs in music database - _song_id, title, artist_id, year, duration_
    
	 **artists**  - artists in music database -   _artist_id, name, location, lattitude, longitude_
    
	 **time**  - timestamps of records in  **songplays**  broken down into specific units -   _start_time, hour, day, week, month, year, weekday_ <br>
     
    
3.  Load dimensional tables back to S3 in parquet format
    
    Writes tables to partitioned parquet files in to five directories on S3.