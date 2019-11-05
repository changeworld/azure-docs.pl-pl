---
title: Interpretowanie modeli na potrzeby lokalnego i zdalnego uruchomienia
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyjaśnić, dlaczego model wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning. Można go użyć podczas szkolenia i wnioskowania, aby zrozumieć, jak Twój model określa ważność funkcji i tworzy przewidywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515306"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretowanie modeli na potrzeby lokalnego i zdalnego uruchomienia

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyjaśnić, dlaczego model wykonał przewidywane informacje z pakietem interpretowania Azure Machine Learning Python SDK. Zapoznaj się z następującymi zadaniami:

* Interpretuj modele uczenia maszynowego, zarówno lokalnie, jak i w zdalnych zasobach obliczeniowych
* Przechowywanie lokalnych i globalnych wyjaśnień w historii uruchamiania platformy Azure
* Wyświetlanie wizualizacji interpretowania w programie [Azure Machine Learning Studio](https://ml.azure.com)
* Wdróż wyjaśnienie oceniania z modelem

Aby dowiedzieć się więcej na temat interpretacji modelu, zobacz artykuł dotyczący [koncepcji](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Lokalne interpretowanie

Poniższy przykład pokazuje, jak używać pakietu Interpretuj lokalnie bez kontaktowania się z usługami platformy Azure. Uruchom `pip install azureml-interpret`, aby uzyskać pakiet interpretacji.

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

2. Wywołaj objaśnienie lokalnie: aby zainicjować obiekt z wyjaśnieniem, musisz przekazać model i niektóre dane szkoleniowe do konstruktora programu wyjaśniającego. Opcjonalnie można również przekazać nazwy funkcji i nazwy klas wyjściowych (jeśli jest to klasyfikacja), które będą używane do wprowadzania wyjaśnień i wizualizacji więcej informacji. Poniżej przedstawiono sposób tworzenia wystąpienia obiektu wyjaśniającego za pomocą `TabularExplainer`, `MimicExplainer`i `PFIExplainer` lokalnie. `TabularExplainer` wywołuje jeden z trzech objaśnień kształtu poniżej (`TreeExplainer`, `DeepExplainer`lub `KernelExplainer`) i automatycznie wybiera najbardziej odpowiedni dla przypadku użycia. Można jednak wywoływać każdego z trzech podstawowych objaśnień bezpośrednio.

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

### <a name="overall-global-feature-importance-values"></a>Ogólne (globalne) wartości ważności funkcji

Pobierz wartości ważności funkcji globalnej.
    
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

### <a name="instance-level-local-feature-importance-values"></a>Wartości ważności funkcji na poziomie wystąpienia (lokalnego)

Pobierz wartości ważności funkcji lokalnych: Użyj następujących wywołań funkcji, aby wyjaśnić pojedyncze wystąpienie lub grupę wystąpień. Należy pamiętać, że PFIExplainer nie obsługuje lokalnych wyjaśnień.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretowanie zdalnych uruchomień

Ten przykład pokazuje, jak używać klasy `ExplanationClient` do włączania interpretowania modeli dla zdalnego uruchomienia. Koncepcja jest podobna do poprzedniej sekcji, ale w ramach zdalnego przebiegu należy użyć `ExplanationClient` w celu przekazania kontekstu interpretacji, a następnie można pobrać kontekst później w środowisku lokalnym. Użyj `pip install azureml-contrib-interpret`, aby uzyskać wymagany pakiet.

1. Utwórz skrypt szkoleniowy w lokalnym notesie Jupyter (na przykład train_explain. PR).

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

1. Postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md#amlcompute) , aby dowiedzieć się, jak skonfigurować Azure Machine Learning obliczenia jako element docelowy obliczeń i przesłać przebieg szkoleniowy. Możesz również zobaczyć [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

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

Opcjonalnie można przekazać potok transformacji funkcji do narzędzia wyjaśniającego (w train_explain. PR), aby otrzymywać wyjaśnienia w postaci nieprzetworzonych funkcji przed przekształceniem (a nie z funkcją). W przypadku pominięcia tego elementu wyjaśnienie zawiera wyjaśnienia dotyczące funkcji wbudowanych.

Format obsługiwanych transformacji jest taki sam jak opisany w [skryptu sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc, wszystkie przekształcenia są obsługiwane, o ile działają one w jednej kolumnie i dlatego są wyraźnie od siebie do wielu. 

Wyjaśnij pierwotne funkcje przy użyciu `sklearn.compose.ColumnTransformer` lub listy zamocowanych krotek transformatora. Poniższy kod używa `sklearn.compose.ColumnTransformer`. 


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

Jeśli chcesz uruchomić przykład z listą dopasowanych krotek transformatora, użyj poniższego kodu.

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
|Eksploracja danych| Przegląd zestawu danych wraz z wartościami przewidywania.|
|Ważność globalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Ten wykres jest przydatny do poznania globalnego zachowania modelu bazowego.|
|Wyjaśnienie eksploracji|Pokazuje, w jaki sposób funkcja jest odpowiedzialna za wprowadzanie zmian w wartości przewidywania modelu (lub prawdopodobieństwo wartości prognoz). Pokazano również, jak dwie funkcje współdziałają w celu wpływu na przewidywania.|
|Ważność podsumowania| Program używa podpisanych wartości ważności funkcji lokalnych dla wszystkich punktów danych, aby pokazać rozkład wpływu każdej funkcji na wartość przewidywania.|

[Pulpit nawigacyjny wizualizacji ![](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Wizualizacje lokalne

Klikaj poszczególne punkty danych w dowolnym momencie w powyższych wykresach, aby załadować wykres ważności funkcji lokalnych dla danego punktu danych.

|Wprowadź|Opis|
|----|-----------|
|Ważność lokalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Ten wykres jest przydatny do poznania lokalnego zachowania modelu bazowego w określonym punkcie danych.|
|Eksploracja perturbation|Umożliwia zmianę wartości funkcji wybranego punktu danych i przestrzeganie sposobu, w jaki te zmiany wpłyną na wartość przewidywania.|
|Oczekiwanie na pojedyncze warunkowe (lód)| Pozwala na zmianę wartości funkcji z wartości minimalnej na wartość maksymalną, aby zobaczyć, w jaki sposób Prognoza punktu danych zmienia się w przypadku zmiany funkcji.|

[Ważność funkcji lokalnej pulpitu nawigacyjnego wizualizacji ![](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[Funkcja perturbation pulpitu nawigacyjnego wizualizacji ![](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Wizualizacja — wykresy lodu](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Należy pamiętać, że przed rozpoczęciem Jupyter jądra należy mieć rozszerzenia widżetu dostępne na pulpicie nawigacyjnym wizualizacji.

* Notesy programu Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Aby załadować pulpit nawigacyjny wizualizacji, użyj poniższego kodu.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Wizualizacja w programie Azure Machine Learning Studio

Wykonując kroki opisane w sekcji możliwość [zdalnego tłumaczenia](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) , można sprawdzić pulpit nawigacyjny wizualizacji w programie [Azure Machine Learning Studio](https://ml.azure.com). Pulpit nawigacyjny wyświetlany w Azure Machine Learning Studio to prostsza wersja pulpitu nawigacyjnego wizualizacji opisana powyżej i obsługuje tylko poniższe dwie karty.

|Wprowadź|Opis|
|----|-----------|
|Ważność globalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Ten wykres jest przydatny do poznania globalnego zachowania modelu bazowego.|
|Ważność podsumowania| Program używa podpisanych wartości ważności funkcji lokalnych dla wszystkich punktów danych, aby pokazać rozkład wpływu każdej funkcji na wartość przewidywania.|

Jeśli są dostępne zarówno globalne, jak i lokalne wyjaśnienia, obie karty zostaną wypełnione danymi. Jeśli jest dostępne tylko ogólne wyjaśnienie, druga karta zostanie wyłączona.

Aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji w programie Azure Machine Learning Studio, można przejść przez jedną z następujących ścieżek:

1. Karta eksperymenty (wersja zapoznawcza): po kliknięciu karty "eksperymenty" zostanie wyświetlona lista eksperymentów uruchomionych w usłudze Azure Machine Learning. Z tej listy można wybrać konkretny eksperyment, który ma zostać przekierowany do strony ze wszystkimi uruchomieniami w ramach wybranej nazwy eksperymentu. Kliknięcie każdego przebiegu i jego karty "wyjaśnienia" spowoduje wyświetlenie pulpitu nawigacyjnego wizualizacji z wyjaśnieniem.


[Ważność funkcji lokalnej pulpitu nawigacyjnego wizualizacji ![](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Karta modele: w przypadku zarejestrowania oryginalnego modelu przy użyciu kroków z sekcji [Wdrażanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)model zostanie wyświetlony na liście kart "modele". Klikając każdy model i jego kartę "wyjaśnienia", zobaczysz pulpit nawigacyjny wizualizacji z wyjaśnieniem.

## <a name="interpretability-at-inference-time"></a>Interpretowanie w czasie wnioskowania

Program objaśniający można wdrożyć wraz z oryginalnym modelem i może być używany w czasie wnioskowania w celu udostępnienia informacji o lokalnym wyjaśnieniu. Oferujemy także jaśniejsze oceny oceniania wagi, aby poprawić wydajność interpretacji w czasie wnioskowania. Proces wdrażania bardziej jaśniejszej oceny oceniania jest podobny do wdrożenia modelu i obejmuje następujące kroki:


1. Utwórz obiekt wyjaśnień (np. przy użyciu TabularExplainer):

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz wyjaśnienie oceniania przy użyciu obiektu wyjaśnienia:

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

1. Obowiązkowe Pobierz wyjaśnienie oceniania z chmury i przetestowanie wyjaśnień

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

1. Wdróż obraz w obiekcie docelowym obliczeń:

   1. Utwórz plik oceniania (przed wykonaniem tego kroku postępuj zgodnie z instrukcjami w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) , aby zarejestrować oryginalny model predykcyjny)

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

   1. Zdefiniuj konfigurację wdrożenia (Ta konfiguracja zależy od wymagań modelu. Poniższy przykład definiuje konfigurację, która używa jednego rdzenia procesora CPU i 1 GB pamięci)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Utwórz plik ze zależnościami środowiska

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

   1. Utwórz niestandardową pliku dockerfile z zainstalowanym programem g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Wdróż utworzony obraz (szacowany czas: 5 minut)

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

1. Testowanie wdrożenia

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

1. Wyczyść: Aby usunąć wdrożoną usługę sieci Web, użyj `service.delete()`.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat interpretacji modelu, zobacz artykuł dotyczący [pojęć](how-to-machine-learning-interpretability.md).
