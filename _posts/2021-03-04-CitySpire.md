---
layout: post
title: CitySpire
subtitle: An app's tale, from idea to deployment
gh-repo: github.com/jiobu1
#gh-badge: [star, fork, follow]
tags: [App Deployment, AWS, AWS RDS, AWS Elastic Beanstalk, Data Science, Machine Learning, Teamwork, Data Visualization, Plotly]
comments: true
---

<p><strong>TLDR:</strong> Worked for 8 weeks (part time) as a data scientist and machine learning engineer on a remote interdisciplinary team; helped to gather, clean and wrangle data to create a recommendation model; created an AWS RDS Postgres database to store data and sent this information to front end through endpoints tested with FastAPI and deployed using AWS Elastic Beanstalk.</p>

<img src = "../img/labs/cover_art/NYC+Hotels+Best+Views.jpg" alt="NYC Hotel" class="center">
<h5 align="center"> https://images.squarespace-cdn.com </h5>

<br>

<h2> Introduction: </h2>

<p><strong><em>- Eeny, Meeny, Miny, Moe, is this how you will find your new home city? </em></strong></p>

<p><strong>Problem:</strong> Imagine you are looking to relocate, how do you decide which new city is right for you? Should you ask friends? Look at Yahoo, Google or TripAdvisor? Moving is not easy and it is a big decision. Where will you find information such as rental prices? What are the demographics of the place, crime rate, pollution? Will you need a car to live there or is it pedestrian friendly? How will you find all this information? You could take hours googling different websites or you can checkout CitySpire.</p>

<p><strong>Solution:</strong> CitySpire’s goal is to be a one-stop resource for users to receive the most accurate city information. We take the hassle out of finding the perfect city to call home by giving you a data driven recommendation in a simple format. This app analyzes data from cities such as populations, cost of living, rental rates, crime rates, park (walk score), and many other social and economic factors that are important in deciding where someone would like to live. This app presents this information in an intuitive and easy to understand interface.</p>

<p><strong>Constraints:</strong> This is the most ambitious project I have undertaken to date. My team and I were working on a tight deadline and had several new technologies that we had to get up to speed on.</p>

<p>As the data scientist team, we were tasked with finding, cleaning, and wrangling data. We then had to create a recommendation system using machine learning. Finally, with this information create a livability index to help users make informed decisions about which city they would like to move to. We also had to make sure that this information was readable by our front end engineers.</p>

<p>While cleaning and wrangling data may seem tedious or rudimentary, for continuity across the data we had to wrangle things like how a city name should appear. Another constraint was learning new technologies on the job since our stakeholder expected AWS deployment but all the data scientists on the team only had experience Heroku.</p>

<h2> Planning:</h2>

<p><strong>Design Architecture</strong></p>

<p>The first few weeks of the project were spent entirely on planning: reading through our product documentation, getting a sense of what data was available to use (whether as datasets, APIs, or if we would have to do web scraping), facilitating data science meetings to create outlines and timelines, and most importantly: our daily meeting with our Team Lead and web developers to keep each other abreast of our planning and to ensure that our final product will be accurately aligned with our stakeholder’s vision.</p>

<p>To ensure that we were all on the same page, I designed our system architecture so we had a visual representation of the data acquisition, storage, analytics, machine learning, API gateway, and data feedback. This helped us begin with the end in mind rather than trying to build the bridge as we tried to across it.</p>

<img src = "../img/labs/architecture/CitySpire DS Architecture@2x.png" alt="System Architecture" class="center">
<h5 align="center"> System architecture for CitySpire </h5>

<br>

<p><strong>From roadmap to implementation</strong></p>

<p>Since we were limited in time and breadth of the project was pretty large, we got right to work. Using the product documentation, we created task cards using Trello so that we could make sure to meet
<a href="https://www.notion.so/CitySpire-c5071dd8198441b6999aefbbbc851ca7">release features</a>.</p>

<h2> Data:</h2>

<p><strong><em>Let the good times roll!</em></strong></p>

<p><strong>Collection, Evaluation, and Cleaning</strong></p>

<p>As the data scientists on the team, we were responsible for finding information about the cities, i.e. crime, rent, pollution, etc. We spent a lot of time gathering the data that would provide the information that the stakeholder wanted to include in his app.</p>

<p><strong>Cleaning</strong></p>

<p><strong><em>Oh, the cleaning!</em></strong></p>

<p>We had to wrangle information to make sure things such as naming conventions were the same across all the data, such as ‘Saint Louis’ vs. ‘St. Louis’, or ‘Fort Bragg’ vs. ‘Ft. Bragg’. We knew that intensive data cleaning would be necessary to ensure data integrity and continuity between each of our sources.</p>

<p><strong>Examples</strong></p>

