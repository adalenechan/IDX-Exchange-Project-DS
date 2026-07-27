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

### Week 3 - Data Preprocessing
- Create a train/test split,
    - Most recent (May 2026) as test and 11 months prior as train.
- Handle missing values for the key variables: used median from the training set to replace missing values for LivingArea, Bedrooms, Bathrooms, and LotSize for both the training and test set.
  - Flagged missing values for LotSize.
  - Converted some datetime columns.
  - Converted the yes/no columns to boolean and replaced NAs as FALSE.
- Encoded a selection of categorical fields to numeric.
- Save the cleaned CSV files.

### Week 4 - Baseline Model
- Train a Linaer Regression as the first model.
  - Trained on mutiple versions of feature sets.
  - Returned to remove observations with invalid and extreme values for ClosePrice.
  - Compared the R^2 scores.
- Evaluate using R^2 on the test set.
- Record baseline results.

### Week 5 - Additional Models
- Try Decision Tree and Random Forest regressors.
- Compare the test R^2 against baseline.
- Document model behavior (strengthe/weaknesses).

### Week 6 - Feature Engineering
- Updated all previous notebooks where everything is shifted forward by one month for the newest dataset available (June 2026).
