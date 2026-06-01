---
title: Design rationale
toc: true
---

# Design rationale


## Question

The question that this assignment aims to answer is: **Which cereals have the best nutrition tradeoffs, and what other factors relate to or affect the cereal rating?** The cereal dataset works with many variables such as nutrition content (things ranging from sugars, fibers to carbohydrates, and fats) and manufacturer, and the type of cereal.

## Visual encodings

The first visualization is a scatterplot that compared nutrition content (you can change x-axis to whichever one you wanted to see) with consumer ratings. By allowing users to choose their own x-axis, this allows users to test different variables to try and explain rating differences. The y-axis is always user-ratings and is unchangeable since that is the main outcome variable that we are trying to test. The different colors encodes manufcturers so that brand patterns can be displayed without having to facet the chart into smaller charts. The circle size encodes the fiber levels since fiber is an important nutritional value and is helpful to have when visualizing other nutritional values. 

The bar chart ranks the top cereals after your filter that you choose, this helps give users a clear understanding of what their top-rated cereals are after the filter, and helps them compare between the other top-rated cereals in that filter. This histogram summarizes the sugar distribution so that viewers can get a good understanding of the range of the cereals included after their filter. 


## Interaction techniques

The dashboard includes three main interaction styles:
1. **Dynamic query filters** the filters include the different manufacturers, maximum sugar, and minimum ratings. This selection allows for users to filter the dataset to only be cereals that they are interested in analyzing. 
2. **X-axis selection** On the scatterplot, you can choose the nutrition value that you want to visualize and compare. This allows for the comparison of many different visualizations without it being too crowded and cluttered. 
3. **Tooltips** Since there is way too much information if we try to show cereal names and nutrition details, to avoid clutter, I included a tooltip function where they are revealed when it is hovered.  

Alternative visualizations that I was considering was a scatterplot matrix, that I found online, but I just felt like it was too cluttered, and just too dense with information overall and would make it difficult for users to focus on what they were trying to answer. Another option I considered, was just not having the option to choose x-axises, but I feel like it was just too much to have to scroll through potentially 8 visualizations, so I just decided to give the users a choice in choosing which x-axis they perferred. 

There was also shelf-placement data included in the original dataset, but I chose to just not include it overall, since it didn't really help answer my intial intended question. Leaving shelf-placement data, just brings in other variables, that don't affect the nutritional values. 

In the end I chose the scatterplot, with the choice of filtering, and choosing different x-axises, as it is best in displaying the relationships between nutrtional values and the cereal ratings, which answers my intended questions. This was the best option as it showed a lot of information without overwhelming users, and the overall dashboard of visualizations. The supporting visualizations were also chosen just so the users had a good grasp of what cereals were within range of what they filtered, and just an easy overview of the top rated cereals in that filter. 

## References

- Dataset: `cereal.csv`, from kraggle for the cereal nutrition dataset used for this project.
https://www.kaggle.com/datasets/crawford/80-cereals?resource=download 
- I also kept in a lot of the features that came with the demo of observable framework, and just tweaked the information that would go inside the boxes, since I thought it was interesting to keep (a good summary of our data)
- I referenced the gapminder site that was showed in class, for interactive filtering and the colors for multivariate encoding. 
https://www.gapminder.org/tools/#$chart-type=bubbles&url=v2
- Lastly, I referenced the plot gallery in observable for the different visualizations that I wanted to make. 
https://observablehq.com/@observablehq/plot-gallery