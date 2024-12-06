# Cooking Time, Calories, and Ratings: The Secret Ingredients of Recipe Success

Author: John Pan

## **Report for The Secret Ingredients of Recipe Success**

### **Introduction**
The **Recipes and Ratings** dataset is a treasure trove of information about user preferences and recipe characteristics, offering a detailed look at what makes a recipe successful. With **over 25,000 unique recipes** and a staggering **1.2 million user ratings**, this dataset provides a unique opportunity to analyze the factors that contribute to user satisfaction. It includes detailed recipe attributes such as cooking time, number of preparation steps, and nutritional content, allowing for a comprehensive exploration of what users value most in their cooking experiences.

#### **Central Question**
**What is the relationship between cooking time and the average rating of recipes?**

This question lies at the heart of understanding user preferences and is especially relevant for recipe developers, home cooks, and the culinary industry. Cooking time is a vital attribute that directly influences the accessibility, complexity, and perceived value of a recipe. By investigating this relationship, we aim to uncover actionable insights that can guide the creation of recipes aligned with user expectations.

For example, do users prefer recipes that are quick and easy to prepare, or do they appreciate recipes that require more time but deliver greater complexity or richer flavors? How does the balance between simplicity and sophistication impact user ratings? And to what extent do other factors, like the number of steps or nutritional content, interact with cooking time to shape user satisfaction?

In addition to understanding cooking time, this project incorporates additional recipe attributes—such as preparation complexity (measured by the number of steps) and nutritional elements like calories and protein—to assess their combined influence on user ratings. By considering these factors holistically, we aim to answer a broader question: what are the key attributes that make a recipe highly rated?

Furthermore, the dataset reveals fascinating anomalies that underscore the importance of data cleaning and exploratory analysis. For instance, one recipe titled "How to Preserve a Husband" listed a cooking time of an astonishing 1,051,200 minutes (approximately two years). Another recipe, despite being categorized as a quick dish, had a preparation time of over 10,000 minutes. Addressing these outliers is essential to ensure the reliability of our analysis and the accuracy of the predictive models we develop.

This project goes beyond descriptive analysis by building predictive models to determine which features most strongly influence user ratings. By doing so, we aim to provide insights that can guide both individual cooking decisions and professional recipe development. Whether you’re a home cook seeking inspiration or a professional chef aiming to craft recipes that resonate with audiences, the findings from this analysis will offer valuable, data-driven guidance.

#### **Introduction of Columns**

The dataset is composed of two CSV files: `RAW_recipes.csv` and `RAW_interactions.csv`. Each file contains distinct columns that contribute unique information:
- **name**: Title of the recipe, e.g., "1 brownies in the world best ever".  
- **minutes**: Time required to prepare the recipe in minutes.  
- **contributor_id**: Unique identifier for the user who contributed the recipe.  
- **submitted**: Date when the recipe was submitted.  
- **tags**: List of tags associated with the recipe, such as `['desserts', 'chocolate', 'easy']`.  
- **nutrition**: A string containing a list of nutritional values:  
  - `calories`, `total fat`, `sugar`, `sodium`, `protein`, `saturated fat`, and `carbs`.  
- **n_steps**: Number of steps required to complete the recipe.  
- **steps**: List of instructions for preparing the recipe.  
- **description**: Text description of the recipe.  
- **ingredients**: List of ingredients required for the recipe.  
- **n_ingredients**: Number of ingredients used in the recipe.  
- **date**: Date when the rating or review was submitted.  
- **rating**: User’s rating of the recipe on a scale from 0 to 5.  
- **review**: User's review of the recipe in free-text format.  

### **Data Cleaning**

Cleaning the dataset was a critical step to ensure the data was consistent, reliable, and ready for analysis. The cleaning process addressed multiple challenges, including parsing nested data, handling missing values, removing outliers, and merging datasets for a comprehensive view.

---

#### **Cleaning Process**

