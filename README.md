# IDX-Exchange-Project-DS

### Week 1 - Set Up

- Review task prompts and project goals.
- Download at least 6 months of raw CRMLSSold CSV data files.
- Review the MetaData document to understand the key variables and feature definitions.

### Week 2 - Data Exploration
- Load at least 6 months of dataset into a jupiter notebook using pandas.
- Explore distributions of the five key variables: ClosePrice, LivingArea, Bedrooms, Bathrooms, LotSize.
- Restrict analysis to PropertyType = Residential and PropertySubType =
SingleFamilyResidence.

#### *Results*
- ClosePrice, LivingArea, and LotSize have a right-skewed distribution.
  - ClosePrice: IQR is from $625 to $1.42M.
  - LivingArea: IQR is from 1387 to 2444 square feet.
  - LotSize: IQR is from 5.6K to 10.45K square feet.
- Bedrooms: Majority has 3-4 bedrooms.
- Bathrooms: Majority has 2-3 bathrooms.

### Week 3 - Data Preprocessing
- Create a train/test split,
    - Most recent (June 2026) as test and 11 months prior (July 2025 - May 2026) as training set.
- Handle missing values for the key variables: used median from the training set to replace missing values for LivingArea, Bedrooms, Bathrooms, and LotSize for both the training and test set.
  - Flagged missing values for LotSize.
  - Converted some datetime columns.
  - Converted the yes/no columns to boolean and replaced NAs as FALSE.
- Encoded a selection of categorical fields to numeric.
- Save the cleaned CSV files.

#### *Results*
- Removed outliers and invalid values for ClosePrice based on Week 2 EDA.
- Imputed median from the training set for unknown/missing values for all numeric key variables.
- Convert CloseDate, ContractStatusChangeDate , PurchaseContractDate, ListingContractDate to datetime
  - Extracted month, year, and day from CloseDate.
- Replaced missing values as FALSE for boolean columns - ViewYN, WaterFrontYN, BasementYN, PoolPrivateYN, AttachedGarageYN, FireplaceYN, and NewConstructionYN (assuming the features do not exist if marked as unknown).
- Performed one-hot encoding on a few categorical variables relating to location information of the properties - City, PostalCode, CountyOrParish, SchoolDistrict.
  - Replaced cities and postal codes not in the top 200 (frequency) to reduce complexity since there are 995 different cities and 2007 different postal codes among the properties in the dataset.
- The resulting datasets has 117,914 observations in the training set and 12,789 observations in the test set, and each with 828 features/columns.

### Week 4 - Baseline Model
- Train a Linear Regression as the first model.
  - Trained on multiple versions of feature sets (7 different sets, adding more features to each previous one for results comparison).
  - Returned to remove observations with invalid and extreme values for ClosePrice.
  - Compared the R^2 scores.
- Evaluate using R^2 on the test set.
- Record baseline results.

#### *Results*
- Linear regression generalizes well as the R^2 scores were similar between the training and test set.
- R^2 score of ~0.37 is relatively low, suggesting underfitting of this model.

### Week 5 - Additional Models
- Try Decision Tree and Random Forest regressors.
- Compare the test R^2 against baseline.
- Document model behavior (strengths/weaknesses).

#### *Results*
- Applied the decision tree and random forest regressor models to the same 7 feature sets respectively and computed the R^2 scores.
  1. Decision Tree
     - Explained most variability when included all three location features in addition to the five key variables and flagged missing lot size.
     - Does not generalize well for any of the feature sets used; the most comprehensive feature set has the least difference in R^2 score between test and training set, but >0.3.
     - Very high R^2 from the training data (>0.99), suggesting overfitting.

| Feature Set | Number of Features | Training R² | Test R² |
|---|---:|---:|---:|
| Basic | 4 | 0.995643 | -0.016423 |
| With Property Features | 8 | 0.997932 | -0.023133 |
| With Missing LotSize Flagged | 9 | 0.997934 | -0.028392 |
| With Location Features (City Only) | 209 | 0.999359 | 0.436744 |
| With Location Features (PostalCode Only) | 209 | 0.998803 | 0.296635 |
| With Location Features (County Only) | 68 | 0.999097 | 0.335991 |
| With All Location Features | 470 | 0.999426 | 0.615222 |  
  
  2. Random Forest
     - Explained the most variability and least difference in R^2 score between test and training set on the most comprehensive feature set.
       - Train: 0.970167, Test: 0.770361
     - Clear improvement in model performance after adding location features.
     - Using only the city information explained slightly more variability than using either the postal code or county alone.

