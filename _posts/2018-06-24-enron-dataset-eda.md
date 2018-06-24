---
layout: post
title: "Enron Dataset: Exploratory Data Analysis"
date: "2018-06-24"
slug: "enron-dataset-eda"
description: "The Enron scandal provides a great reservoir for data analysis. This blog is the first piece to explore the Enron dataset, where exploratory data analysis is carried out and paves the way for feature engineering and modeling."
category: 
  - data science
  - featured
  - exploratory data analysis
# tags will also be used as html meta keywords.
tags:
  - eda
  - data analysis
  - data science
show_meta: true
mathjax: true
gistembed: true
published: true
hide_printmsg: true
show_meta: false
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.1.10/require.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>

{% highlight python %}
import sys, pickle, os, warnings
import matplotlib.pyplot as plt
import pandas as pd, seaborn as sns, numpy as np

warnings.filterwarnings('ignore') # Suppress warning messages.
os.chdir("/Users/ray/Documents/ud120-projects/final_project/")
pd.set_option("max_columns", 999)
{% endhighlight %}


{% highlight python %}
# Load the dictionary containing the dataset, and convert the dictionary to dataframe.
with open("final_project_dataset.pkl", "r") as data_file:
    data_dict = pickle.load(data_file) 
data_frame = pd.DataFrame.from_dict(data_dict, orient="index")

# Convert supposedly numeric columns to type numeric.
cols = data_frame.columns.drop(["poi", "email_address"])
data_frame[cols] = data_frame[cols].apply(pd.to_numeric, errors="coerce")

# Add a name column and change the originally name index to numeric index.
data_frame.drop(["email_address"], axis=1, inplace=True)
data_frame["name"] = data_frame.index
data_frame.index = xrange(len(data_frame))
{% endhighlight %}


{% highlight python %}
data_frame.head(5)
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left; overflow-x: auto;">
      <th></th>
      <th>salary</th>
      <th>to_messages</th>
      <th>deferral_payments</th>
      <th>total_payments</th>
      <th>exercised_stock_options</th>
      <th>bonus</th>
      <th>restricted_stock</th>
      <th>shared_receipt_with_poi</th>
      <th>restricted_stock_deferred</th>
      <th>total_stock_value</th>
      <th>expenses</th>
      <th>loan_advances</th>
      <th>from_messages</th>
      <th>other</th>
      <th>from_this_person_to_poi</th>
      <th>poi</th>
      <th>director_fees</th>
      <th>deferred_income</th>
      <th>long_term_incentive</th>
      <th>from_poi_to_this_person</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>201955.0</td>
      <td>2902.0</td>
      <td>2869717.0</td>
      <td>4484442.0</td>
      <td>1729541.0</td>
      <td>4175000.0</td>
      <td>126027.0</td>
      <td>1407.0</td>
      <td>-126027.0</td>
      <td>1729541.0</td>
      <td>13868.0</td>
      <td>NaN</td>
      <td>2195.0</td>
      <td>152.0</td>
      <td>65.0</td>
      <td>False</td>
      <td>NaN</td>
      <td>-3081055.0</td>
      <td>304805.0</td>
      <td>47.0</td>
      <td>ALLEN PHILLIP K</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>178980.0</td>
      <td>182466.0</td>
      <td>257817.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>257817.0</td>
      <td>3486.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>BADUM JAMES P</td>
    </tr>
    <tr>
      <th>2</th>
      <td>477.0</td>
      <td>566.0</td>
      <td>NaN</td>
      <td>916197.0</td>
      <td>4046157.0</td>
      <td>NaN</td>
      <td>1757552.0</td>
      <td>465.0</td>
      <td>-560222.0</td>
      <td>5243487.0</td>
      <td>56301.0</td>
      <td>NaN</td>
      <td>29.0</td>
      <td>864523.0</td>
      <td>0.0</td>
      <td>False</td>
      <td>NaN</td>
      <td>-5104.0</td>
      <td>NaN</td>
      <td>39.0</td>
      <td>BANNANTINE JAMES M</td>
    </tr>
    <tr>
      <th>3</th>
      <td>267102.0</td>
      <td>NaN</td>
      <td>1295738.0</td>
      <td>5634343.0</td>
      <td>6680544.0</td>
      <td>1200000.0</td>
      <td>3942714.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10623258.0</td>
      <td>11200.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2660303.0</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>-1386055.0</td>
      <td>1586055.0</td>
      <td>NaN</td>
      <td>BAXTER JOHN C</td>
    </tr>
    <tr>
      <th>4</th>
      <td>239671.0</td>
      <td>NaN</td>
      <td>260455.0</td>
      <td>827696.0</td>
      <td>NaN</td>
      <td>400000.0</td>
      <td>145796.0</td>
      <td>NaN</td>
      <td>-82782.0</td>
      <td>63014.0</td>
      <td>129142.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>69.0</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>-201641.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>BAY FRANKLIN R</td>
    </tr>
  </tbody>
