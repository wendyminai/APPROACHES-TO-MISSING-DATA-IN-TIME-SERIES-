# APPROACHES-TO-MISSING-DATA-IN-TIME-SERIES-
Missing data is a common problem in real-world datasets. If you’ve ever wondered how to handle missing values in time series data effectively, this post is for you! I will introduce five approaches for imputing missing values in time series data.

I’ll focus on univariate time series most time. We can impute each time series individually for handling missing values in multivariate time series. The last approach will consider the interaction of multiple time series and impute multivariate time series well.

# Example dataset
I’ll use a time series from the public [world energy consumption dataset](https://www.kaggle.com/datasets/uciml/electric-power-consumption-data-set). Figure 1 shows the aggregated daily energy consumption. Then I randomly pick four periods (two with ten days, two with 30 days) and remove the data. We will try to impute those four periods.
