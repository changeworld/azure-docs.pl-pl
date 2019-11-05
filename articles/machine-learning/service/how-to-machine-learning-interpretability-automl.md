---
title: Interpretowanie modeli w zautomatyzowanej ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyjaśnić, dlaczego zautomatyzowany model ML wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning. Można go użyć podczas szkolenia i wnioskowania, aby zrozumieć, jak Twój model określa ważność funkcji i tworzy przewidywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515332"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Interpretowanie modeli dla zautomatyzowanych modeli ML

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym instruktażu dowiesz się, jak włączyć funkcję interpretacji w ramach automatycznego uczenia maszynowego przy użyciu usługi Azure Machine Learning. Zautomatyzowana ML pozwala zrozumieć znaczenie funkcji nieprzetworzonych i skonstruowanych. Aby można było korzystać z tłumaczenia modelu, należy ustawić `model_explainability=True` w obiekcie `AutoMLConfig`.  

Ten artykuł zawiera informacje o następujących zadaniach:

* Interpretowanie na potrzeby najlepszego modelu lub modelu
* Umożliwienie wizualizacji, aby pomóc Ci w odnajdywaniu wzorców w danych i wyjaśnień
* Interpretowanie podczas wnioskowania lub oceniania

## <a name="prerequisites"></a>Wymagania wstępne

* Uruchom `pip install azureml-interpret azureml-contrib-interpret`, aby uzyskać niezbędne pakiety dla funkcji interpretacji.
* W tym artykule założono wiedzę o tworzeniu zautomatyzowanych eksperymentów z ML. Zobacz [samouczek](tutorial-auto-train-models.md) lub [instrukcje](how-to-configure-auto-train.md) , aby dowiedzieć się, jak używać ZAUTOMATYZOWANEJ ml w zestawie SDK.
 
## <a name="interpretability-during-training-for-the-best-model"></a>Interpretacja dla najlepszego modelu 

Pobierz wyjaśnienie z `best_run`, w tym wyjaśnienia dotyczące funkcji i nieprzetworzonych funkcji. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Pobierz wagę funkcji z magazynu artefaktów

Za pomocą `ExplanationClient` można pobrać przetworzone wyjaśnienia funkcji z magazynu artefaktów best_run. Aby uzyskać wyjaśnienie dla nieprzetworzonych funkcji ustawionych `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretowanie w ramach szkolenia dla dowolnego modelu 

W tej sekcji dowiesz się, jak obliczać objaśnienia modelu i wizualizować wyjaśnienia. Oprócz tego, aby uzyskać informacje o istniejącym modelu, należy zapoznać się z modelem, korzystając z różnych danych testowych. Poniższe kroki umożliwiają obliczanie i wizualizowanie wagi funkcji oraz ważnych funkcji na podstawie danych testowych.

### <a name="retrieve-any-other-automl-model-from-training"></a>Pobierz inny model AutoML z szkoleń

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Konfigurowanie wyjaśnień modelu

Fitted_model może generować następujące elementy, które będą używane do uzyskiwania zaprojektowanych i nieprzetworzonych wyjaśnień funkcji za pomocą automl_setup_model_explanations:

* Dane Featurized z próbek pociągów/próbek testowych
* Zbieranie list z nazwami i nieprzetworzonymi listami funkcji
* Znajdowanie klas w kolumnie zatytułowanej w scenariuszach klasyfikacji

Automl_explainer_setup_obj zawiera wszystkie struktury z powyższej listy.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Zainicjuj objaśnienie śladowe pod kątem ważności funkcji

Aby uzyskać wyjaśnienie modeli AutoML, użyj klasy `MimicWrapper`. MimicWrapper można zainicjować za pomocą parametrów dla obiektu Instalatora programu wyjaśniającego, obszaru roboczego i modelu LightGBM, który działa jako model zastępczy, aby wyjaśnić zautomatyzowany model ML (fitted_model tutaj). MimicWrapper pobiera również obiekt automl_run, w którym zostaną przekazane wyjaśnienia nieprzetworzone i zaprojektowane.

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

Metoda Wyjaśnij () w MimicWrapper może być wywoływana przy użyciu przekształconych próbek testowych w celu uzyskania ważności funkcji dla wygenerowanych funkcji. Można również użyć ExplanationDashboard, aby wyświetlić wizualizację tablicy z wartościami ważności funkcji dla wygenerowanych funkcji przez zautomatyzowane featurizers ML.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Korzystanie z objaśnień do obliczania i wizualizacji ważności nieprzetworzonej funkcji

Metodę Wyjaśnij () w MimicWrapper można ponownie wywołać przy użyciu przekształconych próbek testowych i ustawienia `get_raw` na true, aby uzyskać ważność funkcji dla nieprzetworzonych funkcji. Można również użyć ExplanationDashboard, aby wyświetlić wizualizację pulpitu nawigacyjnego wartości znaczenia funkcji nieprzetworzonych funkcji.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretowanie podczas wnioskowania

W tej sekcji dowiesz się, jak operacjonalizować zautomatyzowany model tablicy z wyjaśnieniem, który został użyty do obliczenia wyjaśnień w poprzedniej sekcji.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zarejestruj model i wyjaśnienie oceniania

Użyj `TreeScoringExplainer`, aby utworzyć program do oceny oceniania, który będzie używany do obliczania nieprzetworzonych i skonstruowanych wartości ważności funkcji w czasie wnioskowania. Należy pamiętać, że zainicjowano wyjaśnienie oceniania przy użyciu feature_map, który został wcześniej obliczony. Feature_map będzie używany przez wyjaśnienie oceniania w celu zwrócenia pierwotnej ważności funkcji.

W poniższym kodzie można zapisać wyjaśnienie oceniania i zarejestrować model i wyjaśnienie oceniania przy użyciu usługi Zarządzanie modelami.

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

Następnie należy utworzyć zależności środowiska potrzebne w kontenerze dla wdrożonego modelu.

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
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Wnioskowanie przy użyciu danych testowych

Wnioskowanie przy użyciu niektórych danych testowych, aby zobaczyć przewidywaną wartość z zautomatyzowanego modelu ML i wyświetlić skonstruowaną wagę funkcji dla wartości przewidywanej i ważności funkcji nieprzetworzonej dla przewidywanej wartości.

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

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Wizualizacje pomagające w odnajdywaniu wzorców danych i wyjaśnień w czasie uczenia

Możesz również wizualizować wykres ważności funkcji w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com). Po zakończeniu zautomatyzowanego uruchomienia sieci należy kliknąć pozycję "Wyświetl szczegóły modelu", co spowoduje przejście do określonego uruchomienia. W tym miejscu kliknij kartę "wyjaśnienia", aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem. 

[![Machine Learning architektura interpretacji](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sposobu, w jaki można włączyć wyjaśnienie modelu i znaczenie funkcji w innych obszarach zestawu SDK poza funkcją automatycznej uczenia maszynowego, zapoznaj się z artykułem [koncepcji](how-to-machine-learning-interpretability.md) w zakresie interpretacji.