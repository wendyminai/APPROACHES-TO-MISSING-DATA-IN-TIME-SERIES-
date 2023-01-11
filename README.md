# APPROACHES-TO-MISSING-DATA-IN-TIME-SERIES-
Missing data is a common problem in real-world datasets. If you’ve ever wondered how to handle missing values in time series data effectively, this post is for you! I will introduce five approaches for imputing missing values in time series data.

I’ll focus on univariate time series most time. We can impute each time series individually for handling missing values in multivariate time series. The last approach will consider the interaction of multiple time series and impute multivariate time series well.

# Example dataset
I’ll use a time series from the public [world energy consumption dataset](https://www.kaggle.com/datasets/uciml/electric-power-consumption-data-set). Figure 1 shows the aggregated daily energy consumption. Then I randomly pick four periods (two with ten days, two with 30 days) and remove the data. We will try to impute those four periods.
<img width="684" alt="Screen Shot 2023-01-11 at 9 41 29 AM" src="https://user-images.githubusercontent.com/111389636/211849863-92510806-583f-45d3-96e3-a6b45a81999b.png">

# 1. Imputation with a single value
This is the most straightforward approach. We impute a single value for all missing values. The typical choices are the last available observation value, the mean or median of the observation window, etc. The figure below shows this approach.
<img width="794" alt="Screen Shot 2023-01-11 at 9 43 28 AM" src="https://user-images.githubusercontent.com/111389636/211850353-f38f6594-f583-4c1e-b19a-51a77f631207.png">
As you can see, this approach is easy to implement and understand. The disadvantage is that it won’t learn any patterns from the available data, and filled data won’t have any variance.

# 2. Imputation by interpolation
Interpolation is a concept in the statistics field. The idea is that if we can fit a function based on known data points, we can construct new data points in the function curve. In terms of implementation, Pandas has the function interpolate() for Series and DataFrame objects. The figure below demonstrates the results of linear interpolation. <img width="872" alt="Screen Shot 2023-01-11 at 9 46 28 AM" src="https://user-images.githubusercontent.com/111389636/211851160-d3bcb143-6ed6-4046-a1f4-5d1f319e8653.png">

# 3. Imputation by Gaussian processes
This approach is like a general version of interpolation. The idea is that many potential functions generate the existing data points, some are possible, and some are unlikely. This approach will try to learn the distribution of those functions. The figure below gives one example of imputation by Gaussian Process. It will estimate the mean and variance of each missing observation. Therefore, we can have confidence intervals for imputed values. As you can see, the longer the missing period, the wider the confidence interval.
<img width="903" alt="Screen Shot 2023-01-11 at 9 48 13 AM" src="https://user-images.githubusercontent.com/111389636/211851569-2652decc-6657-46a6-899a-7280518bcdbb.png">

# 4. Imputation by Forecasting
This approach involves forecasting the values for the missing period as if they were future values. Time series forecasting is well-studied, and various models can be applied for this purpose. I used the [Theta model](https://www.statsmodels.org/dev/examples/notebooks/generated/theta-model.html) and got the results shown in the figure below.<img width="862" alt="Screen Shot 2023-01-11 at 9 52 12 AM" src="https://user-images.githubusercontent.com/111389636/211852681-ba4cb88d-7f57-44d4-80e0-4b49d6682c29.png">

# 5. Imputation by Multivariate Imputation by Chained Equations (MICE)
This approach uses a specialized imputation algorithm that yields the best results for the example dataset.

The MICE method involves iteratively imputing the missing values in a dataset using a series of regression models, with the imputed values being used to update the estimates of the regression parameters.

I use the [scikit-learn’s IterativeImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html#sklearn.impute.IterativeImputer) to implement this approach (note: this estimator is still experimental as of 2023 Jan). The original MICE approach will return multiple imputations. However, the implementation of scikit-learn will only return a single imputation.

I still obtain multiple imputations by a trick: first, I segment the raw signal into rolling windows and add month info to the windows. Then MICE will impute the missing values for each window. Since one data point could appear in multiple windows, we will have numerous imputations for the same data points.

<img width="681" alt="Screen Shot 2023-01-11 at 9 54 06 AM" src="https://user-images.githubusercontent.com/111389636/211853167-0f4e5cf0-70d2-4fa2-8aea-5b45cd64dac9.png">

The figure below shows the imputation results
<img width="865" alt="Screen Shot 2023-01-11 at 9 54 24 AM" src="https://user-images.githubusercontent.com/111389636/211853238-90fe70fc-ebcc-4e28-9220-828c2686786b.png">

# CONCLUSION
Compared to the other approaches above, MICE gives the best results. Sections 1,2, and 4 have smaller RMSE, and most of the filled values are within the 99% confidence interval. Section 3 has obvious outliers (around 2007–02–21 to 2007–03–04) not following the existing patterns.

MICE not only provides confident intervals, but it also works for multivariate time series. Because of the way of regressions, it will learn interactions of multiple time series and use learned patterns to fill the missing values. I may write a detailed article on MICE for time series in the future.
