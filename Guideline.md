### Phase 1: Data Collection and Foundation 📥

[cite_start]**Goal:** Gather all the raw data needed for your research as outlined in your proposal[cite: 349].

1.  **Financial Indices Data:**
    * **What:** Collect daily **Open, High, Low, Close, Adjusted Close,** and **Volume** data.
    * [cite_start]**Which:** S&P 500 (`^GSPC`), NASDAQ (`^IXIC`), FTSE 100 (`^FTSE`)[cite: 350].
    * [cite_start]**How:** Use the `yfinance` library in your notebook to download data for the 2015-2025 period[cite: 350]. Save each index as a separate CSV file in your `/data` folder.

2.  **Macroeconomic Indicators:**
    * [cite_start]**What:** Collect **GDP growth rates, unemployment rates, inflation,** and the **VIX**[cite: 352].
    * [cite_start]**How:** Go to **FRED**, **World Bank**, and **IMF**[cite: 352]. Download the data for the same 2015-2025 period. Note that this data may be monthly or quarterly, which is okay. We will handle that in the next phase. Save them as CSV files.

3.  **Sentiment Data (Text):**
    * [cite_start]**What:** You need unstructured text data like **news headlines** and **tweets**[cite: 351].
    * **How:** Start with one source first to keep it simple. Look for financial news headline datasets on **Kaggle**. This is often easier than using an API for the first time. The goal is to get a CSV file where each row has a `date` and a `headline`.

4.  **Geopolitical Events:**
    * [cite_start]**What:** You need a list of major crisis dates[cite: 353].
    * **How:** Create a simple text file or CSV. [cite_start]List the start and end dates of the major events you want to test, like the **COVID-19 market crash (approx. Feb-Mar 2020)** and the **U.S.-China tariff announcements**[cite: 351, 402].

***

### Phase 2: Data Preprocessing & Feature Engineering 🛠️

**Goal:** Clean the raw data and create the features (inputs) for your machine learning model.

1.  **Clean and Align Structured Data:**
    * Load all your financial and macroeconomic CSVs into `pandas` DataFrames.
    * **Align by Date:** This is a crucial step. Merge all DataFrames into one large table based on the date. Since macroeconomic data is often monthly, use the `ffill()` (forward-fill) method in pandas to fill the daily gaps. This means each day will have the most recent macroeconomic value.
    * [cite_start]**Handle Missing Values:** Use interpolation to fill any remaining gaps in your data[cite: 356].
    * **Normalize Features:** Scale all your numerical columns (like price, volume, GDP) to a range between 0 and 1. The `MinMaxScaler` from the `scikit-learn` library is perfect for this. [cite_start]This helps the machine learning model learn more effectively[cite: 357].

2.  **Create Sentiment Features:**
    * Load your news headlines data.
    * [cite_start]Clean the text by removing special characters and URLs[cite: 359].
    * Use a sentiment analysis tool to give each headline a score. **Start with VADER**, as it's simpler. It will give you positive, negative, neutral, and a "compound" score.
    * Add these sentiment scores as new columns to your main DataFrame, matching by date.

3.  **Create Time-Series Features:**
    * From your price data, create new features that help the model see trends.
    * **Moving Averages:** Calculate the 7-day and 30-day moving average of the closing price.
    * [cite_start]**Lagged Values:** Create columns with the price from the previous day (`t-1`), two days ago (`t-2`), etc.[cite: 374].

4.  **Label Crisis Periods:**
    * Using the list of dates from Phase 1, create a new column in your DataFrame called `is_crisis`.
    * [cite_start]Set the value to `1` for dates that fall within a crisis period and `0` for all other dates[cite: 365]. This is essential for your final evaluation.

At the end of this phase, you should have **one master CSV file** with all your features ready for the model.

***

### Phase 3: Exploratory Data Analysis (EDA) & Revisiting RQs 🧐

**Goal:** Understand your data and address your supervisor's feedback about improving your **Research Questions (RQs)**.

1.  **Visualize Everything:**
    * Plot your main financial index (e.g., S&P 500) and overlay your sentiment scores on the same chart. Do you see sentiment drop *before* the market drops?
    * Create box plots to compare the distribution of sentiment scores during `is_crisis=1` vs. `is_crisis=0` periods. [cite_start]This directly helps with **RQ4**, which compares sentiment features to traditional ones[cite: 294].

2.  **Draft Revised Research Questions:**
    * Your supervisor said to revisit your RQs. Based on your EDA, you can now make them more specific.
    * [cite_start]**Original RQ2:** "Can a hybrid model... improve forecasting accuracy during documented crises?" [cite: 292]
    * **Revised RQ2 (Example):** "How much does adding VADER sentiment scores from news headlines reduce the Root Mean Squared Error (RMSE) of a BiLSTM model when forecasting the S&P 500 during the COVID-19 crash (Feb-Mar 2020)?"
    * Draft 2-3 new, specific questions like this.

***

### Phase 4: Model Development 🤖

[cite_start]**Goal:** Build and train the hybrid model described in your proposal[cite: 385].

1.  **Split Your Data:**
    * [cite_start]Divide your master dataset into **training (70%), validation (15%), and testing (15%) sets**[cite: 394]. **IMPORTANT:** Make sure to split the data chronologically. Do not shuffle it. For example, use 2015-2021 for training, 2022-2023 for validation, and 2024-2025 for testing.

2.  **Build a Baseline Model:**
    * First, build a simple **LSTM model** that only uses historical prices (the time-series features you created). Train it and evaluate its performance. [cite_start]This is your baseline for comparison[cite: 400].

3.  **Build Your Hybrid Model:**
    * Now, build the full model from your proposal. [cite_start]Use a **Bidirectional LSTM (BiLSTM)** as the main component[cite: 390].
    * [cite_start]The input for this model will be all your features combined: time-series features, sentiment scores, and macroeconomic indicators[cite: 392].
    * [cite_start]Train this model using the Adam optimizer and early stopping to prevent overfitting, as you planned[cite: 395].

***

### Phase 5: Model Evaluation & Interpretation 📈

**Goal:** Rigorously test your model and address the feedback about **developing the evaluation section**.

1.  **Calculate Standard Metrics:**
    * [cite_start]For both your baseline and hybrid models, calculate the **RMSE (Root Mean Squared Error)** and **MAE (Mean Absolute Error)** on your test set[cite: 398]. Create a table comparing them.

2.  **Perform Crisis-Specific Testing:**
    * This is key to your research. [cite_start]Use your `is_crisis` column to test your model's performance *only* on the crisis periods in your test set[cite: 401].
    * Does the performance gap between your baseline and hybrid model get bigger during a crisis? This would be a very strong finding.

3.  **Analyze Feature Importance:**
    * [cite_start]Use techniques like SHAP or Permutation Importance to see which features your model relied on the most[cite: 399].
    * Was sentiment a top feature during crises? [cite_start]This provides a direct answer to **RQ4**[cite: 294].

4.  **Benchmark Against Other Models:**
    * [cite_start]As a final step, compare your hybrid model's performance against other models mentioned in your proposal, like **ARIMA** or a **CNN-BiLSTM**[cite: 400].

By completing these five phases, you will have followed your proposal, addressed your supervisor's feedback, and completed a full research cycle. Remember to document every step and every result in your Jupyter Notebook.