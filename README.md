SkLearn2PMML
============

Python library for converting [Scikit-Learn](http://scikit-learn.org/) models to PMML.

# Features #

This library is a thin wrapper around the [JPMML-SkLearn](https://github.com/jpmml/jpmml-sklearn) command-line application. For a list of supported Scikit-Learn Estimator and Transformer types, please refer to the documentation of the JPMML-SkLearn project.

# Prerequisites #

* Python 2.7, 3.4 or newer.
* Java 1.7 or newer. The Java executable must be available on system path.

# Installation #

Installing the latest version from GitHub:

```
pip install --user git+https://github.com/jpmml/sklearn2pmml.git
```

# Usage #

A typical workflow can be summarized as follows:

1. Create a `sklearn2pmml.PMMLPipeline` object, and populate it with pipeline steps as usual. Class `sklearn2pmml.PMMLPipeline` extends class `sklearn.pipeline.Pipeline` with the following functionality:
  * If the `Pipeline.fit(X, y)` method is invoked with `pandas.DataFrame` or `pandas.Series` object as an `X` argument, then its column names are used as feature names. Otherwise, feature names default to "x1", "x2", .., "x{number_of_features}".
  * If the `Pipeline.fit(X, y)` method is invoked with `pandas.Series` object as an `y` argument, then its name is used as the target name (for supervised models). Otherwise, the target name defaults to "y".
2. Fit and validate the pipeline as usual.
3. Convert the fitted `sklearn2pmml.PMMLPipeline` object to PMML document by invoking utility method `sklearn2pmml.sklearn2pmml(pipeline, pmml_destination_path)`.

Developing a simple decision tree model for the classification of iris species:

```python
import pandas

iris_df = pandas.read_csv("Iris.csv")

from sklearn2pmml import PMMLPipeline
from sklearn.tree import DecisionTreeClassifier

iris_pipeline = PMMLPipeline([
	("classifier", DecisionTreeClassifier())
])
iris_pipeline.fit(iris_df[iris_df.columns.difference(["Species"])], iris_df["Species"])

from sklearn2pmml import sklearn2pmml

sklearn2pmml(iris_pipeline, "DecisionTreeIris.pmml", with_repr = True)
```

Developing a more elaborate logistic regression model for the same:

```python
import pandas

iris_df = pandas.read_csv("Iris.csv")

from sklearn2pmml import PMMLPipeline
from sklearn2pmml.decoration import ContinuousDomain
from sklearn_pandas import DataFrameMapper
from sklearn.decomposition import PCA
from sklearn.feature_selection import SelectKBest
from sklearn.preprocessing import Imputer
from sklearn.linear_model import LogisticRegression

iris_pipeline = PMMLPipeline([
	("mapper", DataFrameMapper([
		(["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"], [ContinuousDomain(), Imputer()])
	])),
	("pca", PCA(n_components = 3)),
	("selector", SelectKBest(k = 2)),
	("classifier", LogisticRegression())
])
iris_pipeline.fit(iris_df, iris_df["Species"])

from sklearn2pmml import sklearn2pmml

sklearn2pmml(iris_pipeline, "LogisticRegressionIris.pmml", with_repr = True)
```

# De-installation #

Uninstalling:

```
pip uninstall sklearn2pmml
```

# License #

SkLearn2PMML is licensed under the [GNU Affero General Public License (AGPL) version 3.0](http://www.gnu.org/licenses/agpl-3.0.html). Other licenses are available on request.

# Additional information #

Please contact [info@openscoring.io](mailto:info@openscoring.io)
