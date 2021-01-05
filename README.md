# Customer Segmentation with RFM Model and Clustering
![cover](images/cover.png)
## Introduction
Customer segmentation is the technique of dividing customers into different segments based on specific purchase patterns and identify which group are the most profitable groups for further marketing strategy. In segmenting customers, various criteria can also be used depending on the market, such as geographic, demographic characteristics, or behavior patterns. This technique assumes that groups with different features require different approaches to marketing. 
In this project, I would be using the Retail transaction and promotion response data from [kaggle](https://www.kaggle.com/regivm/retailtransactiondata#Retail_Data_Transactions.csv), and would use RFM quantiles and K-Means & Hierarchical clustering method to perform the segmentation. 

## What is RFM?
RFM is one the most popular and handy model for customer segmentation for both online and offline retailers. According to Wikipedia, RFM is an acronym of recency, frequency and monetary.

- **Recency** is about when was the last order of a customer. It means the number of days since a customer made the last purchase. If it’s a case for a website or an app, this could be interpreted as the last visit day or the last login time.

- **Frequency** is about the number of purchase in a given period. It could be 3 months, 6 months or 1 year. So we can understand this value as for how often or how many a customer used the product of a company. The bigger the value is, the more engaged the customers are. Could we say them as our VIP? Not necessary. Cause we also have to think about how much they actually paid for each purchase, which means monetary value. In this project, I set the time period to one year.

- **Monetary** Values is the total amount of money a customer spent in that given period. Therefore big spenders will be differentiated with other customers such as MVP or VIP.

## Analysis Process
In this project, for better readability, I would simplified the whole process into four steps:
- Import and clean the data
- Create the RFM table and calculate the RFM quantiles
- Perform clustering and generate cluster label
- Visualize the final result using the snake plot and heatmap

### Create the RFM table and calculate the RFM quantiles
For Recency values, I noticed that the most recent date of the transaction is on 2015-03-16. I, therefore, set this date as the pining date and count backward the number of days from the latest purchase for each customer based on customer id. Note that I subset the data for a year. That is, from 2014-03-17 to 2015-03-16. For frequency, I grouped the data by customer id and counted the number. Regarding Monetary Values, I summed up the transaction values. The result and partial code are shown in Figure 1.

<p align="center">	
	<img align="middle" width=700 src="images/Figure 1. RFM Model.png">
</p>
<p align="center">
  <i>Figure 1. RFM Model</i> 
</p>

With the RFM table, I am now able to segment the customers based on RFM values and assign quartiles of these metrics for each customer. Here, I divided the values into 4 groups using the `qcut` function in Pandas and then summed the RFM values to RFM_Score. To analyze the result, I create a new column called RFM level to group the previous result into even smaller segments(Figure 2).

<p align="center">	
	<img align="middle" width=700 src="images/Figure 2. RFM table with levels.png">
</p>
<p align="center">
  <i>Figure 2. RFM table with levels</i> 
</p>

After all the hard work, I now got the final result using the RFM quantiles method. Note that customers belonging to the Top Level do have the lowest values in recency and highest values in both frequency and Monetary Value and would surely deserve more attention when it comes to targeting. In reality, it may require multiple takes of trial and error to discover the appropriate cut-offs, but three is a common practice.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 3. RFM Model with Aggregation.png">
</p>
<p align="center">
  <i>Figure 3. RFM Model with Aggregation</i> 
</p>


### Perform clustering and generate cluster label
Now, I would to use clustering method to do customer segmentation and compare with the prior results. To get a reasonable result after clustering, I first used the square root to unskew the data, making sure that I have the variables symmetrically distributed. Secondly, I standardized the data to let all the variables have the same mean and variance.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 4. Distribution of variables after preprocessing.png">
</p>
<p align="center">
  <i>Figure 4. Distribution of variables after preprocessing</i> 
</p>

I performed the initial k-means model with k =3 to get a better understanding of how the clusters would be. I found out that though cluster 2 stands out, the other two are pretty close according to the mean value.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 5. Initial K-means model.png">
</p>
<p align="center">
  <i>Figure 5. Initial K-means model</i> 
</p>

It seems that I should put in more efforts to find out the optimal K. There were two methods I used to obtain the optimal K values for my analysis, the Scree plot and the Average Silhouette Score.
Unfortunately, we cannot get the optimal K out of this plot. It seems that the optimal may lie in 2 to 5, with k = 4 being the most potential answer.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 6. The Scree Plot.png">
</p>
<p align="center">
  <i>Figure 6. The Scree Plot</i> 
</p>

So I proceeded to the average Silhouette method and listed the top 3 highest values in Average Silhouette Score. The result says that I would get the highest score when k is 2, with k = 4 being the second optimal option.\
K = 2, the Average Silhouette Score is 0.3987\
K = 4, the Average Silhouette Score is 0.3416\
K = 5, the Average Silhouette Score is 0.3241\

Additionally, I also performed the hierarchical clustering using the Ward's linkage method, which minimizes the variant between the clusters to see if it would yield a different result of the number of K.
I drew a horizontal line that passed through the longest distance without a horizontal line and got two, followed by four clusters, as shown in the following figure, which is the same result derived from the scree plot. Combining all the information at hand, I decided to go with K = 4 instead of 2, for it contains more information and insights. If you are interested in the comparison of K = 2 and K = 4, and the difference in value.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 7. Dendrogram of the hierarchical clustering.png">
</p>
<p align="center">
  <i>Figure 7. Dendrogram of the hierarchical clustering</i> 
</p>

### Visualize the final result using the snake plot and heatmap
A snake plot, or line chart, is a market research technique to compare different segments and visually present the attributes of each cluster. I created the plot by putting Metrics at x-axis and value at the y-axis and group the values by `RFM_Level`. Repeat the same code which groups the values by `K_Cluster` this time. The result is shown in Figure 8. It seems the Top and Low groups on the left plot are similar with 1 and 3 clusters on the right plot. And the 0 and 2 clusters seem to be merged into group Middle. On top of this, I also calculate the relative importance of each cluster's attributes compared to the population average. Generally, we would like to have my segments to be different from the overall population and have unique properties of their own. I did this by dividing the average value of each segment over the average value of population and subtracting 1, ensuring 0 is returned when the segment mean equals the population mean. The result is shown in Figure 9. One can quickly notice that the Top group correspond to cluster 3 in the heatmap and to what extent are the exact numeric values differ in the two groups.
<p align="center">	
	<img align="middle" width=700 src="images/Figure 8. Snake Plots">
</p>
<p align="center">
  <i>Figure 8. Snake Plots</i> 
</p>

<p align="center">	
	<img align="middle" width=700 src="images/Figure 9. Heatmap of Relative Importance of Attributes.png">
</p>
<p align="center">
  <i>Figure 9. Heatmap of Relative Importance of Attributes</i> 
</p>


