# Zalora's Data Science Technical test.

Based on Indonesia product list dataset

Current project explores dataset published with Zalora data scientist test task. Package (dataset and problem statement) is downloadable [here](https://s3-ap-southeast-1.amazonaws.com/zalora-public/data-science-zalora.tar.gz).

Given the sample dataset, candidate is expected to analyse data and give suggestions on how to improve and use it in order to create good product ranking.

```
The result of this ranking serves a lot of purposes:
- Displaying best selling products on our homepage.
- Sorting our catalogs / search results whenever users surf our homepage. 
- Sending better, more profitable email campaigns.
```

Below are my answers to 3 questions of the test. 

Code of data exploration is in iPython notebook analysis.ipynb.


## Questions and Answers

### Give us your suggestions on how we could make our data set better / more useful.

- It could be useful and easiest to add **extraction date** - when the data was pulled from database(s). This would allow more intuitive check if activated_at date was before or after 30 (7) days ago
- **Definitions** of views, impressions, sales, cancellations and returns, how they relate to each other and Zalora customer use case should be included with dataset.
- Dataset should include **time period**, when **special price** was active for product. In combination with more detailed sales counts in timeline, this would allow to see what effect discount has on sales.
- Reorganise periodic counts that currently overlap (last 7 days is part of last 30 days). Instead could break down counts per current week, 1 week ago, 2 weeks ago, ...
- Include info about **profit margin** for each product. This would allow us to optimize ranking model for actual profit instead of just number of sales.
- Adding even more detailed breakdown over timeline (eg. daily/hourly counts) could reveal **correlations between sales and weekdays or time of day**. Think of people who are at work during day, or people who stay up late night.
- Combining this dataset with customers individual shopping data would allow personalised recommendations

### With the given dataset, can you come up with a scientific approach and model for our ranking?

Based on given purposes of the ranking we can say that a good ranking would prioritize products that convert to sales well - the more zalora displays a product, the more it gets sold. This can be quantified with a new feature - **impressions_per_sale** - how many impressions in average is needed for one extra sale. 

Ranking model should minimize impressions_per_sale, so that top ranked products, when displayed to customers, are expected to generate more sales. 

Data exploration in analyse.ipynb did not show any significant correlation between impressions_per_sale and other features or more complex pattern that would result in useful model. Thus we should keep the model simple and rank products by score impressions_per_sale ( = impressions_count_last_7days  / net_sale_count_last_7days). **The lower the score (impressions_per_sale), the higher is rank of product**.

Depending on business processes Zalora might want to introduce a rule that **lowers the score when stock gets low** (to avoid running out of stock). Of course this would require model to work on real time database not offline dataset.

As net sales do not include cancellations and returns, we don't have to mind info about these.

### How would you test, train, and evaluate your model?

Usefulness of the proposed model relies on assumption that when we increase impressions of our products, then higher ranked products generate more sales than lower ranked ones.

To test the assumption we can monitor how and if ranking changes if we increase impressions of different products. If increase in impressions raise sales of product and ranking remains relatively constant, then our assumptions proves to be true.

Products, that start generating less sales fall back in our ranking to give space for better converting ones.

