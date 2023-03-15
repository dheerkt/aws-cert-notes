# AWS Certified Machine Learning Specialty MLS-C01 Cheat Sheet

## Streaming Data Collection

* Amazon Kinesis Family Overview
    * Kinesis Data Streams — collect and process large streams of data records in realtime
    * Kinesis Data Firehose — deliver streaming data to various data sources
    * Kinesis Video Streams — stream live videos from devices to the cloud, and build realtime applications around these streams
    * Kinesis Data Analytics — run SQL queries in realtime over streaming data
* Kinesis Data Streams
    * Overview
        * Kinesis Data Streams aggregate realtime data and load it to other services like EMR/Redshift
        * Managed service thats durable (won’t lose data) and elastic (scales up or down)
        * Allows for parallel applications reading from the data streams, which differentiates it from queueing services
    * Process
        * Data producers (i.e. logs, social media streams, video streams, etc.) push data onto the Kinesis Data Streams
        * Shards package and aggregate the data and send it to data consumers
            * Default limit is 500 shards, but it can be increased 
        * Data consumers are processing tools such as Kinesis Data Analytics, Kinesis Data Firehose, EMR, EC2, Lambda etc.
            * Can process, analyze or create dashboards to visualize the data
        * Storage
            * Storage isn’t necessary for streaming data as data gets less valuable over time
            * But different storage options are available, such as data lakes on S3, data warehousing on Redshift, or DynamoDB
    * Shards
        * Each shard consists of a sequence of data records, which can be ingested at 1000 records per second
        * All data within a shard shares the same partition key, and has a unique sequence number
        * Data payload per record can be up to 1MB, but most cases will be a few Kilobytes
            * 1 shard can process 1 MB/sec input data, and 2 MB/sec output data
            * 2 shards can process 2 MB/sec input data, and 2 MB/sec output data
            * and so on...
        * Scaling up shards is the way to scale Kinesis streams (e.g. add more shards if user activity increases)
        * Shards are transient data storage
            * Data records are stored for 24h by default, and can be extended up to 365 days
                * Extended data retention enables storage for 7 days
                * Long-term data retention enables storage for up to 365 days
    * Reading To/Writing From Kinesis Streams
        * Kinesis Producer Library (KPL) — Easy to use library to write to a Kinesis Data Stream
            * Uses Kinesis API under-the-hood, providing a layer of abstraction specific for ingesting data
            * Asynchronous, but includes an automatic and configurable retry mechanism for failure
            * Processing delays can occur for higher packing efficiencies and better performance
            * Only usable with a Java wrapper — cannot use any other language
        * Kinesis Client Library (KCL) — Integrated with KPL for consumer applications to consume/process data from Kinesis Data Stream
        * Kinesis Agent — Pre-built Java application to be installed on Linux servers
        * Kinesis API (AWS SDK) — API operations to send data to Kinesis Data Stream using PutRecord or PutRecords
            * Uses PutRecords and GetRecords to manually handle streams
            * No delays in processing — synchronous
            * Usable through any AWS SDK
    * Kinesis Data Firehose
        * Managed, serverless streaming application to load data into a few options (mostly other AWS services)
        * Receives records from multiple producers and can transform the data before storing or delivering to data consumers
            * Data producers can produce records as large as 1000KB
            * Can copy raw data to transform it in a Lambda function before storing in S3, while also storing raw data in a different bucket
            * To store in Redshift, data will always be stored in S3 first and then Firehose issues a COPY command to load data onto S3
            * Can store in Redshift, S3, ElasticSearch, Splunk, and to an HTTP Endpoint (so data can be indirectly sent to SNS/API Gateway as well)
        * Firehose buffers and compresses all data you push to it, causing a short delay
            * Buffer Interval — Ranges from 60 seconds to 900 seconds
            * Buffer Sizes
                * S3 — 1MB to 128MB
                * Elasticsearch — 1MB to 100MB
                * Redshift — depends on how fast your cluster can finish the COPY command
                * Splunk — 5MB buffer size, and 60s interval
        * Firehose Data Streams can be encrypted using KMS
    * Kinesis Video Streams
        * Uses Kinesis Video Stream Libraries 
            * Manage media sources and stream lifecycle as data flows from source to Kinesis Video Streams
            * Installed on-device to securely connect to Kinesis Video Streams
            * Can do real-time video analysis or batch-oriented video analysis after storing videos durably
    * Kinesis Data Analytics
        * Run SQL queries on streaming data
        * Can join streaming data with data in S3
        * Query output can be streamed to Kinesis Data Streams/Firehose and sent to other data consumers