1. **Parsing the `nutrition` Column**  
   - The `nutrition` column in the recipes dataset stored values as strings that resembled Python lists (e.g., `"[138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]"`), which prevented direct analysis.  
   - Using Python’s `ast.literal_eval` function, we converted these stringified lists into actual Python lists.  
   - From the parsed lists, we extracted the following individual components into new columns:  
     - **calories**: Total calories per serving.  
     - **total fat** (in % Daily Value).  
     - **sugar** (in % Daily Value).  
     - **sodium** (in % Daily Value).  
     - **protein** (in % Daily Value).  
     - **saturated fat** (in % Daily Value).  
     - **carbohydrates** (in % Daily Value).  
   - This step allowed us to analyze each nutritional component individually and build predictive models using these attributes.

---

2. **Handling Missing Values**  
   - **Textual Data**: Missing values in the `description` column were replaced with "No description" to ensure all recipes had consistent metadata.  
   - **Numerical Data**: Missing ratings were replaced with the average rating (mean of **4.6**) to prevent skewing analysis and ensure complete data for modeling.  
   - **Nutrition Columns**: Missing values in nutrition attributes were minimal and were handled using column-wise means.  

---

3. **Identifying and Removing Outliers**  
   - Extreme and unrealistic values in the dataset were identified and removed. Examples include:  
     - Recipes with cooking times exceeding **10,000 minutes** or fewer than **1 minute** were flagged as unreasonable.  
     - The recipe titled **"How to Preserve a Husband"** had a cooking time of **1,051,200 minutes** (approximately two years) and was excluded.  
   - Removing these outliers ensured that statistical and predictive analyses would not be distorted.

---

4. **Merging Datasets**  
   - The `RAW_recipes.csv` and `RAW_interactions.csv` files were merged on the recipe `id`.  
   - From the interactions dataset, the average rating (`avg_rating`) was calculated for each recipe. This was added as a new column in the recipes dataset to provide a holistic view of user feedback on recipes.  
   - The resulting dataset contained both recipe metadata (e.g., cooking time, nutrition) and user feedback, enabling both exploratory analysis and predictive modeling.

---

#### **Cleaned Data Sample**

Below is the head of the cleaned DataFrame, showcasing key features prepared for analysis:

|     id |   minutes |   n_steps |   calories |   protein |   avg_rating |
|-------:|----------:|----------:|-----------:|----------:|-------------:|
| 333281 |        40 |        10 |      138.4 |         3 |            4 |
| 453467 |        45 |        12 |      595.1 |        13 |            5 |
| 306168 |        40 |         6 |      194.8 |        22 |            5 |
| 286009 |       120 |         7 |      878.3 |        20 |            5 |
| 475785 |        90 |        17 |      267.0 |        29 |            5 |

---

#### **Impact of Data Cleaning**

1. **Improved Usability**: Parsing the `nutrition` column into individual attributes enabled a detailed analysis of the relationship between nutritional content and user ratings.  
2. **Consistency**: Handling missing values ensured that all rows contributed to the analysis, avoiding bias introduced by incomplete data.  
3. **Robustness**: By identifying and removing outliers, we ensured that results and models were not skewed by erroneous data points.  
4. **Data Integration**: Merging the datasets provided a unified view of recipe attributes and user feedback, enabling both exploratory and predictive analysis.

### **Exploratory Data Analysis**

Exploratory Data Analysis (EDA) provides insights into the underlying patterns and relationships in the dataset. By visualizing key attributes, we can uncover trends that help explain the factors influencing recipe ratings. This section includes univariate and bivariate analyses, supported by interactive Plotly visualizations.


#### **Univariate Analysis**

