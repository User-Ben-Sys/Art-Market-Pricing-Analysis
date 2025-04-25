# Art Market Pricing Analysis

This project presents a data-driven exploration into the art market to understand what truly drives the price of artwork. By analyzing auction data and artist-related features, the study investigates both technical and intangible factors that contribute to how artworks are valued.

---

## Problem Statement

The art market has long been considered opaque and unpredictable. Prices can vary dramatically—even for artworks that appear similar in size, medium, or origin. For example, a banana duct-taped to a wall once sold for $6.2 million, prompting both fascination and confusion across the art and investment communities.

Is the price of artwork primarily influenced by historical significance, artistic style, or the fame of the artist? Does the choice between collecting an Old Master like Da Vinci or a Modern icon like Banksy impact return on investment? These questions reflect a deeper challenge:  
**What truly determines the value of an artwork?**

---
## Table of Contents

1. [Objective](#1-objective)
2. [Questions I Asked](#2-questions-i-asked)
3. [Tools Used](#3-tools-used)
4. [End-to-End Workflow](#4-end-to-end-workflow)
    - [1. Data Extraction](#41-data-extraction)
    - [2. Feature Engineering](#42-feature-engineering)
    - [3. Handling Missing Data](#43-handling-missing-data)
    - [4. Modeling](#44-modeling)
    - [5. Storytelling & Visualization](#45-storytelling--visualization)
5. [Python Modeling Summary](#5-python-modeling-summary)
6. [Key Insights](#6-key-insights)
7. [Recommendations](#7-recommendations)
8. Documentation
    - 📁 *Dataset* 
        - *Not included* due to scraping restrictions. For academic use only.  
        Contact me directly for access related to review or learning. purposes.
    - 🐍 [Modelling](./python/)  
    - 🖼️ [Presentation Deck](https://drive.google.com/file/d/1TNYARFc0Yyj4ERWcai4D5n3ftVvQSgXe/view?usp=share_link)

---

## 1. Objective

This project aims to explore what drives artwork prices by using real auction data and visual analysis.

The goals are to:
- Identify key factors that influence how much an artwork sells for.
- Understand how both traditional and modern elements affect value.

By connecting data with the art world, this project hopes to offer useful insights for collectors, investors, and anyone curious about how art is valued.

---

## 2. Questions I Asked

To guide the exploratory analysis, I began by asking a series of questions that challenge common assumptions in the art world:

- Do **Old Masters** consistently outperform **Modern Contemporary** artists at auction?
- Does the **age of an artwork** directly influence its price?
- Is there any truth to the claim that **“dead artists sell”**?
- How much does an artist’s **reputation or critical acclaim** impact valuation?

These questions helped shape the direction of the analysis and informed which features to engineer, visualize, and model throughout the project.

---

## 3. Tools Used

| Tool / Platform        | Purpose / Usage                                      |
|----------------------|----------------------------------------------------|
| **Python**           | Data analysis (Pandas, NumPy), modeling (Scikit-learn), and visualization (Matplotlib, Seaborn) |
| **Jupyter Notebooks**| Step-by-step coding, exploratory analysis, and documentation |
| **Tableau**          | Visual exploration, charts, and interactive dashboards |
| **PyMuPDF**          | Extracting structured data from PDF documents       |
| **RegEx** | Parsing and cleaning raw text data |
| **BeautifulSoup / Selenium** | Web scraping from auction and artist platforms |
| **Excel**            | Data cleaning, sanity checks, and light feature engineering |
| **PowerPoint**       | Presenting insights and final storytelling to stakeholders |

---

## 4. End-to-End Workflow

### 4.1. **Data Extraction**
Having domain knowledge helped guide what data to collect and where to find it—avoiding unnecessary cleaning or irrelevant scraping.

- **PyMuPDF:** Extracted auction records (artist, price, artwork info) directly from PDFs provided by auction houses.
- **BeautifulSoup & Selenium:** Scraped individual artist sales histories and follower counts from online art platforms.
- **Excel:** Used for initial cleanup and structuring fields like artwork dimensions, artist name formatting, and currency normalization.

---

### 4.2. **Feature Engineering**
Good feature engineering adds richness and context to the data. Here's how I approached it:

#### **Categorical Features:**

- **Medium Category:** Labeled artworks as painting, sculpture, drawing, etc., based on auction descriptions.
- **Size Category:** Grouped artworks by size (e.g., mural-scale, large-scale) using area.
- **Art Era:** Classified artists as Old Masters or Modern Contemporary based on auction house classification.

#### **Calculated Features:**

- `artist_age_at_death = death_year - birth_year`  
- `artist_age_at_creation = year_created - birth_year`  
- `painting_age = 2025 - year_created`  
- `painting_size_cm² = width * height`

#### **Boolean Flags:**

- **is_artist_deceased**: Derived from whether a death year was recorded.  
- **is_artwork_sold**: Flags if the artwork has a recorded final sale price.  
- **sold_below_low_estimate**, **sold_above_median_estimate**, **sold_above_high_estimate**: Indicate how the final sale performed relative to the estimated price ranges.

#### **Aesthetic Features:**

With a strong personal interest and domain knowledge in art, I wanted to explore whether subjective and emotional aspects could influence pricing—beyond just technical or market-driven features.

To test this, I manually labeled each artwork's:

- **Style** (e.g., Abstraction, Expressionism, Pop Art)
- **Subject** (e.g., Figurative, Events, Animal)
- **Color** (e.g., Bold, Monochromatic, Vibrant)
- **Mood** (e.g., Iconic, Controversial, Serene)

I also used GPT-based tools to assist with image descriptors. AI can be used efficiently to support manual work while saving time and improving consistency.

---

### 4.3. **Handling Missing Data**

Working with historical art means incomplete records—especially for Old Masters. Here is how I addressed key gaps in the dataset:

#### **Sell-Through Rate / Yearly Lots Sold / Average Sales Price**

**Old Masters** don’t appear in auctions frequently, making these metrics hard to capture. To avoid bias and preserve model input quality, I filled these fields with **conservative, randomized values** based on typical patterns seen in auction data:

- **Sell-Through Rate**: Simulated values between 60–75% to reflect average success rates for historical works.  
- **Yearly Lots Sold**: Assigned values between 0–3 based on recent years' auction frequency for each artist.  
- **Average Sales Price**: Estimated using the median of available final sold prices for each artist based on data from Artsy.net and Mutual Art.

#### **Year Created**

Many older artworks lack a clearly recorded creation year. In those cases, I estimated the value by taking the **median year between the artist's birth and death**, as a practical proxy.

#### **Final Sold Price**

Not all auction listings result in a sale, which means the final price can be missing.  
To allow for consistent model training and evaluation, I **imputed the final sold price using the median of the estimated price range** for unsold artworks.

---

### 4.4. Modeling

To understand what drives artwork pricing and predict final sold prices, I selected three different modeling approaches—each suited to different strengths of the dataset and problem type.

**Linear Regression**

I started with **Linear Regression** as a baseline model. It’s simple, interpretable, and useful for identifying which features have a linear relationship with price. While it doesn't capture complex interactions, it provides a good starting point to test the strength of the numeric and engineered features.

#### **Random Forest**

**Random Forest** is well-suited for this type of structured tabular data and handles both categorical and numerical features effectively. It’s robust to outliers and missing values, and it captures **non-linear relationships** between features—important in a dataset where pricing may not scale linearly with size, reputation, or emotion-driven attributes.

#### **Gradient Boosting**

I chose **Gradient Boosting**for its ability to fine-tune performance and handle feature importance more aggressively. This model is particularly good at **capturing subtle patterns** and reducing bias, especially when dealing with uneven distributions like rare high-value sales. It performed well in terms of prediction accuracy and helped confirm which features had the greatest impact on pricing.

---

> *Each model offered a unique lens to understand how technical, market, and aesthetic features influence artwork value. Together, they helped build a more complete picture of the factors driving pricing in the art market.*

---

### 4.5. Storytelling & Visualization

To bring the data to life and communicate findings clearly, I used a mix of exploratory charts and post-modeling visualizations.

#### **Exploratory Data Visualizations (EDA)**
- To answer key questions I posed early on, I created:
  - **Bar chart** to compare how well Old Masters vs. Modern Contemporary artists perform in auctions.
  - **Bar chart** to explore whether deceased artists tend to sell more.
  - **Scatter plot** to investigate whether there’s a relationship between an artwork’s age and its final sold price.
  - **Bar-in-bar chart** to examine if an artist’s follower count is linked to their sales performance.

#### **Post-Modeling Visualizations**
After running the models, I identified three features that consistently ranked high in importance across both Random Forest and Gradient Boosting:
- **Painting height**
- **Artist’s average sales price**
- **Yearly lots sold**

To visualize these insights:
- **Bar chart** to show which artwork size category (e.g., mural-scale, small-scale) achieved the highest sales.
- **Line chart** to show how yearly lots sold impacts pricing—highlighting points where increased supply causes price drops or spikes.
- **Box plot** to show how artist average sales price varies across different groupings, helping identify pricing stability and volatility.

## 5. Python Modeling Summary

This project compares the performance of three models in predicting artwork prices, both **before and after adding aesthetic features** (e.g., style, mood, color, subject).

---

### 📊 Model 1: Without Aesthetic Features

| Model              | Train R² | Test R² | Train RMSE     | Test RMSE     |
|--------------------|----------|---------|----------------|---------------|
| Linear Regression  | 100%     | 98%     | > 0.001        | 43,124        |
| Random Forest      | 99%      | 96%     | 112,704.02     | 195,188       |
| Gradient Boosting  | 94%      | 94%     | 71,191.71      | 82,831.15     |

---

### 🎨 Model 2: With Aesthetic Features (Style, Subject, Color, Mood)

| Model              | Train R² | Test R² | Train RMSE     | Test RMSE     | RMSE Improvement |
|--------------------|----------|---------|----------------|---------------|------------------|
| Linear Regression  | 100%     | 97%     | > 0.001        | 2,986         | ▲ 93%            |
| Random Forest      | 99%      | 89%     | 2,296.44       | 6,548         | ▲ 97%            |
| Gradient Boosting  | 94%      | 92%     | 3,871.73       | 4,909.37      | ▲ 94%            |

---

> *Including aesthetic features resulted in a **notable improvement in predictive power**, reinforcing the **subjective nature of art valuation**. This confirms that how an artwork looks—and how it makes people feel—can strongly influence its market value.*

---

## 6. Key Insights

1. **Modern & Contemporary art dominates** the market due to greater supply and demand, while **Old Masters are rarer** and tend to show **more stable pricing**.
2. **Age doesn't guarantee value** — some 700-year-old paintings sell for under $1M, while modern artworks continue to break records.
3. **Demand outweighs supply** for top artists — living artists like *Banksy* and *Damien Hirst* can sustain high prices despite mass production.
4. **Social media following isn't everything** — artists like *Botticelli* and *Stuart Davis* prove that reputation can drive value, not online popularity.
5. **Size matters** — larger artworks, particularly **mural-scale pieces**, consistently command higher prices.
6. **Aesthetic features are wildcards** — factors like **style, subject, color, and mood** can strongly influence pricing, though often in unpredictable ways.

---

## 7. Recommendations

Your investment strategy depends on your goals and risk appetite:

- **Long-term investment?**  
  Focus on **legendary Old Masters** like *Botticelli* and *Sir Peter Paul Rubens*. These pieces are rare, historically significant, and tend to **appreciate over time** — but expect tough competition from institutions and museums.

- **Short-term collecting or flipping?**  
  Modern Contemporary is your playground. These works are **highly volatile but exciting**, often as fun and speculative as collecting Pokémon cards. The right piece can be **extremely valuable** if timed well.

- **Choose bigger when you can.**  
  Larger artworks typically have **stronger visual impact**, better presence, and make great talking points — which often translates into higher market value.

- **Let aesthetics guide you.**  
  Use **trending aesthetic signals** — like popular styles, emotional subjects, bold colors, and iconic moods — to help spot **the next high-value artwork**.

---
> 💡*For a more detailed analysis, please [view the full presentation here](https://drive.google.com/file/d/1TNYARFc0Yyj4ERWcai4D5n3ftVvQSgXe/view?usp=share_link)*


## 🤝 Let's Connect

If you found this project insightful or have thoughts to share, feel free to reach out!

- 💼 [LinkedIn](https://www.linkedin.com/in/ben-ja-min)  
- 📫 [Email](mailto:benkim1314@gmail.com)