* Amazon Managed Streaming for Kafka (MSK)
    * Apache Kafka — open source streaming data service, using ZooKeeper
        * Publish/subscribe to streams
        * Store streams in a durable way — Unlimited durability
        * Process streams as they occur — Very low latency
    * Amazon Managed Streaming for Kafka
        * With the managed service, you don’t need to:
            * Manage cluster
            * Ensure software updates
            * Ensure high availability
        * Structure
            * VPC
                * Broker Nodes
                    * Live in different availability zones (for high-availability)
                    * Contain streaming data
                    * Subscribed to by producer and consumer nodes outside the VPC
                * ZooKeeper Nodes — coordinate data between broker nodes
                    * Topic Creators
                    * Take care of different partitions
        * Comparisons to Kinesis Data Streams
            * Throughput provisioning (with Shards) or cluster provisioning
            * Seamless scaling for KDS, but MSK scaling is not seamless to client
            * KDS stores data for 7 days max, but MSK can for unlimited time
            * KDS has deep AWS integration, whereas MSK has 3rd party tooling
            * MSK is the most low-latency streaming solution
            * KDS has maximum payload of 1MB, whereas MSK provides configurable data payload sizes up to 6MB

## Data Preparation

* Concepts
    * Data preparation involves transforming a dataset using different techniques to prepare it for model training and testing
        * Remove incomplete values and duplicates, reformat incorrectly formatted values, remove irrelevant data, break observations into tokenized chunks, etc.
    * Two major data transformation services are Amazon SageMaker Jupyter notebooks and AWS Glue ETL jobs
* Categorical Encoding — changing text values into numbers so that ML algorithms can understand them better
    * Terminology — categorical variable = categorical feature = discrete feature
    * Types of categories
        * Nominal
            * Multicategorical — { blue, green, yellow }
            * Bicategorical — boolean values
        * Ordinal categories — { L > M > S }
    * One-Hot Encoding — transforming nominal categories by creating new binary columns for each observation
        * Instead of a single categorical feature such as color = Blue (with possible options of Blue, Green, or Yellow), you would have three features: Blue = 1, Green = 0, Yellow = 0
        * One-hot encoding could exponentially grow dataset if there are too many categories, so it is not the best option for all nominal category encoding cases
            * Possible solutions to reduce the number of categories include grouping similar categories before OHE, or mapping rare values to “Other” category