1. **Cooking Time Distribution**  
   - **Insights**: The histogram of cooking times reveals that the majority of recipes fall within the **30–60 minute range**, indicating a preference for moderately time-intensive recipes. This range reflects the balance between quick convenience and the richness achieved with slightly longer cooking durations.  
   - **Outliers**: We identified recipes with unrealistically high cooking times, such as **"How to Preserve a Husband"** with a listed cooking time of **1,051,200 minutes**, which were removed during data cleaning.  
   - **Visualization**: The plot below highlights the distribution of cooking times, with the x-axis limited to **200 minutes** for clarity.  

   <iframe  
     src="assets/cooking-time.html"  
     width="800"  
     height="600"  
     frameborder="0"  
   ></iframe>  

2. **Average Rating Distribution**  
   - **Insights**: Recipe ratings are skewed heavily toward higher values, with a significant portion of recipes rated above **4.5**. This suggests that users are generally generous in their feedback, or the recipes in the dataset are of high quality.  
   - **Distribution Shape**: The skewness toward higher ratings aligns with the nature of user ratings, where extreme dissatisfaction (low ratings) is less common compared to moderate or high satisfaction.  
   - **Visualization**: The histogram below displays the distribution of average ratings across all recipes.  

   <iframe  
     src="assets/rating-distribution.html"  
     width="800"  
     height="600"  
     frameborder="0"  
   ></iframe>  


#### **Bivariate Analysis**

1. **Cooking Time vs. Average Rating**  
   - **Insights**: The scatter plot demonstrates a nuanced relationship between cooking time and average rating:  
     - Recipes with **extremely short cooking times** (under 10 minutes) or **very long cooking times** (over 120 minutes) tend to receive slightly lower ratings.  
     - Recipes in the **30–60 minute range** tend to have higher ratings, suggesting that users value a balance between effort and outcome.  
     - Some outliers exist, such as recipes with long cooking times but very high ratings (e.g., **slow-cooked dishes**).  
   - **Interpretation**: This relationship highlights the importance of managing expectations around cooking time. Recipes that demand too little or too much time may fail to deliver the user experience associated with optimal satisfaction.  
   - **Visualization**: The scatter plot below illustrates the relationship between cooking time and average rating, with clear trends and clustering around the moderate cooking time range.  

   <iframe  
     src="assets/cooking-time-vs-rating.html"  
     width="800"  
     height="600"  
     frameborder="0"  
   ></iframe>  


#### **Takeaways from EDA**

1. **Popular Recipe Characteristics**:  
   - Recipes in the **30–60 minute cooking time range** consistently perform better in terms of ratings.  
   - Highly rated recipes often balance effort and complexity with user-friendly preparation steps.  

2. **User Rating Patterns**:  
   - Most users are inclined to give high ratings, suggesting that quality control and user experience are critical factors in recipe success.  

3. **Importance of Data Cleaning**:  
   - Removing outliers and parsing nested data were essential to accurately understanding and visualizing these trends.  


### **Interesting Aggregates**

Aggregating the data provides a deeper understanding of how different factors, such as cooking time, influence user ratings. By grouping recipes into cooking time bins, we explored how average ratings varied across different preparation durations. This analysis helps identify trends and user preferences based on recipe timing.

#### **Analysis of Cooking Time Bins**

To examine the relationship between cooking time and average ratings, recipes were grouped into the following bins based on their preparation time:  

- **0–15 minutes**  
- **15–30 minutes**  
- **30–60 minutes**  
- **1–2 hours**  
- **2+ hours**

The table below summarizes the average ratings for recipes within each cooking time bin:  

| Cooking Time Bin    |   Average Rating |
|:--------------------|-----------------:|
| 0–15 minutes        |           4.6709 |
| 15–30 minutes       |           4.6234 |
| 30–60 minutes       |           4.6066 |
| 1–2 hours           |           4.6274 |
| 2+ hours            |           4.5889 |

#### **Insights from Aggregates**

1. **User Preferences for Moderately Timed Recipes**  
   - Recipes in the **0–15 minute range** had the highest average ratings (**4.6709**). These recipes often prioritize convenience and quick preparation, catering to busy users seeking simple yet satisfying meals.  
   - Recipes with cooking times between **30 and 60 minutes** also performed well, with an average rating of **4.6066**. This range strikes a balance between effort and reward, offering recipes that are more complex than quick meals but less time-intensive than elaborate dishes.  

