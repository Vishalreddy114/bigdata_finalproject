# Bigdata_finalproject
This repo is to Process text using Databricks community edition and pyspark.

# Input source file:
https://www.gutenberg.org/files/2852/2852-0.txt

# Steps followed :
  - Injecting the data
  - Cleaning
  - Processing
  - Charting
  
# Tools and Languages:
   - Databricks Community Edition
   - PySpark
   - Python Programming Language
   - Spark Processing Engine
   
# Link to the databricks notebook : 
  https://community.cloud.databricks.com/?o=7560057972792803#notebook/3337096094382281/command/203990017418965
  
 # Commands Used in this Project :
  ##  For Data Injection :
   - In order to read the data from the url we need to import urllib.request.
   - Using the library we can store the data in a temporary file.
  
         import urllib.request 
         stringInURL = "https://www.gutenberg.org/files/158/158-0.txt"
         urllib.request.urlretrieve(stringInURL,"/tmp/gutenberg.txt")
         
   - Using the following command we can move the temp file in databricks storage folder using dbutils.fs.mv
   
         dbutils.fs.mv("file:/tmp/gutenberg.txt", "dbfs:/data/gutenberg.txt")
         
   - Then we need to transfer the file to spark using sparkContext.

         rawRDD= sc.textFile("dbfs:/data/gutenberg.txt")
         
   ## Cleaning the data :
         
   - First we need to split the text according the space and to convert them into lowercase.

         rawRDD = rawRDD.flatMap(lambda eachLine: eachLine.lower().strip().split(" "))
   
   - After splitting the data we need to remove the punctuations from the text by using regualar expression.
    
          import re
          aftercleanedrawRDD = rawRDD.map(lambda w1: re.sub(r'[^A-Za-z]', '', w1))
          from pyspark.ml.feature import StopWordsRemover
          remover = StopWordsRemover()
          stopwords = remover.getStopWords()
          rawRDD = aftercleanedrawRDD.filter(lambda word: word not in stopwords)
          
   - Using the following command we can remove empty space from the data.
         
          removedspacerawRDD = rawRDD.filter(lambda x: x != "")
          
   ## Processing the data:
   - We can map the words to key value pairs by using the following command.
       
          KVPairsRDD= removedspacerawRDD.map(lambda word: (word,1))
          wordsCountRDD = KVPairsRDD.reduceByKey(lambda acc, value: acc+value)
          
   - We can display the 20 results of the word count by using the following command.

         results = wordsCountRDD.map(lambda x: (x[1], x[0])).sortByKey(False).take(20)
         print(results)
         
   - The following command gives the entire word count in the text.
            
          finalresults = wordsCountRDD.collect()
          print(finalresults) 
          
  ## Charting the data:
  - In order display the results in the form a chart we can do it by using the following commands.
   
         import pandas as pd
         import matplotlib.pyplot as plt
         import seaborn as sns
 
  - preparing chart information
        
        source = 'The Project Gutenberg EBook of Emma, by Jane Austen'
        title = 'Top Words in ' + source
        xlabel = 'Words'
        ylabel = 'Count'
 
        df = pd.DataFrame.from_records(output, columns =[xlabel, ylabel]) 
        plt.figure(figsize=(20,4))
        sns.barplot(xlabel, ylabel, data=df, palette="cubehelix").set_title(title)
         
  # Result:
        
   ![result](https://github.com/Vishalreddy114/bigdata_finalproject/blob/main/chartingbigdata.PNG)
        
    
     