</table>
</div>




{% highlight python %}
# Inspect number of data points in each class.
print data_frame.groupby(["poi"]).size()
print 
print "Portion of poi in the dataset:", round(float(data_frame.groupby(["poi"]).size()[1])/len(data_frame),3)*100
{% endhighlight %}

{% highlight html %}
    poi
    False    128
    True      18
    dtype: int64
    
    Portion of poi in the dataset: 12.3
{% endhighlight %}

Since the majority of class is not poi, we will need to take care of this imbalance situation later. This is because in the modeling section, blindly assigning all training points to non-poi's will yield an inflated accuracy of 87.7%, which does not tell the true story at all. 


{% highlight python %}
# Inspect number of NA's after applying type conversion.
data_frame.isna().apply(sum)
{% endhighlight %}


{% highlight html %}
    salary                        51
    to_messages                   60
    deferral_payments            107
    total_payments                21
    exercised_stock_options       44
    bonus                         64
    restricted_stock              36
    shared_receipt_with_poi       60
    restricted_stock_deferred    128
    total_stock_value             20
    expenses                      51
    loan_advances                142
    from_messages                 60
    other                         53
    from_this_person_to_poi       60
    poi                            0
    director_fees                129
    deferred_income               97
    long_term_incentive           80
    from_poi_to_this_person       60
    name                           0
    dtype: int64
{% endhighlight %}

All columns, except poi, email_address, and name, have missing values. The number of missing values in each column cannot be simply ignored. Therefore, try to fill the missing values.
<br><br>
    A careful examination of the document (<em>enron61702insiderpay</em>) shows that we can fill all missing values with 0. This is because the financial data are extracted from that document. NaN values in that document are in fact deliberately left blank, because 1. no information exist for that person or 2. columns other than total_payments and total_stock_value should add up to total_payments and total_stock_value, respectively.


{% highlight python %}
# Fill missing values with 0.
data_frame = data_frame.fillna(value=0)

data_frame.isna().apply(sum)
{% endhighlight %}


{% highlight html %}
    salary                       0
    to_messages                  0
    deferral_payments            0
    total_payments               0
    exercised_stock_options      0
    bonus                        0
    restricted_stock             0
    shared_receipt_with_poi      0
    restricted_stock_deferred    0
    total_stock_value            0
    expenses                     0
    loan_advances                0
    from_messages                0
    other                        0
    from_this_person_to_poi      0
    poi                          0
    director_fees                0
    deferred_income              0
    long_term_incentive          0
    from_poi_to_this_person      0
    name                         0
    dtype: int64
{% endhighlight %}


After filling missing values, we should check if columns of finance data add up to total payments or total stock values.


{% highlight python %}
income_df = (data_frame[["salary","deferral_payments","bonus", "expenses", "loan_advances", "other", "director_fees", 
                         "deferred_income", "long_term_incentive"]].sum(axis=1))
income_diff = data_frame["total_payments"] - income_df
print income_diff[income_diff>0]

print 

stock_df = (data_frame[["exercised_stock_options", "restricted_stock", "restricted_stock_deferred"]].sum(axis=1))
stock_diff = data_frame["total_stock_value"] - stock_df
print stock_diff[stock_diff>0]
{% endhighlight %}

{% highlight html %}
    8       201715.0
    11    15180562.0
    dtype: float64
    
    Series([], dtype: float64)
{% endhighlight %}

