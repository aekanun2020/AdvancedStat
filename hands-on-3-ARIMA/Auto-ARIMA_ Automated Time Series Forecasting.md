<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Auto-ARIMA: Automated Time Series Forecasting

Auto-ARIMA is a powerful statistical method that automatically identifies the optimal parameters for ARIMA (Autoregressive Integrated Moving Average) models, enabling effective time series forecasting without requiring manual parameter specification. This algorithm has become a cornerstone for modern time series analysis, providing an accessible approach to complex forecasting tasks.

## Fundamentals of Auto-ARIMA

Auto-ARIMA algorithms were developed to simplify the traditional ARIMA modeling process, which often required expert knowledge and extensive trial-and-error testing. The technique automatically determines the optimal values for the three key ARIMA parameters (p, d, q) and their seasonal counterparts where applicable (P, D, Q, m)[^3].

### ARIMA Components

An ARIMA model consists of three fundamental components represented by the parameters p, d, and q:

- **Autoregressive (AR) component (p)**: Represents the relationship between an observation and a number of lagged observations. It predicts a variable based on its previous values rather than external factors[^1]. A time series is considered AR when previous values are highly predictive of later values, characterized by a gradual decrease in the autocorrelation function (ACF)[^7].
- **Integrated (I) component (d)**: Represents the degree of differencing required to make a time series stationary. Differencing involves replacing values with the difference between consecutive observations, helping to remove trends and seasonality[^1]. This component is particularly important for non-stationary data[^7].
- **Moving Average (MA) component (q)**: Represents the relationship between an observation and residual errors from moving average models applied to lagged observations. It replaces forecast errors from previous periods with averages of adjacent periods' errors, smoothing out irrelevant fluctuations[^1][^7].


## How Auto-ARIMA Works

The auto-ARIMA algorithm follows a systematic process to identify the best model parameters:

### 1. Determining the Differencing Order (d)

The first step is to identify the appropriate level of differencing needed to achieve stationarity. This is done through statistical tests:

- **KPSS Test (Kwiatkowski-Phillips-Schmidt-Shin)**: Tests for stationarity with a null hypothesis that the series is stationary[^4][^5]. This is the default test in most implementations.
- **ADF Test (Augmented Dickey-Fuller)**: Tests for the presence of unit roots with a null hypothesis that the series has a unit root (is non-stationary)[^4][^14].
- **PP Test (Phillips-Perron)**: Similar to ADF but more robust to unspecified autocorrelation and heteroscedasticity[^9].

The number of differences (d) is determined based on the test results. If the series is already stationary, d=0; otherwise, differencing is applied until stationarity is achieved[^3][^4].

### 2. Identifying Optimal AR and MA Orders (p and q)

After determining d, the algorithm searches for the best combination of p and q values:

- It evaluates multiple combinations of p and q within specified ranges (typically defined by start_p, max_p, start_q, max_q parameters)[^4][^6].
- For each combination, it fits an ARIMA model and calculates an information criterion, such as AIC (Akaike Information Criterion), AICc (corrected AIC), BIC (Bayesian Information Criterion), or HQIC (Hannan-Quinn Information Criterion)[^7].
- The model with the lowest information criterion value is selected as the optimal model[^7].


### 3. Handling Seasonality (if applicable)

If seasonality is present, the algorithm also determines optimal seasonal parameters (P, D, Q):

- Seasonal differencing order (D) is determined using tests like the Canova-Hansen test[^4][^6].
- Seasonal AR and MA orders (P and Q) are identified through a similar process as non-seasonal parameters, with seasonality frequency specified by parameter m (e.g., m=12 for monthly data with yearly seasonality)[^3][^4].


## Implementations in Different Languages

### Python Implementations

1. **pmdarima (formerly pyramid-arima)**:
    - The most popular Python implementation, designed to be equivalent to R's auto.arima[^2][^10].
    - Features include statistical tests, differencing utilities, transformers, and scikit-learn-compatible interfaces[^2][^10].
    - Basic usage: `model = pm.auto_arima(train, seasonal=True, m=12)`[^2][^11].
2. **sktime**:
    - Provides AutoARIMA as part of its time series forecasting toolkit[^6][^8].
    - Wraps pmdarima's implementation with a scikit-learn compatible interface[^8].

### R Implementation

The original auto.arima function is part of the forecast package in R, developed by Rob J. Hyndman[^3][^5]:

- Has been extensively tested and refined over years of use.
- Default settings are optimized for forecast accuracy based on empirical testing[^5].


## Key Parameters and Customization

Auto-ARIMA implementations offer numerous parameters for customization:

### Common Parameters

- **start_p, max_p**: Minimum and maximum values for p (autoregressive order)[^4][^6].
- **start_q, max_q**: Minimum and maximum values for q (moving average order)[^4][^6].
- **d, max_d**: Fixed differencing order or maximum differencing order if automatically determined[^4][^6].
- **seasonal**: Boolean indicating whether to include seasonal components[^4][^6].
- **m**: The period of seasonality (e.g., 12 for monthly data with yearly patterns)[^4][^6].
- **information_criterion**: Criterion for model selection ('aic', 'aicc', 'bic', 'hqic')[^4][^7].
- **test**: Statistical test used for determining differencing order ('kpss', 'adf', 'pp')[^4][^5].
- **stepwise**: Whether to use stepwise search (faster) or conduct a more exhaustive search[^4][^6].
- **trace**: Whether to print status during fitting[^11].