<p><strong>Wrangle Function:</strong></p>
<script src="https://gist.github.com/jiobu1/6eab4270afd8e09cd234008c9839fb78.js"></script>
<h5 align="center"> Wrangle function for cleaning FBI crime data and allowing me to merge the 50 states into a single csv. </h5>

<br>

<p><strong>Using pipe</strong></p>

<p>Another noticing, was that several datasets had cities hyphenated, such as Albany-Schenectady-Troy, NY. In this case not only did I have to do a split on the city and state but I also had to a create a function that would separate the cities by ‘-’ and then copy the row information for each respective city.</p>

<p>There were several functions that needed to be applied, so I used a method called pipe to have my specific functions as a method on the DataFrame in method chains. This allows my fellow engineers to easily follow my process.</p>

<script src="https://gist.github.com/jiobu1/6677a4059e36ba5562e5a977b7a25123.js"></script>
<h5 align="center"> Using .pipe on pollution data </h5>

<br>

<p><strong>Evaluating:</strong></p>

<p><strong>Mapping census tract information to city</strong></p>

<p>Besides cleaning, another important part of the process is evaluating the data and then making some assumptions or decisions about the data and how to use it. One source, we relied on for getting information was the Census Bureau. This app is a city based app but census information is formatted by either tracts or county. As a data scientist, I had to translate tract information to city, and to do this there were several assumptions I had to make. I utilized a file from HUD that maps tracts to zip codes. There was a column named “res_ratio”, that mapped the percentage of a tract population living within a specific zip. I made the assumption, after discussing with my team and stakeholder, to use this ratio for things such as demographics, income, and employment numbers.</p>

<p><strong>Other decisions</strong></p>

<p>Another data driven decision was determining how to summarize certain information to disseminate to the user. Users normally want a snapshot or a succinct representation of the data and it is important to convey this information without bias. I created a summary column for crime, pollution, and diversity.</p>

<p><a href="https://www.njsp.org/info/ucr2000/pdf/calc_ucr2000.pdf">><strong>Crime Rate per 1,000 inhabitants:</strong></a> To summarize crime data, I used FBI crime reporting standard of reporting crime per 1000 residents. This represents the number of Index offenses per 1,000 inhabitants.For example: What is the crime rate for a municipality with 513 Index offenses (murder, rape, robbery, aggravated assault, burglary, larceny-theft and motor vehicle theft), with a population of 8,280? 513 (Index offenses) ÷ 8,280 (population) = .061957 x 1,000 = 62.0 (crime per 1,000 inhabitants). This was then binned into “High”, “Medium”, and “Low” using pd.qcut.</p>

<script src="https://gist.github.com/jiobu1/abca8f9854f6399f51420f557a517743.js"></script>

<p><strong>Air Quality Index:</strong> To determine overall air quality for each city, I used the median value and then created an algorithm that separated it based on the U.S. air quality index.</p>

<img src = "../img/labs/decisions/air_quality.png" alt="AQI Basics" class="center">
<h5 align="center">  AQI basics for ozone and particle pollution </h5>

<br>

<p><strong>Diversity:</strong> To summarize diversity, I use the Simpson’s Diversity Index.</p>

<img src = "../img/labs/decisions/diversity_index.png" alt="Simpson’s Diversity Index" class="center">

<ul>
<li>n = numbers of individuals of each ethnicity</li>
<li>N = total number of individuals of all ethnicities</li>
<li>The value of D ranges between 0 and 1</li>
</ul>

<p>These summary numbers of crime, pollution, diversity, along with the walkability score were then used to calculate the livability score.</p>

<h2> Model: </h2>

<p><strong>Nearest Neighbors</strong></p>

<p>A feature that our team thought would be beneficial to the user was to create a recommendation system. After conferring with our Team Lead and getting an approval, I set out to implement this feature.</p>

<p>Most people when looking to relocate, already have a city in mind but we felt that getting a recommendation of similar cities would be a way to open up other possibilities of where the user could look to move to.</p>

<p>To implement the recommendation system, I used a machine learning model called <a href="https://scikit-learn.org/stable/modules/neighbors.html">nearest neighbors</a> from sci-kit learn library. After merging the different data we collected into a dataframe, I scaled the data, so that columns such as rent and population did not get weighted heavier than percentages or decimal numbers. Then a column called nearest was added to the dataframe. This data is then returned using the get_recommendations endpoint.</p>

<script src="https://gist.github.com/jiobu1/719a1016f6e6c6fcd994d04115dd80be.js"></script>

<br>

<img src = "../img/labs/models/nn_column.png" alt="Nearest Neighbors" class="center">
<h5 align="center"> This is the column used in get_recommendations endpoint </h5>

<br>

<h2> New Tech: </h2>

