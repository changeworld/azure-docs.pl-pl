---
title: Interpretowanie modeli w zautomatyzowanym uczeniu maszynowym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki twój zautomatyzowany model ML określa ważność funkcji i wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 9cacc0a1faa66e5d265f7f80830e13c54a88a68c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366274"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Interpretowanie modeli w zautomatyzowanym uczeniu maszynowym

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak włączyć funkcję interpretacji dla zautomatyzowanej uczenia maszynowego (ML) w Azure Machine Learning. Automatyczna ML pomaga zrozumieć zarówno nieprzetworzoną, jak i przetworzoną ważność funkcji. Aby można było korzystać z tłumaczenia modelu, należy ustawić `model_explainability=True` w obiekcie `AutoMLConfig`.  

W tym artykule omówiono sposób wykonywania następujących zadań:

- Przeprowadzaj interpretację podczas szkolenia dla najlepszego modelu lub modelu.
- Włącz wizualizacje, aby ułatwić Wyświetlanie wzorców danych i objaśnień.
- Implementowanie interpretacji podczas wnioskowania lub oceniania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcje interpretacji. Uruchom `pip install azureml-interpret azureml-contrib-interpret`, aby uzyskać wymagane pakiety.
- Wiedza o tworzeniu zautomatyzowanych eksperymentów ML. Aby uzyskać więcej informacji na temat korzystania z zestawu SDK Azure Machine Learning, wykonaj ten [samouczek model regresji](tutorial-auto-train-models.md) lub Zobacz, jak [skonfigurować zautomatyzowane eksperymenty ml](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretacja dla najlepszego modelu

Pobierz wyjaśnienie z `best_run`, w tym wyjaśnienia dotyczące funkcji i nieprzetworzonych funkcji.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Pobierz wagę funkcji z magazynu artefaktów

Za pomocą `ExplanationClient` można pobrać z magazynu artefaktów `best_run`przetworzonych wyjaśnień funkcji. Aby uzyskać wyjaśnienie dla nieprzetworzonych funkcji ustawionych `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretowanie w ramach szkolenia dla dowolnego modelu 

Podczas obliczania objaśnień modelu i wizualizacji są one nieograniczone do istniejącego wyjaśnienia modelu dla zautomatyzowanego modelu ML. Możesz również uzyskać wyjaśnienie dla modelu z różnymi danymi testowymi. Kroki opisane w tej sekcji przedstawiają sposób obliczania i wizualizacji ważności funkcji oraz ważności funkcji pierwotnej na podstawie danych testowych.

### <a name="retrieve-any-other-automl-model-from-training"></a>Pobierz inny model AutoML z szkoleń

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>Konfigurowanie wyjaśnień modelu

Użyj `automl_setup_model_explanations`, aby uzyskać zaprojektowane i nieprzetworzone wyjaśnienia funkcji. `fitted_model` może generować następujące elementy:

- Polecane dane z próbek przeszkolonych lub testowych
- Listy nazw funkcji zaprojektowanych i nieprzetworzonych
- Klasy odnalezione w kolumnie oznaczonej etykietą w scenariuszach klasyfikacji

`automl_explainer_setup_obj` zawiera wszystkie struktury z powyższej listy.

```python
from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Zainicjuj objaśnienie śladowe pod kątem ważności funkcji

Aby wygenerować wyjaśnienie dla modeli AutoML, użyj klasy `MimicWrapper`. Możesz zainicjować MimicWrapper z następującymi parametrami:

- Obiekt konfiguracji programu wyjaśniającego
- Twój obszar roboczy
- Model LightGBM, który działa jako surogat do `fitted_model` zautomatyzowanego modelu ML

MimicWrapper pobiera również obiekt `automl_run`, w którym zostaną przekazane wyjaśnienia nieprzetworzone i zaprojektowane.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Korzystanie z MimicExplainer na potrzeby przetwarzania i wizualizacji wagi funkcji

Można wywołać metodę `explain()` w MimicWrapper z przekształconymi próbkami testowymi, aby uzyskać ważność funkcji dla wygenerowanych funkcji. Możesz również użyć `ExplanationDashboard`, aby wyświetlić wizualizację pulpitu nawigacyjnego wartości znaczenia funkcji dla wygenerowanych funkcji przez automatyczne featurizers ML.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Korzystanie z objaśnień do obliczania i wizualizacji ważności nieprzetworzonej funkcji

Możesz ponownie wywołać metodę `explain()` w MimicWrapper przy użyciu przekształconych próbek testowych i ustawienia `get_raw=True`, aby uzyskać ważność funkcji dla nieprzetworzonych funkcji. Można również użyć `ExplanationDashboard`, aby wyświetlić wizualizację pulpitu nawigacyjnego wartości ważnych funkcji.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretowanie podczas wnioskowania

W tej sekcji dowiesz się, jak operacjonalizować zautomatyzowany model tablicy z wyjaśnieniem użytym do obliczenia wyjaśnień w poprzedniej sekcji.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zarejestruj model i wyjaśnienie oceniania

Użyj `TreeScoringExplainer`, aby utworzyć program do oceny oceniania, który obliczy nieprzetworzone i zaprojektowane wartości znaczenia funkcji w czasie wnioskowania. Należy zainicjować wyjaśnienie oceniania przy użyciu `feature_map`, które zostały wcześniej obliczone. Program do oceny ocen używa `feature_map` do zwrócenia pierwotnej ważności funkcji.

Zapisz wyjaśnienie oceniania, a następnie zarejestruj model i wyjaśnienie oceniania przy użyciu usługi Zarządzanie modelami. Uruchom następujący kod:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Tworzenie zależności Conda na potrzeby konfigurowania usługi

Następnie utwórz wymagane zależności środowiska w kontenerze dla wdrożonego modelu. Należy zwrócić uwagę, że usługa Azure-Defaults z wersją > = 1.0.45 musi być wymieniona jako zależność PIP, ponieważ zawiera funkcje wymagane do hostowania modelu jako usługi sieci Web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Wdrażanie usługi

Wdróż usługę przy użyciu pliku Conda i pliku oceniania z poprzednich kroków.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Wnioskowanie z danymi testowymi

Wnioskowanie z niektórymi danymi testowymi, aby zobaczyć przewidywaną wartość z zautomatyzowanego modelu ML. Wyświetl zaprojektowaną ważność funkcji dla wartości przewidywanej i ważności funkcji nieprzetworzonej dla przewidywanej wartości.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Wizualizowanie w celu odnajdywania wzorców danych i wyjaśnień w czasie uczenia

Możesz wizualizować wykres ważności funkcji w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com). Po zakończeniu zautomatyzowanego przebiegu ML wybierz pozycję **Wyświetl szczegóły modelu** , aby wyświetlić konkretny przebieg. Wybierz kartę **wyjaśnienia** , aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem.

[![Machine Learning architektura interpretacji](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sposobu włączania objaśnień modelu i ważności funkcji w obszarach zestawu Azure Machine Learning SDK innego niż automatyczne Uczenie maszynowe, zobacz [artykuł dotyczący koncepcji interpretacji](how-to-machine-learning-interpretability.md).
