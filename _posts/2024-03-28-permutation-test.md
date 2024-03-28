---
layout: post
title: Permutation Test
subtitle: Intoduction to permutation tests
author: Jake
categories: Stats
banner:
  image: ../assets/images/banners/permutation_test.png
  opacity: 0.8
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: Stats Permutation
sidebar: []
---

# Unlocking the Secrets of Permutation Tests with Python

## Introduction: Navigating the Landscape of Statistical Inference

In the vast domain of data analysis, the ability to discern truth from conjecture stands as a pillar of scientific inquiry and informed decision-making. Among the tools at our disposal, statistical tests serve as the navigational aids that guide us through the complex terrain of datasets and hypotheses. Traditional tests, such as the t-test, have paved familiar paths for us, but they come with their own set of assumptions that must be met to ensure their validity and reliability.

The t-test, a cornerstone of statistical testing, assumes that the data are normally distributed within each group being compared. This assumption of normality is crucial, especially for small sample sizes, where deviation from normality can significantly affect the test's accuracy. Additionally, the t-test assumes homogeneity of variances, meaning the variance among the groups should be approximately equal. When these assumptions are not met, the t-test's ability to provide reliable results is compromised, leading researchers to seek alternative methods.

Enter the realm of permutation tests, a method that transcends these traditional pathways by offering a flexible and assumption-light approach to hypothesis testing. Unlike the t-test, permutation tests do not require the data to follow a specific distribution, nor do they assume homogeneity of variances. Instead, they embrace the empirical distribution of the data, allowing us to draw conclusions directly from the information at hand, making permutation tests particularly valuable in situations where the t-test's assumptions cannot be satisfied.

However, this flexibility comes with its own considerations. One potential drawback of permutation tests is their computational intensity, especially with large datasets. The process involves repeatedly recalculating test statistics under numerous permutations, which can be resource-intensive and time-consuming as the size of the data grows. Moreover, the permutation test's reliance on the empirical distribution means its precision and power may vary depending on the sample size and the specific characteristics of the data.

This exploration is not merely academic; it is a practical voyage into applying permutation tests using Python, the lingua franca of data science. Our journey will demystify the process of conducting these tests, transforming theoretical knowledge into a tangible skillset. Through practical examples, we will illuminate the permutation test’s utility, demonstrating how it can be applied to real-world scenarios to derive meaningful insights, while also being mindful of its limitations.

As we embark on this exploration, our goal is twofold: to deepen our understanding of the mathematical foundations that underpin permutation tests and to harness the power of Python to apply these concepts in a variety of contexts. By the end of our journey, you will not only grasp the essence of permutation testing but also be equipped to employ this method in your own analyses, enhancing your ability to make data-driven decisions with confidence and an awareness of the method's practical boundaries.

## What is a Permutation Test?

Let's embark on a thought experiment. You’re in a cafe, sipping your morning beverage, observing the world around you. On one side, there's a group savoring coffee; on the other, tea enthusiasts are enjoying their brew. A question strikes you: Does the choice between coffee and tea affect how quickly people can respond to a sudden event? You stand up and scream "HELP!!!" and with the help of a trusty high speed camera you of course have laying around time how long it takes everyone to react.

Here's where the permutation test steps into the spotlight. It's a statistical method that doesn't just leap to conclusions; it probes deeper, testing our observed reality against the realm of chance. Imagine taking the labels 'coffee drinker' and 'tea drinker' and shuffling them like a deck of cards, redistributing them randomly to the reaction times recorded. Now, if the average reaction times between these randomly assigned groups consistently show differences as extreme (or more so) than the original groups, you’d start to question if the original observation was just a fluke.

### The Fun Part: Practical Application with Python
To bring this concept out of the cafe and into the realm of data science, we turn to Python. It's like our digital laboratory where we can test our hypotheses with the precision of code.

Let's roll up our sleeves and get coding:

We'll start by creating two arrays: one for the reaction times of coffee drinkers and one for tea drinkers.
Then, we'll perform our permutation by randomly shuffling these labels many times, say 10,000, each time calculating the difference in average reaction times.
By plotting these differences, we'll create a distribution—a map of all possible outcomes under the null hypothesis, which assumes there's no real difference between coffee and tea drinkers' reaction times.
This histogram becomes our landscape, the terrain we’ll navigate to determine if our observed difference is a mountain or a mere molehill.

### Engaging with You, the Reader
Now, before revealing how this story unfolds, let's turn the page over to you. How would you approach writing this Python script? What would your histogram tell you about the world of coffee and tea drinkers?

After the code walkthrough, we’ll ask you to ponder variations: what if we changed the metric from reaction time to something else—like productivity levels or even mood scores? What insights will you uncover when you shuffle the labels in your data?