| Feature Set | Number of Features | Training R² | Test R² |
|---|---:|---:|---:|
| Basic | 4 | 0.915542 | 0.394207 |
| With Property Features | 8 | 0.919244 | 0.414490 |
| With Missing LotSize Flagged | 9 | 0.919244 | 0.415383 |
| With Location Features (City Only) | 209 | 0.956261 | 0.678966 |
| With Location Features (PostalCode Only) | 209 | 0.943323 | 0.577668 |
| With Location Features (County Only) | 68 | 0.948738 | 0.624738 |
| With All Location Features | 470 | 0.970167 | 0.770361 |


### Week 6 - Feature Engineering
- Updated all previous notebooks where everything is shifted forward by one month for the newest dataset available (June 2026).
- Re-train all three models with the updated feature sets that include PropertyAge (CURRENT YEAR - YearBuilt), BedBathRatio (BedroomsTotal / BathroomsTotalInteger), and SchoolDistrict.
  - SchoolDistrict is a more detailed geographic feature obtained by spatially joining each property's coordinates (longitude and latitude) against the CA School District Areas 25-26 boundaries.
- Compared the new results with previous results.

#### *Results*
- Returned to notebook 02 to update dataset coverage and create the new sample features - PropertyAge, BedBathRatio.
- For all three models, the following two feature sets performed best based on R^2 scores:

| Feature Set | Includes |
| ---- | ----: |
| With Engineered Features + All Location + School District | Property features + engineered features + City + Postal Code + County + School District  |
| With Everything + Missing Year Built | Property features + engineered features + Missing Year Built + City + Postal Code + County + School District |

1. Linear Regression
   - R^2 scores higher with the sample features included (0.364->0.427).
   - Test results slightly higher than that of training (~0.1-0.2 difference).
2. Decision Tree Regressor
   - Least gap between training and test results with new sample features engineered.
     - Smallest Gap: Training 0.99 vs Test 0.65 for feature set *With Engineered Features + All Location + School District* and *With Everything + Unknown Year Built*
   - Still suggests overfitting as observed in the really high R^2 from training set.
3. Random Forest Regressor
   - Shifting the dataset used forward by one month gave similar results, slightly narrowed the gap difference between training and test.
   - Adding sample features (PropertyAge, BedBathRatio, SchoolDistrict) further improved the model.
   - Explained slightly more variability
     - Training: 0.970->0.974, Test: 0.776->0.810

Overall,
- Have improvement with engineered features based on R^2 scores.
- The Decision Tree Regressor Model is still unstable. Only outperformed the Linear Regression Model when either the city feature or all location features were included, and when the engineered and all locations features were included.
- The Random Forest Regressor Model consistently outperformed both the other two models under all feature sets tested, but is the slowest to compute.
- Linear Regression has stable performance and is fast to compute, but it cannot capture nonlinear patterns between property features (explained by lower R^2 scores).

### Week 7 - Advanced Models
- Try Gradient Boosting
  - Used XGBoost
- Perform light hyperparameter tuning (depth, learning rate, n_estimators)

#### *Results*

Test R^2 Scores from each model:

| Feature Set | Linear Regression |	Decision Tree Regressor	| Random Forest Regressor | XGBoost (Baseline) | XGBoost (Tuned) |
| ---- | ----: | ----: | ----: | ----: | ----: |
| With Engineered Features + All Location | 0.440762 | 0.650386 | 0.810480 | 0.814515 | 0.834611 |
| With Everything + Missing_YearBuilt | 0.440762 | 0.652362 | 0.810361 | 0.814688 | 0.835624 |

### Week 8 - Evaluation Expansion
- Compute metrics beyond R^2 scores (MAPE and MdAPE) for all models tested.
- Summarize insights (e.g., which price bands performed better)

#### *Results*
- Across all models, the middle price bands ($500K-1M and $1M-2M) performed better than the ends (<$500K and >$2M) by observing the MAPE and MdAPE values.