<p>One of the exciting parts of working on this product was getting experience with new technology. Fortunately, both <a href="https://fastapi.tiangolo.com/tutorial/">FastAPI</a> and <a href="https://aws.amazon.com/getting-started/">AWS</a> (<a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html">RDS Postgres</a>, <a href="https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html">Elastic Beanstalk</a>) have great documentation.</p>

<p>This was where the fun really began!</p>

<h2> FastAPI: </h2>

<p><strong>Getting started</strong></p>

<p>Having worked with Flask, there were a lot of similarities that I could carry over. This article <a href="https://amitness.com/2020/06/fastapi-vs-flask/">“FASTAPI for Flask Users”</a> was very helpful.</p>

<p>To get started we needed a <a href="https://github.com/jiobu1/labspt15-cityspire-g-ds/blob/main/app/main.py">main.py</a> file which was provided in our repository, so to test it out, it was as simple as running the following series of commands:</p>

<img src = "../img/labs/endpoints/loading_fastapi/Screen Shot 2021-03-02 at 9.48.46 PM.png" alt="1. pipenv install — dev" class="center">
<h5 align="center"> 1. Install all dependencies needed for application </h5>

<br>

<img src = "../img/labs/endpoints/loading_fastapi/Screen Shot 2021-03-02 at 9.49.03 PM.png" alt="2. pipenv shell" class="center">
<h5 align="center"> 2. entering into the virtual environment where the correct dependencies are installed </h5>

<br>

<img src = "../img/labs/endpoints/loading_fastapi/Screen Shot 2021-03-02 at 9.49.19 PM.png" alt="3. uvicorn app.main:app — reload" class="center">
<h5 align="center"> 3. This deploys your FastAPI app. </h5>

<br>

<p>If you run the server and go to the endpoint <code>http://127.0.0.1:8000/docs</code>, you will get an auto-generated swagger documentation. FastAPI is easy to use and the best part is the documentation, which it grabs from your docstrings. This makes it easy to show how to work with the endpoints rather than having to go to separate documentation, like README.md (this would have been where I provided documentation and links if I was working with Flask).</p>

<h2> AWS Elastic Beanstalk: </h2>

<p>After creating and testing our basic endpoints, now we were ready to deploy the app to AWS Elastic Beanstalk. Our Team Lead provided us with with 12 digit account ID(IAM), and an AWS username & password. After installing <code>awscli</code> and <code>awsebcli</code> , we were able to run the following commands and we were in business!</p>

<ol>
<li> <code>eb init --platform docker CHOOSE-YOUR-NAME --region us-east-1</code></li>
<li> <code>eb create --region us-east-1 CHOOSE-YOUR-NAME</code></li>
<li> <code>eb open</code></li>
</ol>

<p>Then when making changes, commit your changes using git and then run eb deploy and eb open to re-deploy the application with the changes.</p>

<p>One thing we learned the hard way, was when connecting with the front end we needed to be sure to configure the correct ports so information can be received from HTTPS traffic. We had to create a 443 Listener Port when the default setting only has a Port 80, which only allows HTTP traffic.</p>

<img src = "../img/labs/aws/elastic_beanstalk.png" alt="Ports for AWS Elastic Beanstalk" class="center">
<h5 align="center"> Ports needed for HTTP and HTTPS traffic </h5>

<br>

<h2> AWS RDS Postgres: </h2>

<p>Creating a database with AWS RDS was very straightforward. I signed in to the AWS Console and chose the region. Then I just went through the standard create and chose a PostgreSQL, and I was able to create the database instance with their default settings.</p>

<p>When developing locally, I used <a href="https://pypi.org/project/python-dotenv/">python-dotenv</a> to load an <code>.env</code> file. (The <code>.env</code> file is listed in <code>.gitignore</code>.) This made sure that the my username and password were protected. When deploying, we used the Elastic Beanstalk console for <a href="https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-cfg-softwaresettings.html#environments-cfg-softwaresettings-console">configuring environment variables</a> there.</p>


<img src = "../img/labs/aws/rds_postgres.png" alt="Loading environment variables" class="center">
<h5 align="center"> Include Database name and url — connects to Elastic Beanstalk </h5>

<br>

<h2> API: </h2>

<p><strong>Endpoints:</strong></p>

<p>Having successfully established a connection to the database and deployed the basic API to AWS, the next steps were in building out the API itself, including the routes (endpoints), functions, and documentation for usage.</p>

<img src = "../img/labs/endpoints/endpoints.png" alt="Image for post" class="center">
<h5 align="center"> Data Science Endpoints </h5>

<br>

<p>For the DS team, I created the API endpoints for the <a href="https://github.com/jiobu1/labspt15-cityspire-g-ds/blob/main/app/ml.py">nearest neighbors model</a> and the <a href="https://github.com/jiobu1/labspt15-cityspire-g-ds/blob/main/app/viz.py">visualization</a> endpoints.</p>

<img src = "../img/labs/endpoints/nearest_neighbors.png" alt="Nearest Neighbor endpoint" class="center">
<h5 align="center"> Nearest Neighbors API endpoint — pulls docstring from .py file. Shows successful response and error messages. </h5>

<br>

<p><strong>Visualization</strong></p>

<p><strong>A picture is worth a thousand words.</strong></p>
<p>Below are the interactive plotly visualization endpoints that I created. These endpoints enables users to see data points presented visually as well as dive deeper into each statistic. Using charts or graphs to visualize large amounts of data makes it easier to understand.</p>


<div class = "video-container">
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plotly.com/~jiobu1/25.embed"></iframe>
<h5 align="center"> /api/demographics_graph — shows percentage from each race category </h5>


<div class = "video-container">
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plotly.com/~jiobu1/27.embed"></iframe>
<h5 align="center"> /api/employment_graph — breaks down industry sectors and employment type </h5>


<div class = "video-container">
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plotly.com/~jiobu1/31.embed"></iframe>
<h5 align="center"> /api/aqi_graph — goes more in depth into air quality for each city </h5>

<br>
<br>

<h2> Final Thoughts: </h2>

<p>Working on this project has been both exciting and rewarding. I was excited about learning on the job and was glad that my training and education, thus far, set me up for success for working with AWS and FastAPI. I was pleasantly surprised at how easy AWS makes it to deploy and manage web apps, and impressed with FastAPI and its built in features that make using it for creating DS endpoints a no brainer.</p>

<p>Another important lesson I learned from this process is when working with stakeholders and a large team, constant communication is key. It was important to utilize the product scope to make sure that we were lock step in achieving the vision that the stakeholder had for CitySpire, and not our false assumptions. Also, communication was important in making sure each team member knew what they were responsible for. Creating the system architecture and trello board provided us an important road map as well as kept us accountable.</p>

<p>CitySpire is the most ambitious project I have undertaken. I learned many things such as scoping a project, communication, and being open to learning on the job. As such, it provided to me many valuable, foundational experiences that I’ll surely keep with me throughout my entire career.</p>

<p><strong>Potential Improvements:</strong></p>

<p>Thus far in the project, we’ve been able to implement rental price estimates, walk score, crime, population, pollution, and a livability score on the DS API as well as the front-end web site. Some possible next steps are:</p>

<ul>
<li>Rental prediction model</li>
<li>Population prediction model</li>
<li>Pollution prediction model</li>
<li>Incorporating prevalent industries for each city</li>
<li>Integrating other data points, such as bus and bike scores</li>
<li>Implementing visualization APIs</li>
<li>Creating a NLP recommendation system, where users can set criteria and receive results based on their query</li>
</ul>

<p><strong>Team Overview:</strong></p>

<p>I worked for 8 weeks (part time) as a data scientist and machine learning engineer on a remote interdisciplinary team to create CitySpire. </p>

<img src = "../img/labs/team/Screen Shot 2021-02-28 at 10.04.26 PM.png" alt="Team" class="center">
<h5 align="center"> CitySpire — Team G </h5>

<br>

<p>I worked with an amazing team. The team included myself and three other data scientists, three web developers, one UX designer, and our Project Lead who kept up our energy and made sure we were moving in the right direction. Everyone was hardworking and immediately rolled up their sleeves ready to work. (Our team did such a good job, that out of the 8 other teams, our repository was chosen for future cohorts to build upon.)</p>

<p><strong>Role:</strong> App Architect, Data Scientist, Machine Learning Engineer, Data Visualization</p>

<p><strong>DS Tech Stack:</strong> Python, FastAPI, AWS RDS Postgres, AWS Elastic Beanstalk, Docker</p>

<p><strong>Timeline:</strong> 8 weeks (part-time)</p>

<p>Our final Github repository can be found <a href="https://github.com/jiobu1/labspt15-cityspire-g-ds">here</a>.</p>

<h2> Demo: </h2>
<div class = "video-container">
<iframe width="560" height="315" src="https://www.youtube.com/embed/WSLJawDw1aQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<h5 align="center"> CitySpire API Demo and DS Architecture </h5>

<br>
<br>

<h2> Links: </h2>

<p><strong>Data Sources:</strong></p>

<ul>
<li>US Census — https://www.census.gov/</li>
<li>FBI Crime Data — https://ucr.fbi.gov/crime-in-the-u.s/2019/crime-in-the-u.s.-2019/tables/table-8/table-8-state-cuts/arizona.xls/@@template-layout-view?override-view=data-declaration</li>
<li>Pollution Data — https://aqs.epa.gov/aqsweb/airdata</li>
<li>Rental Data — https://www.huduser.gov/</li>
<li>Walkscore — https://www.walkscore.com/</li>
</ul>
