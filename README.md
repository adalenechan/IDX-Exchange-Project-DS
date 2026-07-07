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
- Encoded a selection of categorical fields to numeric.
- Save the cleaned CSV files.