Looks like 9th and 12th person have non-zero results. We need to correct the data for these two persons.


{% highlight python %}
# Correct the data for 9th and 12th person.
data_frame.at[8, "exercised_stock_options"] = 0
data_frame.at[8, "total_stock_value"] = 0
data_frame.at[8, "total_payments"] = 3285
data_frame.at[8, "restricted_stock_deferred"] = -44093
data_frame.at[8, "restricted_stock"] = 44093
data_frame.at[8, "director_fees"] = 102500
data_frame.at[8, "deferred_income"] = -102500
data_frame.at[8, "deferral_payments"] = 0
data_frame.at[8, "expenses"] = 3285

data_frame.at[11, "exercised_stock_options"] = 15456290
data_frame.at[11, "total_stock_value"] = 15456290
data_frame.at[11, "total_payments"] = 137864
data_frame.at[11, "restricted_stock_deferred"] = -2604490
data_frame.at[11, "restricted_stock"] = 2604490
data_frame.at[11, "director_fees"] = 0
data_frame.at[11, "deferred_income"] = 0
data_frame.at[11, "deferral_payments"] = 0
data_frame.at[11, "expenses"] = 137864
{% endhighlight %}


{% highlight python %}
# Check those two persons again to see if the data have been corrected.
income_df = (data_frame[["salary","deferral_payments","bonus", "expenses", "loan_advances", "other", "director_fees", 
                         "deferred_income", "long_term_incentive"]].sum(axis=1))
income_diff = data_frame["total_payments"] - income_df
print income_diff[income_diff>0]

print 

stock_df = (data_frame[["exercised_stock_options", "restricted_stock", "restricted_stock_deferred"]].sum(axis=1))
stock_diff = data_frame["total_stock_value"] - stock_df
print stock_diff[stock_diff>0]
{% endhighlight %}

{% highlight html %}
    Series([], dtype: float64)
    
    Series([], dtype: float64)
{% endhighlight %}

{% highlight python %}
# First visualize the distribution of each column that is about a person's emails.
plot_email_data = (data_frame[["to_messages","shared_receipt_with_poi","from_messages","from_this_person_to_poi",
                               "from_poi_to_this_person"]])

sns.set(); sns.set_context("talk") # Set default aesthetics; change context to talk.
fig = plt.figure(figsize=(10,8), dpi=90)
for plot_idx in xrange(5):
    ax = plt.subplot(2, 3, plot_idx+1)
    sns.boxplot(plot_email_data[plot_email_data.columns[plot_idx]], ax=ax, orient="v", width=0.4, color="#41f483")
    ax.set_xlabel(plot_email_data.columns[plot_idx]); ax.set_ylabel("")
    
plt.tight_layout()
{% endhighlight %}


![png]({{ "/images/output_13_0.png" | absolute_url }})



{% highlight python %}
fig = plt.figure(figsize=(12,8), dpi=90)
for plot_idx in xrange(5):
    ax = plt.subplot(3, 2, plot_idx+1)
    sns.distplot(plot_email_data[plot_email_data.columns[plot_idx]], ax=ax, kde=False, color="#4341f4")
    
plt.tight_layout()
{% endhighlight %}


![png]({{ "/images/output_14_0.png" | absolute_url }})



{% highlight python %}
# Then visualize the distribution of each column that is about a person's finance .
plot_finance_data = (data_frame[["salary","deferral_payments","total_payments","exercised_stock_options","bonus",
                              "restricted_stock", "restricted_stock_deferred", "total_stock_value", "expenses", 
                               "loan_advances", "other", "director_fees", "deferred_income", "long_term_incentive"]])

sns.set(); sns.set_context("talk") # Set default aesthetics; change context to talk.
fig = plt.figure(figsize=(10,20), dpi=90)

for plot_idx in xrange(len(plot_finance_data)):
    try:
        ax = plt.subplot(5, 3, plot_idx+1)
        sns.boxplot(plot_finance_data[plot_finance_data.columns[plot_idx]], ax=ax, orient="v", width=0.4, 
                    color="#41f483")
        ax.set_xlabel(plot_finance_data.columns[plot_idx]); ax.set_ylabel("")
    except IndexError:
        continue
    except ValueError:
        continue
    
