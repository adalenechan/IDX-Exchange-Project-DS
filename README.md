# IDX-Exchange-Project-DS

### Project Background
The goal for the IDXExchange Project is to apply machine learning models to predict the close price of a single residential property in California based on various characteristics. 

### Dataset Source
- The project uses real estate properties data sourced from the California Regional Multiple Listing Service (CRMLS). The dataset consists of 12 months of real estate properties data.
- Train/Test Split Strategy
  - **Training**: July 2025 - May 2026
  - **Test**: June 2026
- Target variable: `ClosePrice` (final sale price of the property)
- Key variables: `LivingArea`, `Bedrooms`, `Bathrooms`, `LotSize`

### Data Preprocessing
Outlier Removal
- Removed outliers and invalid values for `ClosePrice` based on data exploration results in `01_exploration.ipynb`.

Handling Missing Values
- Imputed median from the training set for unknown/missing values for all numeric key variables.
- Replaced missing values as FALSE for boolean columns - `ViewYN`, `WaterFrontYN`, `BasementYN`, `PoolPrivateYN`, `AttachedGarageYN`, `FireplaceYN`, and `NewConstructionYN` (assuming the features do not exist if marked as unknown).

Datatype Conversions
- Convert `CloseDate`, `ContractStatusChangeDate`, `PurchaseContractDate`, `ListingContractDate` to datetime
  - Extracted month, year, and day from `CloseDate`.

One-Hot Encoding
- Performed one-hot encoding on a few categorical variables relating to location information of the properties - City, PostalCode, CountyOrParish, SchoolDistrict.
  - Replaced cities and postal codes not in the top 200 (frequency) to reduce complexity since there are 995 different cities and 2007 different postal codes among the properties in the dataset.

Feature Engineering & Geographic Layer
- `PropertyAge`: CURRENT YEAR - YearBuilt
- `BedBathRatio`: BedroomsTotal / BathroomsTotalInteger
- `SchoolDistrict`: a more detailed geographic feature obtained by spatially joining each property's coordinates (longitude and latitude) against the CA School District Areas 25-26 boundaries.

- The resulting dataset has 117,914 observations in the training set and 12,789 observations in the test set, and each with 828 features/columns.

### Models Tested

- Linear Regression
- Decision Tree Regressor
- Random Forest Regressor
- XGBoost Model
  - Tuned version: `max_depth`, `learning_rate`, `n_estimators`

#### *Note*
- Multiple feature set versions were trained (features beyond the key variables were added to each previous one for results comparison)

### Model Results

Test R^2 Score Comparison
| Feature Set | Linear Regression |	Decision Tree Regressor	| Random Forest Regressor | XGBoost (Baseline) | XGBoost (Tuned) |
| ---- | ----: | ----: | ----: | ----: | ----: |
| With Engineered Features + All Location | 0.440762 | 0.650386 | 0.810480 | 0.814515 | 0.834611 |
| With Everything + Missing_YearBuilt | 0.440762 | 0.652362 | 0.810361 | 0.814688 | 0.835624 |

Evaluation Expansion Results
- The MAPE and MdAPE scores in addition to R^2 show that the tuned XGBoost Model was the best-performing model.
  - Highest Test R^2: 0.83
  - Test MAPE: 25.74%
  - Test MdAPE: 11.12%

Price Band Analysis Results
- The middle price bands (`$500K-1M` and `$1M-2M`) performed better than the ends (`<$500K` and `>$2M`) by observing the MAPE and MdAPE values.

Overall, the tuned XGBoost model achieved the best prediction performance. Engineered features and geographic layers also improved performance.
