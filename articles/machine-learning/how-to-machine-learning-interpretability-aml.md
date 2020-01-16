---
title: Interpretowanie modeli na potrzeby lokalnego i zdalnego uruchomienia
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki model uczenia maszynowego określa ważność funkcji i wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 19b7fbe5541bda5e6e2c265681e292f452cd57c0
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044269"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretowanie modeli na potrzeby lokalnego i zdalnego uruchomienia

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano sposób użycia pakietu interpretera Azure Machine Learning Python SDK, aby zrozumieć, dlaczego model wykonał przewidywania. Omawiane kwestie:

* Interpretuj modele uczenia maszynowego, zarówno lokalnie, jak i na zdalnych zasobach obliczeniowych.
* Przechowywanie lokalnych i globalnych wyjaśnień w historii uruchamiania platformy Azure.
* Wyświetlanie wizualizacji interpretacji w programie [Azure Machine Learning Studio](https://ml.azure.com).
* Wdróż wyjaśnienie oceniania z modelem.

Aby uzyskać więcej informacji, zobacz [interpretowanie modeli w Azure Machine Learning](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Lokalne interpretowanie

Poniższy przykład pokazuje, jak używać pakietu do interpretowania lokalnego bez kontaktowania się z usługami platformy Azure.

1. W razie potrzeby użyj `pip install azureml-interpret`, aby uzyskać pakiet interpretacji.

1. Uczenie przykładowego modelu w lokalnym notesie Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Wywołaj objaśnienie lokalnie.
   * Aby zainicjować obiekt objaśniający, Przekaż model i niektóre dane szkoleniowe do konstruktora programu wyjaśniającego.
   * Aby ułatwić wyjaśnienie i wizualizacje więcej informacji, możesz wybrać opcję przekazywania nazw funkcji i nazw klas wyjściowych w przypadku klasyfikacji.

   Poniższe bloki kodu pokazują, jak utworzyć wystąpienie obiektu wyjaśniającego z `TabularExplainer`, `MimicExplainer`i `PFIExplainer` lokalnie.
   * `TabularExplainer` wywołuje jeden z trzech objaśnień kształtu poniżej (`TreeExplainer`, `DeepExplainer`lub `KernelExplainer`).
   * `TabularExplainer` automatycznie wybiera najbardziej odpowiedni dla przypadku użycia, ale można wywołać każdego z trzech podstawowych objaśnień bezpośrednio.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    lub

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    lub

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Ogólne, wartości ważności funkcji globalnych

Zapoznaj się z poniższym przykładem, aby uzyskać informacje na temat wartości ważności funkcji globalnych.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Wartości ważności funkcji lokalnych na poziomie wystąpienia

Uzyskaj wartości ważności funkcji lokalnych przez wywołanie wyjaśnień dla pojedynczego wystąpienia lub grupy wystąpień.
> [!NOTE]
> `PFIExplainer` nie obsługuje lokalnych wyjaśnień.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretowanie zdalnych uruchomień

Poniższy przykład pokazuje, jak można użyć klasy `ExplanationClient`, aby umożliwić interpretowanie modeli dla zdalnego uruchomienia. Jest on koncepcyjnie podobny do procesu lokalnego, z tą różnicą, że:

* Użyj `ExplanationClient` w zdalnym przebiegu do przekazania kontekstu interpretacji.
* Pobierz kontekst później w środowisku lokalnym.

1. W razie potrzeby użyj `pip install azureml-contrib-interpret`, aby uzyskać wymagany pakiet.

1. Utwórz skrypt szkoleniowy w lokalnego notesu programu Jupyter. Na przykład `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Skonfiguruj Azure Machine Learning obliczenia jako element docelowy obliczeń i prześlij swój przebieg szkoleniowy. Instrukcje można znaleźć w temacie [Konfigurowanie obiektów docelowych obliczeń dla szkolenia modeli](how-to-set-up-training-targets.md#amlcompute) . Przydatne może być również znalezienie [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

1. Pobierz wyjaśnienie w lokalnym notesie Jupyter.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Nieprzetworzone przekształcenia funkcji

Możesz zdecydować się na uzyskanie wyjaśnień pod względem nieprzetworzonych, nieprzekształconych funkcji zamiast wbudowanych funkcji. W przypadku tej opcji przekazanie potoku przekształcenia funkcji do programu wyjaśniającego w `train_explain.py`. W przeciwnym razie wyjaśnienie zawiera wyjaśnienia dotyczące funkcji.

Format obsługiwanych transformacji jest taki sam, jak opisano w [skryptu sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc, wszystkie przekształcenia są obsługiwane tak długo, jak działają w pojedynczej kolumnie, dzięki czemu są one jasne.

Zapoznaj się z opisem funkcji nieprzetworzonych za pomocą `sklearn.compose.ColumnTransformer` lub z listą dopasowanych krotek transformatora. Poniższy przykład używa `sklearn.compose.ColumnTransformer`.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Jeśli chcesz uruchomić przykład z listą dopasowanych krotek transformatora, użyj następującego kodu:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Wizualizacje

Po pobraniu wyjaśnień w lokalnym notesie Jupyter można użyć pulpitu nawigacyjnego wizualizacji, aby zrozumieć i zinterpretować model.

### <a name="global-visualizations"></a>Wizualizacje globalne

Poniższe wykresy przedstawiają globalny widok modelu przeszkolonego wraz z jego przewidywaniami i objaśnieniami.

|Wprowadź|Opis|
|----|-----------|
|Eksploracja danych| Wyświetla przegląd zestawu danych wraz z wartościami przewidywania.|
|Ważność globalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Pomaga zrozumieć globalne zachowanie modelu źródłowego.|
|Wyjaśnienie eksploracji|Pokazuje, w jaki sposób funkcja wpływa na zmianę wartości przewidywania modelu lub prawdopodobieństwo wartości prognoz. Pokazuje wpływ interakcji z funkcją.|
|Ważność podsumowania|Używa lokalnych wartości znaczenia funkcji dla wszystkich punktów danych, aby pokazać rozkład wpływu każdej funkcji na wartość przewidywania.|

[Pulpit nawigacyjny wizualizacji ![](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Wizualizacje lokalne

Możesz załadować lokalny punkt ważności funkcji dla dowolnego punktu danych, wybierając poszczególne punkty danych na wykresów.

|Wprowadź|Opis|
|----|-----------|
|Ważność lokalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Pomaga zilustrować lokalne zachowanie modelu bazowego w określonym punkcie danych.|
|Eksploracja perturbation|Umożliwia zmianę wartości funkcji wybranego punktu danych i obserwowanie wyników zmian wartości prognozowanych.|
|Oczekiwanie na pojedyncze warunkowe (lód)| Zezwala na zmianę wartości funkcji z minimalnej na wartość maksymalną. Program ułatwia zilustrowanie zmiany przewidywania punktu danych po zmianie funkcji.|

[Ważność funkcji lokalnej pulpitu nawigacyjnego wizualizacji ![](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[Funkcja perturbation pulpitu nawigacyjnego wizualizacji ![](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Wizualizacja — wykresy lodu](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Przed rozpoczęciem jądra Jupyter upewnij się, że włączono rozszerzenia widżetu dla pulpitu nawigacyjnego Wizualizacja.

* Notesy programu Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Aby załadować pulpit nawigacyjny wizualizacji, użyj poniższego kodu.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Wizualizacja w programie Azure Machine Learning Studio

Po wykonaniu kroków [zdalnego tłumaczenia](#interpretability-for-remote-runs) można wyświetlić pulpit nawigacyjny wizualizacji w programie [Azure Machine Learning Studio](https://ml.azure.com). Ten pulpit nawigacyjny to prostsza wersja pulpitu nawigacyjnego wizualizacji opisana powyżej. Obsługuje tylko dwie karty:

|Wprowadź|Opis|
|----|-----------|
|Ważność globalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Pomaga zrozumieć globalne zachowanie modelu źródłowego.|
|Ważność podsumowania|Używa lokalnych wartości znaczenia funkcji dla wszystkich punktów danych, aby pokazać rozkład wpływu każdej funkcji na wartość przewidywania.|

Jeśli są dostępne zarówno globalne, jak i lokalne wyjaśnienia, dane wypełniają obie karty. Jeśli jest dostępne tylko ogólne wyjaśnienie, karta ważność podsumowania jest wyłączona.

Aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji w programie Azure Machine Learning Studio, wykonaj jedną z tych ścieżek:

* Okienko **eksperymenty** (wersja zapoznawcza)
  1. Wybierz pozycję **eksperymenty** w okienku po lewej stronie, aby wyświetlić listę eksperymentów, które zostały uruchomione na Azure Machine Learning.
  1. Wybierz konkretny eksperyment, aby wyświetlić wszystkie uruchomienia w tym eksperymentie.
  1. Wybierz przebieg, a następnie kartę **wyjaśnienia** do pulpitu nawigacyjnego wizualizacji z wyjaśnieniem.

   [Ważność funkcji lokalnej pulpitu nawigacyjnego wizualizacji ![](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* Okienko **modele**
  1. Jeśli Twój oryginalny model został zarejestrowany przez wykonanie kroków opisanych w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where), możesz wybrać **modele** w lewym okienku, aby je wyświetlić.
  1. Wybierz model, a następnie kartę **wyjaśnienia** , aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem.

## <a name="interpretability-at-inference-time"></a>Interpretowanie w czasie wnioskowania

Można wdrożyć wyjaśnienie wraz z oryginalnym modelem i użyć go w czasie wnioskowania, aby podać informacje dotyczące lokalnego wyjaśnienia. Oferujemy także jaśniejsze oceny oceniania wagi w celu zwiększenia wydajności interpretacji w czasie wnioskowania. Proces wdrażania bardziej jaśniejszej oceny oceniania jest podobny do wdrożenia modelu i obejmuje następujące kroki:

1. Utwórz obiekt wyjaśnień. Na przykład można użyć `TabularExplainer`:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz wyjaśnienie oceniania przy użyciu obiektu wyjaśnienie.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Skonfiguruj i zarejestruj obraz, który używa modelu oceny oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Opcjonalnym krokiem jest pobranie objaśnień oceniania z chmury i przetestowanie wyjaśnień.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Wdróż obraz w obiekcie docelowym obliczeń, wykonując następujące czynności:

   1. W razie konieczności Zarejestruj swój oryginalny model predykcyjny, wykonując kroki opisane w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

   1. Utwórz plik oceniania.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Definiowanie konfiguracji wdrożenia.

         Ta konfiguracja zależy od wymagań modelu. W poniższym przykładzie zdefiniowano konfigurację, która używa jednego rdzenia procesora i jednej GB pamięci.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Utwórz plik ze zależnościami środowiska.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Utwórz niestandardowy pliku dockerfile z zainstalowanym programem g + +.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Wdróż utworzony obraz.
   
         Ten proces trwa około pięciu minut.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Przetestuj wdrożenie.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Wyczyść.

   Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o interpretacji modelu](how-to-machine-learning-interpretability.md)