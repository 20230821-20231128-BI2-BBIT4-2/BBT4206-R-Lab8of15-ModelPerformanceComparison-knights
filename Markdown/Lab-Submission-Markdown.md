Business Intelligence Project
================
<Knights>
\<6 Nov 2023\>

- [Student Details](#student-details)
- [Setup Chunk](#setup-chunk)
- [Understanding the Dataset (Exploratory Data Analysis
  (EDA))](#understanding-the-dataset-exploratory-data-analysis-eda)
  - [Loading the Dataset](#loading-the-dataset)
    - [Source:](#source)

# Student Details

|                                           |                      |
|-------------------------------------------|----------------------|
| **Student ID Number** && **Student Name** |                      |
| 134834 - Emmanuel Kasio                   | 135356 - Ann Kigera  |
| 122883 - Michelle Guya                    | 136301 - Ian Nyameta |
| 135230 - Peter Aringo                     |                      |

**BBIT 4.2 Group** \| B \|  
**BI Project Group Name/ID (if applicable)** \| Knights \|

# Setup Chunk

**Note:** the following KnitR options have been set as the global
defaults: <BR>
`knitr::opts_chunk$set(echo = TRUE, warning = FALSE, eval = TRUE, collapse = FALSE, tidy = TRUE)`.

More KnitR options are documented here
<https://bookdown.org/yihui/rmarkdown-cookbook/chunk-options.html> and
here <https://yihui.org/knitr/options/>.

# Understanding the Dataset (Exploratory Data Analysis (EDA))

## Loading the Dataset

### Source:

The dataset that was used can be downloaded here: *\<<a
href="https://github.com/selva86/datasets/blob/master/Sacramento.csv\"
class="uri">https://github.com/selva86/datasets/blob/master/Sacramento.csv\</a>\>*

``` r
# STEP 1. Install and Load the Required Packages ----
## readr ----
if (require("readr")) {
  require("readr")
} else {
  install.packages("readr", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: readr

``` r
## mlbench ----
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: mlbench

``` r
## caret ----
if (require("caret")) {
  require("caret")
} else {
  install.packages("caret", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: caret

    ## Loading required package: ggplot2

    ## Loading required package: lattice

``` r
## kernlab ----
if (require("kernlab")) {
  require("kernlab")
} else {
  install.packages("kernlab", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: kernlab

    ## 
    ## Attaching package: 'kernlab'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     alpha

``` r
## randomForest ----
if (require("randomForest")) {
  require("randomForest")
} else {
  install.packages("randomForest", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: randomForest

    ## randomForest 4.7-1.1

    ## Type rfNews() to see new features/changes/bug fixes.

    ## 
    ## Attaching package: 'randomForest'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     margin

``` r
# STEP 2. Load the Dataset ----
data(iris)
```

``` r
# STEP 3. The Resamples Function ----
## 3.a. Train the Models ----
train_control <- trainControl(method = "repeatedcv", number = 10, repeats = 3)

### LDA ----
set.seed(7)
iris_model_lda <- train(Species ~ ., data = iris,
                            method = "lda", trControl = train_control)

### CART ----
set.seed(7)
iris_model_cart <- train(Species ~ ., data = iris,
                             method = "rpart", trControl = train_control)

### KNN ----
set.seed(7)
iris_model_knn <- train(Species ~ ., data = iris,
                            method = "knn", trControl = train_control)

### SVM ----
set.seed(7)
iris_model_svm <- train(Species ~ ., data = iris,
                            method = "svmRadial", trControl = train_control)

### Random Forest ----
set.seed(7)
iris_model_rf <- train(Species ~ ., data = iris,
                           method = "rf", trControl = train_control)

## 3.b. Call the `resamples` Function ----
results <- resamples(list(LDA = iris_model_lda, CART = iris_model_cart,
                          KNN = iris_model_knn, SVM = iris_model_svm,
                          RF = iris_model_rf))
```

``` r
# STEP 4. Display the Results ----
## 1. Table Summary ----
# It creates a table with one model per row
# and its corresponding evaluation metrics displayed per column.

summary(results)
```

    ## 
    ## Call:
    ## summary.resamples(object = results)
    ## 
    ## Models: LDA, CART, KNN, SVM, RF 
    ## Number of resamples: 30 
    ## 
    ## Accuracy 
    ##           Min.   1st Qu.    Median      Mean 3rd Qu. Max. NA's
    ## LDA  0.8666667 0.9500000 1.0000000 0.9800000       1    1    0
    ## CART 0.8000000 0.9333333 0.9333333 0.9333333       1    1    0
    ## KNN  0.8666667 0.9333333 1.0000000 0.9666667       1    1    0
    ## SVM  0.8000000 0.9333333 0.9666667 0.9511111       1    1    0
    ## RF   0.8666667 0.9333333 0.9333333 0.9600000       1    1    0
    ## 
    ## Kappa 
    ##      Min. 1st Qu. Median      Mean 3rd Qu. Max. NA's
    ## LDA   0.8   0.925   1.00 0.9700000       1    1    0
    ## CART  0.7   0.900   0.90 0.9000000       1    1    0
    ## KNN   0.8   0.900   1.00 0.9500000       1    1    0
    ## SVM   0.7   0.900   0.95 0.9266667       1    1    0
    ## RF    0.8   0.900   0.90 0.9400000       1    1    0

``` r
## 2. Box and Whisker Plot ----
# To visualize the spread of the estimated accuracies
# for different algorithms and how they relate.

scales <- list(x = list(relation = "free"), y = list(relation = "free"))
bwplot(results, scales = scales)
```

![](Lab-Submission-Markdown_files/figure-gfm/Displaying%20Results-1.png)<!-- -->

``` r
## 3. Dot Plots ----
# To show both the mean estimated accuracy as well as the 95% confidence
# interval (e.g. the range in which 95% of observed scores fell).

scales <- list(x = list(relation = "free"), y = list(relation = "free"))
dotplot(results, scales = scales)
```

![](Lab-Submission-Markdown_files/figure-gfm/Displaying%20Results-2.png)<!-- -->

``` r
## 4. Scatter Plot Matrix ----
# To consider whether the predictions from two
# different algorithms are correlated.

splom(results)
```

![](Lab-Submission-Markdown_files/figure-gfm/Displaying%20Results-3.png)<!-- -->

``` r
## 5. Pairwise xyPlots ----
# Pairwise comparison of the accuracy of trial-folds for
# two models using an xyplot.

# xyplot plots to compare models
xyplot(results, models = c("LDA", "SVM"))
```

![](Lab-Submission-Markdown_files/figure-gfm/Displaying%20Results-4.png)<!-- -->

``` r
# or
# xyplot plots to compare models
xyplot(results, models = c("SVM", "CART"))
```

![](Lab-Submission-Markdown_files/figure-gfm/Displaying%20Results-5.png)<!-- -->

``` r
## 6. Statistical Significance Tests ----
# To calculate the significance of the differences between the
# metric distributions of the various models.

diffs <- diff(results)

summary(diffs)
```

    ## 
    ## Call:
    ## summary.diff.resamples(object = diffs)
    ## 
    ## p-value adjustment: bonferroni 
    ## Upper diagonal: estimates of the difference
    ## Lower diagonal: p-value for H0: difference = 0
    ## 
    ## Accuracy 
    ##      LDA      CART      KNN       SVM       RF       
    ## LDA            0.046667  0.013333  0.028889  0.020000
    ## CART 0.001444           -0.033333 -0.017778 -0.026667
    ## KNN  1.000000 0.003853             0.015556  0.006667
    ## SVM  0.097915 0.433974  0.698427            -0.008889
    ## RF   0.173724 0.014258  1.000000  1.000000           
    ## 
    ## Kappa 
    ##      LDA      CART     KNN      SVM      RF      
    ## LDA            0.07000  0.02000  0.04333  0.03000
    ## CART 0.001444          -0.05000 -0.02667 -0.04000
    ## KNN  1.000000 0.003853           0.02333  0.01000
    ## SVM  0.097915 0.433974 0.698427          -0.01333
    ## RF   0.173724 0.014258 1.000000 1.000000