## Best Practices and Tips

1. **Choose the appropriate stationarity test**: KPSS is the default in most implementations as it typically leads to more accurate forecasts[^5]. However, if you're unsure, you can try both KPSS and ADF tests and compare results[^14].
2. **Handle contradictory test results**: Sometimes KPSS and ADF tests may yield contradictory conclusions. This is normal as they have opposite null hypotheses (KPSS tests for stationarity, ADF tests for non-stationarity)[^16][^14].
3. **Consider data transformations**: Sometimes applying transformations (like logarithmic or Box-Cox) before using auto-ARIMA can improve results, especially for data with changing variance[^7].
4. **Tune seasonal parameters appropriately**: Set m to match your data's seasonal period (e.g., m=12 for monthly data with yearly seasonality)[^3][^11].
5. **Evaluate model fit**: Don't rely solely on information criteria. Examine residuals for randomness and use out-of-sample validation to test forecast accuracy[^13].

## Limitations and Considerations

1. **Computational complexity**: When using non-stepwise search or with large parameter spaces, auto-ARIMA can be computationally expensive[^3][^7].
2. **Stationarity challenges**: Auto-ARIMA might struggle with complex non-stationary data patterns that differencing alone cannot address[^7][^13].
3. **Over-reliance on information criteria**: The "best" model according to information criteria might not always provide the best forecasts[^7].
4. **Different test results**: Using different stationarity tests (KPSS vs. ADF) may lead to different model specifications[^14][^16].
5. **Handling extreme values/outliers**: Auto-ARIMA doesn't automatically handle outliers, which can significantly impact model accuracy[^7].

## Conclusion

Auto-ARIMA represents a significant advancement in making time series forecasting accessible to data scientists and analysts of varying expertise levels. By automating the complex process of parameter selection, it reduces the technical barriers to effective forecasting while still providing options for customization by advanced users.

While it offers a powerful "out-of-the-box" solution for many forecasting problems, understanding its underlying principles and limitations is crucial for proper application. For optimal results, users should consider their domain knowledge, data characteristics, and alternative models rather than applying auto-ARIMA as a black-box solution.

As time series analysis continues to evolve, auto-ARIMA remains a valuable tool in the broader ecosystem of forecasting techniques, striking a balance between automation and control that appeals to both novice and experienced forecasters.

<div>⁂</div>

[^1]: https://help.sap.com/docs/SAP_INTEGRATED_BUSINESS_PLANNING/feae3cea3cc549aaa9d9de7d363a83e6/c9b4470ccc0b47b7bed31d18c76f2201.html

[^2]: https://pypi.org/project/pmdarima/

[^3]: https://mrinalcs.github.io/how-auto-arima-works

[^4]: https://alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html

[^5]: https://robjhyndman.com/hyndsight/unit-root-tests/

[^6]: https://www.sktime.net/en/latest/api_reference/auto_generated/sktime.forecasting.arima.AutoARIMA.html

[^7]: https://alkaline-ml.com/pmdarima/tips_and_tricks.html

[^8]: https://www.sktime.net/en/v0.33.1/api_reference/auto_generated/sktime.forecasting.arima.AutoARIMA.html

[^9]: https://alkaline-ml.com/pmdarima/1.3.0/tips_and_tricks.html

[^10]: https://github.com/alkaline-ml/pmdarima

[^11]: https://www.alldatascience.com/time-series/forecasting-time-series-with-auto-arima/

[^12]: https://stackoverflow.com/questions/47682100/auto-arima-differencing-when-data-is-stationary

[^13]: https://stackoverflow.com/questions/76297649/auto-arima-in-python-results-in-poor-fitting-prediction-of-trend

[^14]: https://www.diva-portal.org/smash/get/diva2:1668033/FULLTEXT01.pdf

[^15]: https://www.youtube.com/watch?v=sCl6CXZ2xBg

[^16]: https://stats.stackexchange.com/questions/163939/why-does-auto-arima-not-differentiate-my-series

[^17]: https://github.com/mkosaka1/AirPassengers_TimeSeries

[^18]: https://unit8co.github.io/darts/generated_api/darts.models.forecasting.auto_arima.html

[^19]: https://www.alldatascience.com/time-series/forecasting-time-series-with-auto-arima/

[^20]: https://www.rdocumentation.org/packages/forecast/versions/8.23.0/topics/auto.arima

[^21]: https://stackoverflow.com/questions/71758726/auto-arima-parameters-for-correct-forecasting

[^22]: https://nixtlaverse.nixtla.io/statsforecast/docs/models/autoarima.html

[^23]: https://github.com/alkaline-ml/pmdarima/blob/master/pmdarima/arima/auto.py

[^24]: https://otexts.com/fpp2/arima-r.html

[^25]: https://stackoverflow.com/questions/76297649/auto-arima-in-python-results-in-poor-fitting-prediction-of-trend

[^26]: https://www.kaggle.com/code/jurk06/auto-arima-on-multivariate-time-series

[^27]: https://www.youtube.com/watch?v=TB46ytQutGQ

