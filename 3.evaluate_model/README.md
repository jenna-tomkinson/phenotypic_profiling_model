# 3. Evaluate Model

In this module, we evaluate the final and shuffled baseline ML models.

After training the models in [2.train_model](../2.train_model/), we use these models to predict the labels of the training and testing datasets and evaluate their predictive performance.
We evaluate each model for each combination of model type (final, shuffled baseline), feature type (CP, DP, CP_and_DP), balance type (balanced, unbalanced), dataset type (ic, no_ic) and dataset (train, test).
See [2.train_model/README.md](../2.train_model/README.md) for more information on model combinations.

In [get_model_predictions.ipynb](get_model_predictions.ipynb), we derive the predicted and true phenotypic class for each model, feature type, and dataset combination.
These predictions are saved in [predictions](predictions/).

In [confusion_matrices.ipynb](confusion_matrices.ipynb), we evaluate these sets of predictions with a confusion matrix to see the true/false positives and negatives (see [sklearn.metrics.confusion_matrix](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.confusion_matrix.html) for more details).
The confusion matrix data are saved to [confusion_matrices](evaluations/confusion_matrices/).

In [F1_scores.ipynb](F1_scores.ipynb), we evaluate each model (final, shuffled baseline) trained with each feature type (CP, DP, CP_and_DP) on each dataset (train, test, etc) to determine phenotypic and weighted f1 scores.
F1 score measures the models precision and recall performance for each phenotypic class (see [sklearn.metrics.f1_score](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html) for more details).
The f1 score data are saved to [F1_scores](evaluations/F1_scores/).

In [class_PR_curves.ipynb](class_PR_curves.ipynb), we use [sklearn.metrics.precision_recall_curve](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_recall_curve.html) to derive the precision-recall curves for each model, feature type, and dataset combination.
These PR curves are created for each label type of the logistic regression model.
E.g. each multi-class model has 15 labels (1 for each phenotypic class) and 15 PR curves while each single-class model has 2 labels (positive and negative label for its respective phenotype) and 2 PR curves.
The precision recall curves and their data are saved to [precision_recall_curves](evaluations/precision_recall_curves/).

In [get_LOIO_probabilities.ipynb](get_LOIO_probabilities.ipynb), we use the optimal hyperparameters from each final logistic regression model (DP, CP, CP_and_DP) to fit and evaluate new models in a Leave One Image Out (LOIO) fashion.
These optimal hyper parameters are found with Grid Search Cross Validation in [train_model.ipynb](../2.train_model/train_model.ipynb) and are saved with model data in [models/](../2.train_model/models/).
LOIO evaluation gives an idea of how well the model will perform on cells that are in an image the model has never seen before.
If the model performs well in LOIO evaluation, we can be confident it will generalize well to images it has never seen before.
Our LOIO is within the family of [leave one out cross validation](https://machinelearningmastery.com/loocv-for-evaluating-machine-learning-algorithms/).
The LOIO evaluation procedure is as follows:
1) Load in entire MitoChecks labeled cell dataset (from [labeled_data.csv.gz](../data/labeled_data.csv.gz))
2) For each image in MitoCheck labeled cell dataset (as specified by the `Metadata_DNA` field):
    - Train a logistic regression model with optimal hyperparameters (`C` and `l1_ratio`) determined for a particular model in [train_model.ipynb](../2.train_model/train_model.ipynb) on every cell that is **not** in the specific image.
    - Predict probabilities on every cell that **is** in the specific image.

These probabilities are saved to [LOIO_probas](evaluations/LOIO_probas/).

**Notes:** 
1) Intermediate `.tsv` data are stored in tidy format, a standardized data structure (see [Tidy Data](https://vita.had.co.nz/papers/tidy-data.pdf) by Hadley Wickham for more details).
2) SCM stands for "single cell model(s)" and is used as an abbrevation for the binary, sinlge-class models throughout this module.

## Step 1: Evaluate Model

Use the commands below to evaluate the ML models:

```sh
# Make sure you are located in 3.evaluate_model
cd 3.evaluate_model

# Activate phenotypic_profiling conda environment
conda activate phenotypic_profiling

# Evaluate model
bash evaluate_model.sh
```