* Text Feature Engineering — transforming text so ML algorithms can understand it
    * Breaking down large text corpus into words
        * Bag-of-Words — breaks up whitespace into single words (eg. “how are you” → {“how”, “are”, “you”}
        * N-gram — breaks up text into <=N words (eg. 2-gram of “how are you” → {“how are”, “are you”, “how”, “are”, “you”}
            * Used to find combinations of words
        * Orthogonal Sparse Bigram (OSB) — Creates groups of words that always include the first word (eg. OSB of “how are you” → {“how_are”, “how__you”, “are_you”}
            * The number of underscores shows number of whitespace from the first word
        * Term Frequency - Inverse Document Frequency (tf-idf) — Shows popularity of words in a text corpus by making common words like “the” or “and” less important
            * Vectorized tf-idf = (number of documents, number of unique n-grams)
    * Other transformations used to standardize text/words
        * Removing punctuation
        * Lowercase transformation
    * Cartesian product for text can be used to create associations between disparate categories
        * Eg. Used on e-commerce sites to suggest similar products {ML Analysis Book, Softcover} cartesian product → {ML_Softcover, Analysis_Softcover, Book_Softcover}
    * Date/Time Feature Engineering — extract more information from a datetime object
        * Date=2020-08-08 → YY=2020,MM=08,DD=08,DayOfWeek=7, IsWeekend=T
* Numeric Feature Engineering — changing numbers so ML algorithms can perform computations faster
    * Feature scaling — changes numeric values so all values are on the same scale
        * Normalization — assign smallest value to 0, largest value to 1, and scale the rest accordingly between 0 and 1
            * Outliers throw off normalization, they need to be removed before normalizing a dataset
        * Standardization — If outliers cannot be removed, standardization sets the average value to 0, and scales the rest based on z-score and standard deviation
        * Values can always be scaled back after calculations are done
    * Binning — changes numeric values into groups or bins
        * Quantile binning — attempts to place the same number of values in each bin, grouping in equal parts
* Other Feature Engineering — representing different media types with numerals for ML algorithms to process
    * eg. Image feature engineering, audio feature engineering
    * Dataset Formats — can be either file formats, or pipe format
        * File format loads all data from S3 directly onto training instance volumes, with formats such as CSV, JSON, Parquet, and image files (.png and .jpg)
        * Pipe datasets are streamed directly from S3 with recordIO-protobuf format, which creates a tensor (multidimensional array) with the data
            * Skips downloading dataset, allowing training jobs to start sooner, finish quicker, and need less disk space
            * Pipe mode now also supports CSV format
* Handling Missing Values
    * Missing data can be represented as null, NA, NaN, etc.
    * Understand why values are missing in the first place, and try to rectify that if possible
    * Missing-ness Mechanisms — data may be missing in the following ways:
        * Missing at random (MAR)
        * Missing completely at random (MCAR)
        * Missing not at random (MNAR)
    * How to handle missing values
        * Supervised learning — most difficult, but may yield the best results
            * Predict missing values based on the values of other features of that row
        * Mean/Median/Mode — quick and easy, but results can vary
        * Dropping rows — easiest, but it can dramatically change datasets
* Feature Selection — deciding which columns to keep or get rid of before ML algorithm processing
    * ***Intuitive*** step that humans take to reduce the number of features
* Principle Component Analysis (PCA) — unsupervised learning algorithm that reduces the number of features while retaining as much information as possible
    * Feature selection removes obviously useless features that do not help solve the problem, whereas PCA reduces total number of features for data that’s too large
* AWS Data Preparation Helper Tools
    * AWS Glue — serverless, fully-managed ETL service for longstanding/repetitive transformations
        * Run PySpark — Python or Scala code — to transform data on a schedule or on-demand
        * Python Shell also allows running traditional Python scripts for small datasets
        * Built-in PySpark ETL transforms to increase efficiency/reduce overhead
        * Zeppelin & Jupyter notebooks for interactive development
        * Used for vast majority of ETL cases—integrates with S3, Redshift, RDS, DynamoDB, and on-premise DBs
    * Amazon SageMaker — build & train ML models
        * Allows you to spin up Jupyter notebooks for quick transformations
    * Amazon EMR — fully managed Hadoop cluster ecosystem
        * Distribute workloads over many EC2 instances for PB-scale data transformations
        * Big data transformation is possible if you’re already working with EMR, but in most cases Glue/SageMaker may be easier/faster with reduced operational overhead
        * Apache Spark, Hive, Pig, Presto and Jupyter notebooks — also integrates directly with Amazon SageMaker & AWS Glue
    * Amazon Athena — run SQL queries on S3 data using AWS Glue Data Catalog
    * AWS Data Pipeline — process & move data between AWS compute services
        * Not used for an ETL job in most cases
        * But can transform data using languages other than Python & Scala like Java

## Data Analysis & Visualization

* Different categories of visualization
    * Relationships — i.e. trends/correlation
        * Scatter plots — plot relationships between two values
        * Bubble plots — plot relationships between three values
            * Both allow us to see correlation and trends between values
    * Comparisons
        * Bar charts — use bars to mark and compare single variable values
        * Line charts — lines to compare one or more variables changing over time
    * Distributions — eg. finding outliers
        * Histograms — compares values that are grouped into bins
        * Box plots — shows lowest/highest values, the range where most values fall, and outliers
        * Scatter plots can also show clustering and distribution of data
    * Compositions — what makes up our data
        * Pie charts — show how various values compare as a whole
        * Stacked area charts — measurement of items over longer periods of time
        * Stacked column charts — quantity of various items over shorter periods of time
    * Misc
        * Heatmaps — represent values as color
* Tools for Visualization
    * Developer Tools — Python/R libraries within Amazon SageMaker or other Jupyter notebooks
    * Business Intelligence (BI) Tools — Amazon QuickSight or Tableau

## Modeling

* Modeling is finding a generalization (not memorization) to a large problem.
    * There are many steps involved in building an ML solution — choosing the right algorithm, stacking data prep, training, and testing processes on each other
        * What type of generalization are we seeking? — forecasting, computer visualization, image recognition, nlp
        * Do we really need ML? — will linear regression or IF...THEN logic suffice?
        * How will my ML generalizations be consumed? — APIs
        * What do we have to work with? — too much data, too little data, signal-to-noise ratio
        * How can I tell if the generalization is working? — testing accuracy and effectiveness, sensitivity to false positives/negatives (learn the confusion matrix)

* Train/Test Split
    * ~70-80% of data should be used to train the model, and the remaining should be held back to test if the model works
    * Data preparation pipeline should be Randomize → Split → (Fold) → Train → Test
        * Data should be randomized before being split into training dataset and testing dataset
        * K-fold cross validation splits data into K groups. It validates that data is randomized by picking 1 group as test set, and using rest of the groups as training data. This is done over multiple rounds so that each of the k groups get a chance to be a test set. 
            * Error rates across k groups are compared to check if data is sufficiently randomized
    * For time-series data, time has influence over the outcome, so use sequential split strategy (leave some of the latest data for testing, don’t randomize)
* SageMaker modeling
    * Most SageMaker algos accept CSV, ensure S3 data Content-Type is text/csv. 
    * For unsupervised algorithms, specify the absence of labels by setting Content-Type as “text/csv;label_size=0“
    * For optimal performance, use protobof recordIO format to use Pipe mode and stream data from S3
    * CreateTrainingJob API
        * Two options to access API
            * High-level Python library provided by Amazon SageMaker
            * SDK for Python
        * Conditions/Parameters for CreateTrainingJob API
            * Specify training algorithm
            * Specify algorithm-specific hyper-parameters
            * Specify input & output configuration
        * Hyperparameters vs. Parameters — Hyper-parameters are set before learning process begins; whereas Parameters are values derived from the learning process
* SageMaker training
    * Instances
        * CPUs & GPUs are most flexible compute engines; GPUs are generally more expensive and high-powered, whereas CPUs 
        * ASICs are specialized hardware where model has been “burned in”; most performant but impossible to change
        * FPGAs are compute engines with specialized chips; more performant than CPUs and GPUs but expensive to change
    * SageMaker images
        * SageMaker has common algorithms stored in ECR repositories
        * Image repository types
            * Training Images
                * Train on high-powered GPU instance
                * Model is not production-ready; needs to “learn” more
                * CreateTrainingJob API call
            * Inference Images
                * Deploy on less-costly CPU instance
                * Model good enough for production
                * CreateModel API call
        * Image ECR repositories are regional, use the one closest to you for lowest latency
        * Image repository versioning
            * :1 tag for stable version (recommended for production)
            * :latest for latest version (may be unstable or not backwards-compatible)
        * Recommended instance types and and input file types are provided by AWS for each image repo
        * Custom algorithm images may also be stored in ECR
            * Can be uploaded using docker or nvidia-docker (for GPU-optimized images) GPU
    * CloudWatch Logs during Training Process, which are also accessible from SageMaker console
        * Logged info
            * Arguments provided
            * Errors during training
            * Algorithm accuracy statistics
            * Algorithm timing
        * Common errors 
            * Specifying invalid/extra hyperparameters
            * Incorrect input file formats
            * 

## Algorithms

    * Concepts
        * Definition — An algorithm is a bunch of unambiguous repeatable steps to consistently solve a class of problems
        * Supervised learning
            * Dataset is labelled
            * Model must be trained
        * Unsupervised learning
            * Unlabelled dataset
            * Model learns “on the job”; no formal training
        * Reinforcement learning
            * Model is trained to maximize reward
    * Regression
        * Linear Learner Algorithm — minimizes error using stochastic gradient descent
            * Supervised
            * Pros/Cons
                * Very flexible — well suited for discrete or continuous inferences
                * Good first choice — used to explore different objectives 
                * Built-in tuning — internal mechanism for tuning hyper-parameters 
            * Use cases
                * Regression — predict quantitative values based on given numeric input
                * Discrete binary classification (eg. Yes/No)
                * Discrete multi-class classification (eg. Email/Phone Call/Text)
        * Factorized Machines Algorithm — regression algorithm for high-dimensional sparse datasets
            * Recommender model
            * Supervised
            * Pros/Cons
                * Considers only pair-wise features
                * CSV not supported
                * Doesn’t work for multi-class classification
                * Really needs LOTS of data (10,000 to 10,000,000 feature input space recommended)
                * CPUs are better for sparse data
                * Don’t perform well on dense data
            * Use cases
                * High-dimensional sparse data — Lots of rows but missing columns
                * Recommendations (eg. Netflix)
    * Clustering
        * K-means Algorithm — takes a list of things with attributes, and groups things according to similar attributes in K clusters
            * Unsupervised
            * Pros/Cons
                * Expects tabular data
                * Define identifying attributes
                * CPU instances recommended — or only 1 GPU
                * Training is still a thing — to be sure your model is accurate
                * Define a number of features and clusters
    * Classification
        * K-nearest neighbor — predicts value based on what the K objects that you’re closest to (or average values of those you’re closest to)
            * Supervised
            * K-means Algorithm can be applied when you don’t know what features are most impactful and can’t use clustering
            * Pros/Cons
                * You choose k value of neighbors
                * Lazy algorithm — training data is used not to generalize but to figure out what’s nearby
                * Training data stays in-memory
                * Beware of bias... known for stereotyping
            * Use cases
                * Credit ratings
                * Product recommendations
        * Image Analysis models — classifies based on images fed into model
            * Supervised
            * Usually returns a confidence % level
            * We can set an accuracy threshold based on this % value to filter out inaccuracies
            * Subclasses of Image Analysis algorithms
                * Image Classification — determines classification of an image
                * Object Detection — detects specific objects inside an image and assigns confidence score
                * Semantic Segmentation — identifies edges of objects
                    * Accepts PNG input
                    * Only supports GPU training
                    * Can be deployed to either CPU or GPU
            * Use cases
                * Computer vision systems
                * Image metadata extraction
    * Anomaly Detection
        * Random Cut Forest — find occurrences significantly beyond normal distribution (3+ s.d.) that could mess up model training
            * Unsupervised
            * Gives anomaly score to data points (low means normal, high means anomaly)
            * Scales well with size of data
            * Doesn’t benefit from GPUs
            * Use cases
                * Quality control
                * Fraud detection
        * IP Insights — can flag odd online behavior for review
            * Unsupervised
            * Pros/Cons
                * Ingests entity/IP address pairs
                * Returns inference via a score
                * Uses neural network
                * GPUs recommended for training; CPUs for inference
            * Use cases
                * Tier-ed authentication models
                * Fraud detection
    * Text Analysis
        * Latent Dirichlet Allocation (LDA) — find how similar documents are
            * Unsupervised
            * Use cases
                * Recommend articles on similar topics which you might have read in the past
                * Musical influence modeling
        * Neural Topic Model (NTM) — similar use and function to LDA, but different algorithm which might yield different result
        * Sequence-to-Sequence — Language translation engine that takes input text and predicts what it might be in another language. Must supply training data and vocabulary.
            * Supervised
            * Pros/Cons
                * Intense training, only GPU instance are supported
                * Commonly initialized with pre-trained word libraries
                * Steps consist of embedding, encoding, and decoding
            * Use cases
                * Language translations
                * Speech to Text
        * BlazingText — optimized way to determine contextual semantic relationships between words in text
            * Supervised (Text Classficiation) or Unsupervised (Word2Vec)
            * Pros/Cons
                * Expects single preprocessed text file
                * Highly scalable
                * Faster than FB’s FastText
            * Use cases
                * Sentiment analysis — Amazon Comprehend
                * Classify documents — Amazon Macie
        * Object2Vec — map out things in d-dimensional space to figure out how similar they might be to each other
            * Supervised
            * Ellaboration of Word2Vec
            * Pros/Cons
                * Expects data in pairs
                * Feature engineering
                * Training data is required
            * Use cases
                * Recommendation
                * Movie rating prediction
                * Document classification
    * Reinforcement Learning — find path to greatest reward
        * Use cases
            * Autonomous vehicles
            * Intelligent HVAC control
    * Forecasting
        * DeepAR — predict point-in-time values and estimated values over a time-frame and use multiple sets of historic data
            * Supervised
            * Cold-start problem — no historical data, so DeepAR allows us to combine multiple datasets of similar items to predict performance of new items
            * Pros/Cons
                * Supports various time series
                * More time series is better
                * Must supply at least 300 observations
                * Must supply some hyperparameters — context length, epochs, prediction length,  time frequency
                * Automatic evaluation of model
            * Use cases
                * Predict new product performance
                * Predict labor needs
    * Ensemble Learning — use multiple algorithms to improve model accuracy
        * Extreme Gradient Boosting (XGBoost) — swiss-army knife for all sorts of regression, classification, and ranking problems
            * Supervised
            * Very flexible — only 2 required hyperparameters, and 35 optional
            * Uses decision trees to improve on regression
            * Pros/Cons
                * Accepts CSV and libsvm
                * Memory-bound/memory-intensive
                * Only CPU training
                * Spark integration — can call directly from Spark environment
            * Use cases
                * Ranking products or search results
                * Fraud detection

## Evaluation and Optimization

    * Evaluation feedback loop: Define evaluation metric → evaluate → tune model → repeat
        * Offline validation — done using test set of data
            * Eg. K-fold validation
        * Online validation — done using real-world conditions
            * Eg. Canary deployment or A/B testing
                * Canary deployment is sending a small portion of real-world data to new environment
    * Algorithm metrics
        * Training metrics — used during training process
            * Have a test-prefix
        * Validation metrics — used during test 
            * Have a validation-prefix
        * SageMaker algorithms automatically sends logs to CloudWatch
            * Use logs to monitor these metrics and fine-tune
    * Evaluating model accuracy
        * Underfitting — model isn’t reflective of underlying data
            * Resolution: introduce more data, or train for longer
        * Overfitting — model is too dependent on specific training data, and memorizes instead of generalizing
            * Denoted by low training error but high testing error
            * Resolutions: 
                * Add more data
                * Sprinkle noise to generalize model
                * Regulate data by creating constraints around weights
                * Reduce training time
                * Try ensembles — amplify individual weaker models and smooth out strong models
                * Ditch some features — too many features may drown signal with noise
        * Regression accuracy
            * Residuals — difference between predicted and actual values
                * Shows whether you’re consistently predicting higher or lower than actual values
                * RMSE measures this — Root Mean Squared Error
        * Binary classification accuracy
            * False positives — Type I error
            * False negatives — Type II error
            * Area under the curve metric
                * Precision —> True positives / (True positives + False negatives)
                    * Legit emails get blocked
                * Recall —> True positives / (True positvies + False positives)
                    * Spam gets through
                * F1 score — balance between precision and recall, a larger value indicates better predictive accuracy
                    * 2 * (Precision * Recall) / (Precision + Recall)
        * Multi classification accuracy
            * Compute F1 score heatmaps
        * Improve model accuracy
            * Collect more data
            * Improve feature processing so that features are more representative
            * Model parameter tuning — adjust hyperparameters
    * Model Tuning — making small hyperparameter adjustments to improve performance/reduce error
        * SageMaker offers automatic model tuning
            * Choose a tunable hyperparameter — only some can be autotuned, consult documentation
            * Choose range of values for that parameter
            * Choose objective metric that auto-tuning seeks to optimize
            * Uses bayesian optimization
            * Not a perfect solution and may result in a weaker model

## Implementations and Operations

    * Types of Usage
        * Offline Usage
            * Make inferences in batch and return results as a set
            * Entire dataset is required before using as an input
            * Eg. predictive models with large historic dataset inputs
        * Online Usage
            * Make inferences on-demand via API or service
            * Not viable for in-memory algorithms
            * Eg. realtime fraud detection or autonomous machines
    * Deployment Types
        * “Big Bang”
            * Highest risk
            * Deploy everything at once
        * Phased rollout
            * Deploy new models incrementally
            * Lower risk, lower cost
        * Parallel adoption
            * Run both models at once and ensure everything runs smoothly
            * Lowest risk, highest time, highest cost
            * Sometimes risk may be higher due to concurrency or data sync issues
    * Common Approaches
        * Rolling deployments — risk is multiple versions in production 
        * Canary deployments — direct small amount of traffic to new version to see what happens
        * A/B testing — run both at once, send half the traffic to A and other half to B
    * Use CI/CD and more CD to deploy
        * Continuous integration — merge code back to main branch frequently with testing as you go
        * Continuous delivery — automate release process so you can deploy at the click of a button
        * Continuous deployment — each code change is tested and deployed with no human intervention required
* AI Developer Services
    * Overview
        * Easy to use without ML knowledge
        * Scalable, robust, and fault-tolelerant
    * Amazon Comprehend — NLP service to find relationships within text, often used for sentiment analysis
    * Amazon Forecast — Combines time-series data to deliver highly-accurate forecasts (DeepAR algorithm)
    * Amazon Lex — Build conversational interfaces to create chatbots
    * Amazon Polly — Text-to-speech service
    * Amazon Personalize — Recommendation engine based on demographics and behavioral data
    * Amazon Rekognition — Rollup of object detection and image recognition algorithms, recognizes facial expressions and does face recognition
    * Amazon Textract — Scan documents, extract context, OCR
    * Amazon Transcribe — Transcribe/speech-to-text
    * Amazon Translate — Translates languages using seq2seq algorithm

* Amazon SageMaker Deployments (Inferences)
    * Offline Usage (Batch Transform)
        * Create model using ECR image, train data, export model to S3
        * Inference results are dropped off in Amazon S3
    * Online Usage (Hosting Services)
        * Create a Model → Configure endpoint → Create endpoint
        * Training model
            * Call ECR registry, train data, export model artifacts to Amazon S3
            * Deploy model by specifying path to S3 objects, and specifying path to ECR inference image
            * Create an endpoint to direct traffic to deployed model instance
            * Retrain after model is deployed by creating a v2 inference image
                * Create production variant for each version
                    * Contains instance_type and instance_weight for traffic
                * Endpoint directs traffic to the production variants based on instance weights
    * Can create Inference Pipelines to chain different algorithm containers in both realtime and batch scenarios
    * SageMaker Neo allows us to optimize models for different architectures
        * Optimizes for IoT devices, or nVidia instances, or SageMaker hosting
    * Elastic Inference — turbo boost for inference models and instances to deal with bursts of increased traffic
    * SageMaker supports autoscaling — minimum and maximum instances must be defined, along with a cooldown period
    * High availability — deploy multiple instances in separate AZs to automatically deal with failure
* Non-SageMaker deployments
    * Generate model using SageMaker → output artifacts to S3 → manually deploy with the following options.
    * Deployment options
        * ECS
        * EC2
        * EMR
        * On-Premise
* Security
    * Visibility
        * VPC endpoints
        * NACLs
        * Security Groups
    * Encryption
        * KMS — S3 bucket is encrypted at-rest, whereas HTTPS encrypts in-transit by default
    * Notebook Instances
        * Internet enabled by default
        * You can disable internet access but must use NAT Gateway to download libraries
        * Designed for single users, and can use IAM to restrict users to certain notebooks