2. **Slightly Lower Ratings for Long Cooking Times**  
   - Recipes requiring **2+ hours** received slightly lower average ratings (**4.5889**). Although long cooking times can result in rich and flavorful dishes, they may also demand significant time and effort, which might deter users.  

3. **Surprising Performance of 1–2 Hour Recipes**  
   - Recipes in the **1–2 hour range** achieved relatively high average ratings (**4.6274**), suggesting that users appreciate the outcomes of moderately long cooking durations, particularly for specific categories such as stews, roasts, or baked goods.  

#### **Takeaways from Aggregate Analysis**

- **Time Matters**: Cooking time has a nuanced effect on user satisfaction. While users appreciate quick and moderately timed recipes, there is also room for highly rated longer recipes if the effort-to-reward ratio is perceived as worthwhile.  
- **Category-Specific Patterns**: Further exploration could analyze recipe types (e.g., desserts vs. main courses) within each time bin to see how specific categories impact ratings.  
- **Data Cleaning’s Role**: Identifying and removing outliers like recipes with unrealistic times (e.g., **"How to Preserve a Husband"**) was essential to obtaining meaningful aggregates.  

This aggregate analysis complements our earlier exploratory data analysis and reinforces the importance of cooking time in predicting user satisfaction.  


### **Assessment of Missingness**

#### **Handling Missing Values**
The dataset required careful attention to missing values to ensure the analyses were robust and accurate. Below is a summary of the missing data handling process and its impact on the dataset:

#### **Steps Taken**

1. **Identifying Missing Data**  
   - **Description Column**: Missing values in the `description` column were identified. These entries represented recipes with no user-provided textual information about the dish.
   - **Average Rating Column**: Some recipes lacked user ratings (`avg_rating`), either because they were new or received insufficient feedback.

2. **Imputation Strategy**
   - **Description Column**: Missing values were replaced with "No description" to provide a neutral placeholder, ensuring textual analyses could proceed without interruptions.
   - **Average Rating Column**: Missing values in `avg_rating` were imputed using the **mean value of all available ratings**. This approach was chosen as the simplest method to preserve overall trends in the dataset without introducing additional bias.


#### **Insights from Imputation**
1. **Distributions Before and After Imputation**  
   - Before imputation, missing values caused the rating distribution to exhibit small gaps and inconsistencies, particularly in regions where recipes were unrated.  
   - After imputation, the distribution smoothed out, especially around the mean rating of **4.6**, reducing gaps and ensuring all recipes had a rating value.  

2. **Justification for Using the Mean**  
   - Using the mean preserves the dataset's central tendencies without introducing extreme outliers.  
   - While it may slightly overestimate or underestimate individual ratings, it ensures the imputed values are not biased toward either high or low ratings.



#### **Impact on Analyses**
- **Improved Completeness**: Imputing missing ratings ensured that all recipes had a valid `avg_rating` value, enabling downstream predictive modeling.
- **Bias Mitigation**: Using the mean value minimized the risk of introducing significant deviations from the overall dataset trends. However, this approach assumes that the missing ratings are not systematically biased (e.g., recipes with no ratings are not inherently better or worse than rated recipes).

### **Prediction Problem**

#### **Objective**
The goal of this project is to predict the **average rating** of a recipe, which reflects user satisfaction, based on its key attributes. These attributes include cooking time, the number of steps involved in preparation, and various nutritional components such as calorie and protein content. By analyzing these factors, we aim to identify patterns and features that contribute to a highly-rated recipe. Understanding what drives user satisfaction can provide valuable insights for recipe developers, meal planning services, and culinary enthusiasts, enabling them to craft recipes that align with user preferences. Furthermore, this analysis sheds light on the relationship between recipe complexity, nutritional values, and overall user feedback, offering actionable guidance for optimizing the recipe creation process.

