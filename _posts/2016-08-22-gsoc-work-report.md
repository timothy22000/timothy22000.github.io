---
layout: post

#event information
title:  "GSoC 2016 Work Report"
cover: "img/clode.jpg"
date:   2016-08-22

organizer: "Timothy Sum"


---


<h2 class="section-heading">Introduction</h2>

The Open Web Application Security Project (OWASP) is a worldwide not-for-profit charitable organization focused on improving the security of software. The organization’s mission is to make software security visible, so that individuals and organizations are able to make informed decisions. OWASP is in a unique position to provide impartial, practical information about AppSec to individuals, corporations, universities, government agencies and other organizations worldwide.

AppSensor project defines a conceptual framework that provides a backbone to implementing application intrusion detection and automated response. My goal during my initial proposal is to extend and add a machine learning (ML) analysis engine to the AppSensor project. However, we decided to build an external system to conduct ML analysis on common web server logs to simulate the data processing that would happen in AppSensor as there were some requirements that the current AppSensor did not fulfill if we used Spark for streaming data processing and ML (such as being able to use a HDFS file system and others) during the community bonding period. This project is considered a standalone application separate from AppSensor at the moment and could grow into its own project that accepts any common log formats, runs ML algorithms (currently available in Spark MLib) and generates some rules. Besides that, the project started off as a proof of concept and is still currently in an experimental state as well as requiring more tests to be done. Therefore, it should be noted that it is not suitable for production usage yet.

<h2 class="section-heading">Work Done</h2>
This section will briefly cover what has been done overall during the summer period and milestones achieved. It is by no means a detailed record of every work that has been done (refer to the link for weekly meeting summaries below for that information).  

<h3>Community Bonding Period</h3>
For most of the community bonding period, I was working closely with my mentors (John, Juan, Giri) to solidify my project timeline plan. There was a lot of experimentation and looking into different technologies (such as InfluxDB, Kafka, Prometheus and others) in order to decide how we want to move forward with the project. It was truly interesting as there was no fixed tickets to close and we were learning together on how to approach this problem. Initially, we wanted to build the application within AppSensor itself but after some research (AppSensor lacked integrations for HDFS and Spark for now) we decided that I would develop a standalone application that will operate in a similar way to how AppSensor would by processing log files and conducting machine learning (ML) analysis. This approach allowed me to get the ball rolling earlier and familiarize myself with the technologies that I will be working with.

Milestone Reached:
<ol>
<li>Experimented and Researched with different technologies to decide on the best approach for the project (being flexible to any changes along the way)</li>
<li>Set up log management with Elasticsearch, Logstash and Kibana.</li>
<li>Set up Kafka and connect it with Spark Streaming to complete the ML pipeline.</li>
</ol>

<h3>Coding Period Part 1 (Before Mid Terms)</h3>
The first half of coding period was focused on getting results (good or bad) by running some ML algorithms on our data. Clustering algorithm - KMeans was chosen as the first ML algorithm as our dataset (apache log files) does not have any pre-determined class for a supervised algorithm to train on. Then, we can use the result for clustering to train a classification model to predict on future data as an additional step. Throughout this period, it was one of the most memorable period during GSoC as I learned the difficulties of data pre-processing to mould the data in the form that can be processed by ML algorithms as well as setting the parameters (ex: learning rate for StochasticGradientDescent) to obtain better results. From that experience, I was able to develop valuable skills as I gained a deeper understanding of the ML algorithms and optimizations done implicitly to allow it to work in the real world.

Milestone Reached:
<ol>
<li>Learned about SparkSQL, RDD and Dataframes. Then, applying them on the dataset</li>
<li>Implemented indexer, vectorizer and normalizers for feature extraction and transformation of our chosen features to able to run it in algorithms</li>
<li>Implemented four different ML algorithms (clustering, logistic regression, naive bayes, decision trees) from SparkMLib</li>
<li>Integrated the ML algorithms for our simple (one feature) and complex case (more than one feature)</li>
</ol>

