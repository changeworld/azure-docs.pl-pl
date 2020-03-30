---
title: Używanie i wdrażanie istniejących modeli
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z usługi Azure Machine Learning z modelami, które zostały przeszkolone poza usługą. Można zarejestrować modele utworzone poza usługą Azure Machine Learning, a następnie wdrożyć je jako usługę sieci web lub moduł usługi Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472379"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Używanie istniejącego modelu za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak korzystać z istniejącego modelu uczenia maszynowego za pomocą usługi Azure Machine Learning.

Jeśli masz model uczenia maszynowego, który został przeszkolony poza usługą Azure Machine Learning, nadal można użyć usługi do wdrożenia modelu jako usługi sieci web lub urządzenia usługi IoT Edge. 

> [!TIP]
> Ten artykuł zawiera podstawowe informacje na temat rejestrowania i wdrażania istniejącego modelu. Po wdrożeniu usługa Azure Machine Learning zapewnia monitorowanie modelu. Umożliwia również przechowywanie danych wejściowych wysyłanych do wdrożenia, które mogą być używane do analizy dryfu danych lub szkolenia nowych wersji modelu.
>
> Aby uzyskać więcej informacji na temat pojęć i terminów używanych w tym miejscu, zobacz [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego.](concept-model-management-and-deployment.md)
>
> Aby uzyskać ogólne informacje na temat procesu wdrażania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego](how-to-manage-workspace.md).

    > [!TIP]
    > Przykłady języka Python w tym `ws` artykule założono, że zmienna jest ustawiona na obszar roboczy usługi Azure Machine Learning.
    >
    > Przykłady interfejsu wiersza polecenia `myworkspace` `myresourcegroup`używają symbolu zastępczego i . Zastąp je nazwą obszaru roboczego i grupy zasobów, która go zawiera.

* Zestaw [SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia usługi Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i uczenia [maszynowego](reference-azure-machine-learning-cli.md).

* Wyszkolony model. Model musi być utrwalony do jednego lub więcej plików w środowisku programistycznym.

    > [!NOTE]
    > Aby zademonstrować rejestrowanie modelu przeszkolonego poza usługą Azure Machine Learning, przykładowe fragmenty kodu w tym artykule [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)używają modeli utworzonych przez projekt analizy tonacji na Twitterze Paolo Ripamonti: .

## <a name="register-the-models"></a>Zarejestruj model(y)

Rejestrowanie modelu umożliwia przechowywanie, przechowywanie wersji i śledzenie metadanych dotyczących modeli w obszarze roboczym. W poniższych przykładach języka `models` Python i `model.h5`CLI `model.w2v` `encoder.pkl`katalog `tokenizer.pkl` zawiera , , i plików. W tym przykładzie przekazuje pliki `models` zawarte w katalogu jako `sentiment`nową rejestrację modelu o nazwie:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Aby uzyskać więcej informacji, zobacz [odwołanie model.register().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Można również ustawić `tags` `properties` obiekty dodawania i słownika do zarejestrowanego modelu. Te wartości mogą być później używane do identyfikowania określonego modelu. Na przykład zastosowano ramy, parametry treningowe itp.

Aby uzyskać więcej informacji, zobacz odwołanie do [rejestru modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Aby uzyskać więcej informacji na temat rejestracji modelu w ogóle, zobacz [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania definiuje środowisko używane do uruchamiania wdrożonego modelu. Konfiguracja wnioskowania odwołuje się do następujących jednostek, które są używane do uruchamiania modelu po jego wdrożeniu:

* Skrypt wejściowy. Ten plik `score.py`(nazwany) ładuje model po uruchomieniu wdrożonej usługi. Jest również odpowiedzialny za odbieranie danych, przekazywanie ich do modelu, a następnie zwracanie odpowiedzi.
* [Środowisko](how-to-use-environments.md)usługi Azure Machine Learning . Środowisko definiuje zależności oprogramowania potrzebne do uruchomienia modelu i skryptu wejścia.

W poniższym przykładzie pokazano, jak używać sdk do tworzenia środowiska, a następnie użyć go z konfiguracją wnioskowania:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

+ [Jak korzystać ze środowisk](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) odwołanie.


Cli ładuje konfigurację wnioskowania z pliku YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

W wierszu polecenia środowisko conda `myenv.yml` jest zdefiniowane w pliku, do którego odwołuje się konfiguracja wnioskowania. Następująca zawartość pliku YAML:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Skrypt wejścia

Skrypt wpisowy ma tylko dwie `init()` `run(data)`wymagane funkcje i . Te funkcje są używane do inicjowania usługi podczas uruchamiania i uruchamiania modelu przy użyciu danych żądania przekazywanych przez klienta. Reszta skryptu obsługuje ładowanie i uruchamianie modeli.

> [!IMPORTANT]
> Nie ma ogólnego skryptu wpisu, który działa dla wszystkich modeli. Jest zawsze specyficzne dla modelu, który jest używany. Musi zrozumieć, jak załadować model, format danych, który oczekuje modelu i jak uzyskać dane przy użyciu modelu.

Poniższy kod Języka Python jest`score.py`przykładowym skryptem wpisowym ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiowanie wdrożenia

Pakiet [usługi sieci Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) zawiera klasy używane do wdrażania. Klasa, której używasz określa, gdzie model jest wdrażany. Na przykład, aby wdrożyć jako usługę sieci web w usłudze Azure Kubernetes, należy użyć [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) do utworzenia konfiguracji wdrożenia.

Poniższy kod języka Python definiuje konfigurację wdrożenia dla wdrożenia lokalnego. Ta konfiguracja wdraża model jako usługę sieci web na komputerze lokalnym.

> [!IMPORTANT]
> Wdrożenie lokalne wymaga działającej instalacji platformy [Docker](https://www.docker.com/) na komputerze lokalnym:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Aby uzyskać więcej informacji, zobacz [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) odwołania.

Cli ładuje konfigurację wdrożenia z pliku YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Wdrażanie w innym miejscu docelowym obliczeń, takich jak usługa Azure Kubernetes w chmurze platformy Azure, jest tak proste, jak zmiana konfiguracji wdrażania. Aby uzyskać więcej informacji, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Poniższy przykład ładuje informacje o `sentiment`zarejestrowanym modelu o nazwie `sentiment`, a następnie wdraża go jako usługę o nazwie . Podczas wdrażania konfiguracja wnioskowania i konfiguracja wdrażania są używane do tworzenia i konfigurowania środowiska usługi:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Aby uzyskać więcej informacji, zobacz [odwołanie model.deploy().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Aby wdrożyć model z interfejsu wiersza polecenia, należy użyć następującego polecenia. To polecenie wdraża wersję 1 zarejestrowanego modelu (`sentiment:1`) przy użyciu `inferenceConfig.json` `deploymentConfig.json` konfiguracji wnioskowania i wdrażania przechowywanych w plikach i:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Aby uzyskać więcej informacji, zobacz odwołanie do [modelu az ml deploy.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Aby uzyskać więcej informacji na temat wdrażania, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Zużycie odpowiedzi na żądanie

Po wdrożeniu zostanie wyświetlony identyfikator URI oceniania. Ten identyfikator URI może służyć przez klientów do przesyłania żądań do usługi. Poniższy przykład jest podstawowym klientem języka Python, który przesyła dane do usługi i wyświetla odpowiedź:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji na temat korzystania z wdrożonej usługi, zobacz [Tworzenie klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Następne kroki

* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)
* [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md)
* [Jak utworzyć klienta dla wdrożonego modelu](how-to-consume-web-service.md)