#### **Approach**
To predict the **average rating (`avg_rating`)** of recipes, we adopted a **regression-based approach**, as the target variable is continuous and ranges between 0 and 5. The analysis focused on identifying the relationships between recipe features and user ratings, ensuring that the model could generalize well to unseen data.

- **Problem Type**: Regression  
   Predicting average ratings requires modeling a continuous output variable. This approach allows us to understand how different recipe attributes influence user satisfaction.

- **Evaluation Metric**: Mean Squared Error (MSE)  
   MSE was chosen as the primary metric because it penalizes larger errors more heavily, making it suitable for identifying discrepancies between predicted and actual ratings. A lower MSE indicates better model performance, emphasizing the importance of accurate predictions.

- **Features**:  
   The following key recipe attributes were used as predictors in the regression model:  
   - **`minutes`**: Reflects the total cooking time. Cooking time has been shown to correlate with ratings, with moderate times (30–60 minutes) often yielding higher satisfaction.  
   - **`n_steps`**: Represents the number of steps in the recipe. This captures recipe complexity, which can affect user perceptions. Recipes that are too simple or overly complex may receive lower ratings.  
   - **`calories`**: Indicates the calorie content of the recipe. This feature provides insight into user preferences for indulgent or health-conscious recipes.  
   - **`protein`**: The protein content is a critical nutritional factor, especially for users seeking balanced or high-protein meals.

### **Baseline Model**

#### **Setup**
To establish a starting point for our prediction task, we trained a **Baseline Model** using a **Random Forest Regressor**, a robust machine-learning algorithm capable of capturing non-linear relationships in the data. The model utilized the following key features, selected based on exploratory data analysis and domain relevance:

1. **`minutes`**: Total cooking time, capturing how the duration of a recipe impacts user satisfaction.
2. **`n_steps`**: Number of steps in the recipe, which reflects complexity and ease of preparation.
3. **`calories`**: Calorie content of the recipe, providing insight into user preferences for health-conscious or indulgent options.
4. **`protein`**: Protein content, a significant nutritional attribute that aligns with user dietary goals.

The features were included without additional transformations, allowing the model to learn patterns directly from the raw data. By focusing on these attributes, the baseline model aimed to uncover fundamental relationships between recipe characteristics and user ratings.

#### **Results**
- **Baseline Model MSE**: `0.455`  

The baseline model achieved a Mean Squared Error (MSE) of **0.455** on the test set, offering a preliminary measure of how well recipe attributes predict average ratings. While the model successfully captured some relationships in the data, this result leaves room for improvement, particularly in fine-tuning the model and engineering additional features.

#### **Significance**
The baseline model served as an essential benchmark for the project, providing:
1. A **foundation** for assessing subsequent improvements in prediction accuracy.
2. Insights into the predictive power of the selected features, offering evidence of their relevance to the problem.
3. A clear metric (MSE) against which more sophisticated models could be evaluated.  

### **Final Model**

#### **Improvements**
The **Final Model** aimed to enhance prediction accuracy by addressing the limitations of the baseline model. Two primary strategies were implemented to achieve this:

1. **Feature Engineering**:  
   - A new feature, **`calories-per-minute`**, was created by dividing the `calories` column by the `minutes` column. This feature captured the caloric density of recipes relative to their cooking time, offering additional insight into user preferences for efficient, high-calorie meals versus longer, lower-calorie options.  
   - This addition was motivated by the observation that both `calories` and `minutes` had significant relationships with `avg_rating`, and combining them provided a nuanced metric for analysis.

2. **Hyperparameter Tuning**:  
   - The hyperparameters of the **Random Forest Regressor** were optimized using **GridSearchCV** to balance model complexity and predictive power.  
   - The following hyperparameters were tuned:
     - **`n_estimators`**: Number of trees in the forest (tested values: 50, 100, 200).  
     - **`max_depth`**: Maximum depth of each tree (tested values: 10, 20, None).  
     - **`min_samples_split`**: Minimum number of samples required to split a node (tested values: 2, 5, 10).  
   - The best combination of hyperparameters was selected based on the lowest cross-validated Mean Squared Error (MSE).

