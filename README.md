# Used-Car Price Analysis

A Python notebook project for cleaning used-car listing data and setting up a first baseline for selling-price prediction. The notebook explores data quality, standardizes fields, handles missing values, encodes selected categorical variables, and evaluates a simple mean-price baseline using mean absolute error (MAE).

> **Project status:** This is an exploratory data-cleaning and baseline project. A predictive regression model has not yet been trained or evaluated.

## Dataset

The notebook expects a CSV file named `used_cars_messy.csv` in the notebook's working directory. It contains used-car listings with fields such as:

- Vehicle identity: `car_name`, `brand`, `model`

- Vehicle condition/specifications: `vehicle_age`, `km_driven`, `mileage`, `engine`, `max_power`, `seats`

- Listing information: `seller_type`, `fuel_type`, `transmission_type`

- Target: `selling_price`

The source data initially contains approximately 15,661 rows and 14 columns, including an extra index-like `Unnamed: 0` column. Ensure you have permission to use and share the dataset; the data file itself is not included here.

## Requirements

- Python 3

- Jupyter Notebook or JupyterLab

- Packages:
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`
  - `scikit-learn`

Install dependencies with:

```bash
python -m pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

## Run the notebook

1. Place `used_cars_messy.csv` beside the notebook.

1. Start Jupyter from that directory:

   ```bash
   jupyter notebook
   ```

1. Open the notebook and run its cells **in order**, from a fresh kernel.

The notebook reads the CSV using a relative path, so running it from another directory may require changing the path in `pd.read_csv(...)`.

## Workflow

1. **Inspect the raw data** with DataFrame summaries and data types.

1. **Remove the extra index column** and normalize text fields (for example, trimming whitespace and standardizing capitalization/known seller-type misspellings).

1. **Convert formatted measurements to numeric values**, including `km_driven` values containing `km`/`kms` and `mileage` values containing `kmpl`.

1. **Handle missing data:** drop rows without `selling_price`; fill missing `engine`, `max_power`, `mileage`, and `km_driven` values with their medians; fill missing `seats` with its mode.

1. **Filter invalid seating values** by retaining rows with at least two seats.

1. **Encode selected categorical columns** using one-hot encoding for fuel and seller type. The notebook also removes `car_name` before preparing the data.

1. **Split features and target** into training and test sets (80/20, `random_state=42`).

1. **Evaluate a baseline:** predict every test-set price using the mean selling price from the training set and calculate MAE.

## Baseline result

The saved notebook output reports:

- Training-set mean selling price: approximately **1,018,693**

- Test-set baseline MAE: approximately **895,752**

The currency/unit is not explicitly documented in the notebook; confirm it from the dataset source before interpreting these amounts. MAE means that, on average, the baseline prediction differs from the observed price by about 895,752 dataset currency units. Since this is only a constant-mean baseline, it is a reference point for future models, not a useful pricing model by itself.

## Known issues and next steps

- The notebook includes a recorded `KeyError` at an encoding cell: `fuel_type` and `seller_type` were not present at the time that cell ran. This is consistent with executing cells out of order or rerunning a destructive transformation. Restart the kernel and run cells in order; also verify the columns before encoding.

- In the final feature table, `brand`, `model`, and `transmission_type` remain strings. Most scikit-learn estimators require these categorical values to be encoded before fitting, so the current notebook does **not** yet fit a model.

- Some suspicious values remain after the documented cleanup (for example, `vehicle_age = 0`, and the source summary reports an extreme selling-price maximum of 1,000,000,000). Investigate these records and establish sensible validity/outlier rules before modeling.

- The notebook filters `seats >= 2`; its earlier note says to drop values “less than 0,” but the actual code is stricter and removes values below two.

- For a robust modeling workflow, fit imputers and encoders on the training data only (preferably with a scikit-learn `Pipeline`/`ColumnTransformer`) to avoid data leakage. Then train a regression model and compare its test MAE with this baseline.