<h3>Coding Period Part 2 </h3>
The second half built on the work done in the first half of GSoC by generating rules from the results of the ML analysis and improving the results themselves. The rule suggestion work was my first stretch goal in the project timeline. There was also a lot of new things I picked up with regards to geocoding, latitude/longitude, timezones. We also thought about visualization our data to allow us to better understand the results and why the results are that way. This was noted down as a possible future work for this project. There was also suggestions by John that I should make the project a lot more accessible to people so they are more likely to contribute. The codebase is still not where I would like it to be yet as most of my efforts was focused on the getting the ML analysis and rule generation to work. I will definitely look forward to contributing and continue working on it during my free time after GSoC. I had less time during this period as I had my university placement and logbook to complete which took away some time from my beloved GSoC project.

Milestone Reached:
<ol>
<li>Created rule generator for the results from any ML algorithms</li>
<li>Created a script to generate artificial server log data to get some good examples and for testing of the ML analysis</li>
<li>Created geocoder using Google Geocoding API and logstash's plugin to get lat/long as a feature</li>
</ol>

<h2 class="section-heading">How to Use Quick Guide</h2>
Set up your environment as described in the README.md of the <a href="https://github.com/timothy22000/GSoC-MLAnalysisEngine">Github</a> repo. Config file settings and dependencies required are also described there. What you should see when using this application is that you can feed it an apache log file, it will pass through Logstash into Kafka then into Spark Streaming where ML analysis will be ran and rules will be generated. Here are the steps to use the application:

<ol>
    <li>
    Start Zookeeper.
    ```bin/zookeeper-server-start.sh config/zookeeper.properties```
    </li>
    <li>
    Start Kafka.
    ```bin/kafka-server-start.sh config/server.properties```
    </li>
    Create your kafka topic. (I called it logstash_logs in my base)
    ```bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic logstash_logs```
    <li>
    Start logstash with your configurations (pointing to which folder, etc)
    ```logstash -f /etc/logstash/conf.d/file_to_kafka.conf --auto-reload```
    </li>
    <li>
    Start the application itself either by running in an IDE (Main.class) or through spark-submit with your desired parameters.
    </li>
    <li>
    Create, copy or move a log file of your choice into the folder that you set for Logstash to listen.
    (You use the script in the ```src/main/resources/scripts folder``` to generate artifical logfiles.)
    </li>
    <li>
    Wait for the application to complete its analysis and the output files are generated under
    ```src/main/resources/output``` by default.
    </li>
</ol>

<h2 class="section-heading">Links</h2>

Combined weekly meeting summaries and report to mailing lists:
This document contains a more detailed record of what has been done including bugs and new learnings.

<a href="https://docs.google.com/document/d/1XYFbZPX4EzFqaj9ViOkkZaBsC30peqDrmEXuZ1-DLwc/edit?usp=sharing">Weekly reports</a>

Github Repo:
This project has only been worked on by me so far so the whole repository is my work. I will be sharing two links (one for source repository, one for commit history).

<a href="https://github.com/timothy22000/GSoC-MLAnalysisEngine">Source Repo</a>

<a href="https://github.com/timothy22000/GSoC-MLAnalysisEngine/commits/master">Commits</a> (Last Commit: Aug 23, 2016 - Merging dev into master)

<h2 class="section-heading">Future Work</h2>
Even though most of the work that was planned has been completed during the GSoC period, there are still many improvements and extensions that can be made to the the project to make it a lot better and to get it past the experimental stage. This links to the importance of making the codebase simple to understand so that people are able to contribute easily.

<ol>
<li>Extend to be able to take in other common web server log formats.</li>
<li>Extend to take in other features that exist in common web server log formats through any feature extraction or transformation necessary.</li>
<li>Visualizing the why. Ex: which settings to use for different algorithms (learning rate, etc), which ML algorithms to use.</li>
<li>Integration with AppSensor.</li>
<li>Deploying application in full cluster mode.</li>
<li>Improve rule generation step.</li>
<li>Add Travis CI to Github Repo.</li>
<li>Add Apache Zeppelin for visualization of data.</li>
<li>Add REST API endpoints as an alternative way.</li>
<li>Add more tests as well as documentation.</li>
</ol>

<h2 class="section-heading">Acknowledgements</h2>
I'm indebted to John Melton for providing me with the opportunity to work on this amazing project. I would also like to express my gratitude to my mentors John, Juan, Giri for helping me through the duration of the projects and providing suggestions to any problems that I encounter.

<h2 class="section-heading">Contact</h2>
If you encounter any problems (I documented how to set up and run it but there could still be areas that are lacking) or you are interested in this project, please do contact me at timothy22000@gmail.com.