#### **Results**
- **Final Model MSE**: **0.4123**  
  The final model achieved a Mean Squared Error of **0.4123**, a substantial improvement over the baseline MSE of **0.455**, demonstrating that feature engineering and hyperparameter tuning enhanced the model's predictive accuracy.

- **Feature Importance**:  
  The final model provided valuable insights into the relative importance of features in predicting `avg_rating`:
  1. **`calories`**: **44.63%** – The most critical predictor, highlighting the role of caloric content in user satisfaction. Recipes with balanced or high-calorie content appear to resonate more with users.  
  2. **`protein`**: **20.83%** – A key nutritional metric, reflecting the growing demand for protein-rich recipes.  
  3. **`minutes`**: **19.79%** – Cooking time remained significant, with moderate durations (30–60 minutes) generally preferred.  
  4. **`n_steps`**: **14.74%** – Recipe complexity, as measured by the number of steps, played a lesser but notable role in determining ratings.

#### **Significance**
The **Final Model** underscores the following key findings:
1. **Caloric Content**: Recipes with well-balanced caloric values are highly influential in shaping user satisfaction. This insight can guide recipe creators in optimizing nutritional profiles.  
2. **Protein Popularity**: The importance of `protein` aligns with health-conscious trends and dietary preferences, emphasizing the need for recipes to cater to such demands.  
3. **Cooking Efficiency**: `minutes` and `calories-per-minute` reveal that users value recipes that balance preparation time with nutritional value, favoring options that are both efficient and fulfilling.  


### **Conclusions**

#### **1. Key Findings**
Through this analysis of the **Recipes and Ratings** dataset, several meaningful insights emerged:  
- Recipes with **moderate cooking times** (30–60 minutes) received the highest average ratings, emphasizing user preference for recipes that balance effort and time.  
- **Caloric content** proved to be the strongest predictor of user satisfaction, with users gravitating toward recipes that align with their caloric expectations and nutritional preferences.  
- Other features, such as **protein content** and **cooking complexity (n_steps)**, also played critical roles, though to a lesser extent, reflecting the nuanced ways users assess recipes.

#### **2. Practical Implications**
This study provides actionable recommendations for recipe developers and content creators:  
- **Optimize Cooking Times**: Focus on developing recipes that fall within the 30–60 minute range to meet user preferences for moderate preparation times.  
- **Caloric Balance**: Pay attention to caloric content, ensuring recipes align with popular dietary goals, whether they are protein-rich, calorie-efficient, or indulgent.  
- **Simplicity and Accessibility**: Consider streamlining recipes with fewer, straightforward steps to appeal to a broader audience, while maintaining variety for more adventurous cooks.  

#### **3. Challenges Addressed**
- **Outliers**: Extreme outliers, such as "How to Preserve a Husband," which had an absurd cooking time of **1,051,200 minutes**, were successfully excluded to maintain the integrity of the analysis.  
- **Missing Values**: Imputation techniques were used to handle missing ratings, ensuring the dataset was complete and usable without introducing bias.

#### **4. Future Work**
While this analysis uncovered valuable patterns, several directions remain for further exploration:  
- **Incorporating More Features**: Expanding the analysis to include categorical features like **tags** (e.g., "vegetarian," "quick-meals") and detailed ingredient lists could provide deeper insights into user preferences.  
- **Classification Tasks**: Predict whether a recipe is highly rated (e.g., above 4.5) as a binary classification task, which may offer a new perspective for recipe recommendations.  
- **User Segmentation**: Explore how user demographics and preferences influence ratings to tailor recipe development further.  
- **Exploring Alternative Models**: Experiment with neural networks, gradient boosting, or ensemble methods to further improve prediction accuracy.

By combining these findings with future research, recipe developers and food platforms can create more engaging, user-centered content, ultimately enriching the culinary experience for their audiences.