A Narrative of Numbers
Our journey through the permutation test is more than a trek through numbers; it's a narrative that starts with a real-world question and leads us through the valleys and peaks of data analysis. By the end of our post, the permutation test won't just be a statistical term—it'll be a story of exploration, where you discovered not just the power of Python, but the excitement of unearthing truths hidden in your data.

# Dataset Introduction: The Morning Brew Challenge
In our exploration, we introduce two groups: Team Coffee and Team Tea. Each team's members start their day with their drink of choice and today their reaction time will be put to the test. Can the choice of drink lead to a statistically significant difference in how quickly they can react to the unexpected?

## Creating simulated data
In the vast universe of data analysis, sometimes the best way to understand complex concepts is by constructing our own universes—simulated data where we control the laws of mathematics and chance. Let's walk through the Python code that allows us to simulate such a universe, specifically for our coffee and tea drinkers' reaction times. First I will post the full code and then I will walk you through it chunck by chunk.

```python
import numpy as np
import matplotlib.pyplot as plt


# Function to generate reaction times based on distribution type
def generate_reaction_times_by_distribution(n, params):
    """
    Generate reaction times based on the specified distribution type and parameters. The current available distribution
    types are normal, chi-square, and gamma

    :param n: Number of samples
    :param params: Dictionary with parameters for the distribution
    :return: Numpy array of reaction times

    Distribution Params Examples:
        normal_params = {
            'type': 'normal', \n
            'mean': 1.0,  # Mean of the normal distribution \n
            'std': 0.2    # Standard deviation of the normal distribution
        }

        chi_square_params = {
            'type': 'chi-square', \n
            'df': 2  # Degrees of freedom for the chi-square distribution
        }

        gamma_params = {
            'type': 'gamma', \n
            'shape': 2.0,  # Shape parameter (k) of the gamma distribution \n
            'scale': 0.5   # Scale parameter (theta) of the gamma distribution
        }
    """
    dist_type = params['type']
    if dist_type == 'normal':
        return np.random.normal(params['mean'], params['std'], n)
    elif dist_type == 'chi-square':
        return np.random.chisquare(params['df'], n)
    elif dist_type == 'gamma':
        return np.random.gamma(params['shape'], params['scale'], n)
    else:
        raise ValueError(f"Unsupported distribution type: {dist_type}")


# Updated function to use the distribution type check function
def generate_diverse_reaction_times(n_coffee, n_tea, coffee_params, tea_params, seed=None):
    """
    Generate reaction times for coffee and tea drinkers from different distributions.

    :param n_coffee: Number of coffee drinkers
    :param n_tea: Number of tea drinkers
    :param coffee_params: Dictionary with parameters for the distribution for coffee drinkers
    :param tea_params: Dictionary with parameters for the distribution for tea drinkers
    :param seed: Random seed for reproducibility
    :return: Numpy arrays of reaction times for coffee and tea drinkers
    """
    if seed is not None:
        np.random.seed(seed)

    coffee_times = generate_reaction_times_by_distribution(n_coffee, coffee_params)
    tea_times = generate_reaction_times_by_distribution(n_tea, tea_params)

    return coffee_times, tea_times


# Set the parameters
# Define parameters for the coffee and tea drinkers' reaction times
coffee_params = {
    'type': 'gamma', 'shape': 2.0, 'scale': 0.5  # Parameters for gamma distribution
}
tea_params = {
    'type': 'chi-square', 'df': 2  # Parameters for chi-square distribution
}
# Set the number of coffee and tea drinkers
n_coffee_drinkers = 1000
n_tea_drinkers = 1000

# Generate the data
coffee_times, tea_times = generate_diverse_reaction_times(n_coffee_drinkers, n_tea_drinkers,
                                                          coffee_params, tea_params, seed=42)

# Visualize the data
plt.figure(figsize=(10, 5))
plt.hist(coffee_times, bins=30, alpha=0.7, color='sandybrown', label='Coffee Drinkers (Gamma Dist.)')
plt.hist(tea_times, bins=30, alpha=0.7, color='seagreen', label='Tea Drinkers (Chi-square Dist.)')
plt.title('Reaction Times: Coffee vs. Tea Drinkers')
plt.xlabel('Reaction Time (seconds)')
plt.ylabel('Frequency')
plt.legend()
plt.show()
```

### Laying the Foundation
Our first step is to import the libraries that are the building blocks of our simulation:

```python
import numpy as np
import matplotlib.pyplot as plt

```

numpy gives us the power to perform complex mathematical operations with ease, while matplotlib allows us to visualize our data, bringing numbers to life through graphs and charts.



## Visuals and Validation
As we journey through this dataset together, we'll employ diagrams to demystify the permutation test, animations to articulate the shuffling process, and graphs that ground theory in the tangible. 

