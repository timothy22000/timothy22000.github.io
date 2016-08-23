---
layout: post

#event information
title:  "GSoC 2016 Work Report"
cover: "img/clode.jpg"
date:   2016-08-22


---


<h2 class="section-heading">Introduction</h2>

The Open Web Application Security Project (OWASP) is a worldwide not-for-profit charitable organization focused on improving the security of software. The organization’s mission is to make software security visible, so that individuals and organizations are able to make informed decisions. OWASP is in a unique position to provide impartial, practical information about AppSec to individuals, corporations, universities, government agencies and other organizations worldwide.

AppSensor project defines a conceptual framework that provides a backbone to implementing application intrusion detection and automated response. My goal during my initial proposal is to extend and add a machine learning (ML) analysis engine to the AppSensor project. However, we decided to build an external system to conduct ML analysis on common web server logs to simulate the data processing that would happen in AppSensor as there were some requirements that the current AppSensor did not fulfill if we used Spark for streaming data processing and ML (such as being able to use a HDFS file system and others) during the community bonding period. This project is considered a standalone application separate from AppSensor at the moment and could grow into its own project that accepts any common log formats, runs ML algorithms (currently available in Spark MLib) and generates some rules. Besides that, the project started off as a proof of concept and is still currently in an experimental state as well as requiring more tests to be done. Therefore, it should be noted that it is not suitable for production usage yet.

<h2 class="section-heading">Work Done</h2>

<h3>Community Bonding Period</h3>

<h3>Coding Period Part 1 (Before Mid Terms)</h3>

<h3>Coding Period Part 2 </h3>

<h2 class="section-heading">Links</h2>
Video Showing How to Use:


Combined weekly meeting summaries and report to mailing lists:
This document contains a more detailed record of what has been done including bugs and new learnings.
<a href="https://docs.google.com/document/d/1XYFbZPX4EzFqaj9ViOkkZaBsC30peqDrmEXuZ1-DLwc/edit?usp=sharing">Weekly reports</a>

Github Repo:
This project has only been worked on by me so far so the whole repository is my work. I will be sharing two links (one for source repository, one for commit history).

<a href="https://github.com/timothy22000/GSoC-MLAnalysisEngine">Source Repo</a>

<a href="https://github.com/timothy22000/GSoC-MLAnalysisEngine/commits/master">Commits</a>

<h2 class="section-heading">Future Work</h2>
Even though most of the work that was planned has been completed during the GSoC period, there are still many improvements and extensions that can be made to the the project to make it a lot better and to get it past the experimental stage. This links to the importance of making the codebase simple to understand so that people are able to contribute easily.

1) Extend to be able to take in other common web server log formats
2) Extend to take in other features that exist in common web server log formats through any feature extraction or transformation necessary.
3) Visualizing the why - ex: which settings to use for different algorithms (learning rate, etc), which ML algorithms to use
4) Integration with AppSensor
5) Deploying application in full cluster mode
6) Improve rule generation step
7) Add Travis CI to Github Repo
8) Add Apache Zeppelin for visualization of data.
9) Add REST API endpoints as an alternative way
10) Add more tests as well as documentation

<h2 class="section-heading">Acknowledgements</h2>
I'm indebted to John Melton for providing me with the opportunity to work on this amazing project. I would also like to express my gratitude to my mentors John, Juan, Giri for helping me through the duration of the projects and providing suggestions to any problems that I encounter.

<h2 class="section-heading">Contact</h2>
If you encounter any problems (I documented how to set up and run it but there could still be areas that are lacking) or you are interested in this project, please do contact me at timothy22000@gmail.com .