plt.tight_layout()
{% endhighlight %}


![png]({{ "/images/output_15_0.png" | absolute_url }})



{% highlight python %}
fig = plt.figure(figsize=(12,22), dpi=90)
for plot_idx in xrange(len(plot_finance_data)):
    try:
        ax = plt.subplot(7, 2, plot_idx+1)
        sns.distplot(plot_finance_data[plot_finance_data.columns[plot_idx]], ax=ax, kde=False, color="#4341f4")
    except IndexError:
        continue
    except ValueError:
        continue
    
plt.tight_layout()
{% endhighlight %}


![png]({{ "/images/output_16_0.png" | absolute_url }})


Some takeaways from above plots:
<ol>
    <li>Every column has many outliers. Those columns are completely stretched by outliers. We should first tackle these outliers before fitting any models</li>
    <li>All distributions are skewed to the right. Spike in every distribution takes more than 50% of data points.</li>
</ol>


{% highlight python %}
# Tackle the outliers.
data_frame[["name", "total_payments"]].sort_values(by=["total_payments"], ascending=False).head(5)
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th></th>
      <th>name</th>
      <th>total_payments</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>130</th>
      <td>TOTAL</td>
      <td>309886585.0</td>
    </tr>
    <tr>
      <th>79</th>
      <td>LAY KENNETH L</td>
      <td>103559793.0</td>
    </tr>
    <tr>
      <th>47</th>
      <td>FREVERT MARK A</td>
      <td>17252530.0</td>
    </tr>
    <tr>
      <th>78</th>
      <td>LAVORATO JOHN J</td>
      <td>10425757.0</td>
    </tr>
    <tr>
      <th>122</th>
      <td>SKILLING JEFFREY K</td>
      <td>8682716.0</td>
    </tr>
  </tbody>
</table>
</div>



Clearly, TOTAL should be dropped, since this row is just the summary from the document. Another data that should be dropped is Kenneth Lay, because it is well aware that Kenneth Lay, the boss of Enron, was a poi and his existence in the dataset does not tell more about the characteristic of other poi's.


{% highlight python %}
data_frame = data_frame.drop([130,79]).reset_index(drop=True)
{% endhighlight %}


{% highlight python %}
# Visualize correlation among all features.
corr_matrix = data_frame.drop(["name"], axis=1).corr()
mask = np.zeros_like(corr_matrix, dtype=np.bool) # Create a mask to wipe out upper right triangle.
mask[np.triu_indices_from(mask)] = True

sns.set(style="white"); sns.set_context("notebook")
f, ax = plt.subplots(figsize=(7, 6), dpi=120)
cmap = sns.diverging_palette(220, 10, as_cmap=True) # Create color map.
ax = sns.heatmap(corr_matrix, mask=mask, cmap=cmap, vmax=.5, center=0, square=True, linewidths=.5, vmin=-1, 
                 cbar_kws={"shrink": .5})
f.add_axes(ax)
{% endhighlight %}


![png]({{ "/images/output_21_1.png" | absolute_url }})


The correlation heatmap coincides with the data structure that email data almost always correlate with email data, and finance data almost always correlate with finance data.
<br><br>
This correlation heatmap can guide us in the feature engineering section. For example, we can create a feature, the ratio of from_this_person_to_poi over to_messages. This feature can inform us the portion of this person's emails going to poi, indicating the frequency of communication between this person and poi.

Next, let's use intuition to plot some scatter plots and examine whether some features good indicators of poi.


{% highlight python %}
sns.set(); sns.set_context("notebook")
plt.rcParams['figure.dpi'] = 90
sns.pairplot(data_frame, hue="poi", vars=["salary", "bonus", "exercised_stock_options", "long_term_incentive"], 
             palette="husl")
{% endhighlight %}




    <seaborn.axisgrid.PairGrid at 0x10e9939d0>




![png]({{ "/images/output_24_1.png" | absolute_url }})


Quitely interesting, exercised_stock_options and long_term_incentive seem like good indicators of poi. Of course we will examine importance of each feature in the modeling section.
