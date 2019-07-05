---
title: Jak korzystać z istniejącego modelu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać usługi Azure Machine Learning z modeli, które zostały skonfigurowanych pod kątem spoza usługi. Zarejestruj modele utworzone poza usługi Azure Machine Learning i wdrażać je jako usługi sieci web lub moduł usługi Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453686"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Sposób korzystania z istniejącego modelu przy użyciu usługi Azure Machine Learning

Dowiedz się, jak użyć istniejącej maszyny uczenie modelu przy użyciu usługi Azure Machine Learning.

W przypadku usługi machine learning model, który został skonfigurowanych pod kątem spoza usługi Azure Machine Learning można nadal używać usługi, aby wdrożyć model jako usługę sieci web lub na urządzeniu usługi IoT Edge. 

> [!TIP]
> Ten artykuł zawiera podstawowe informacje dotyczące zarejestrowaniem i wdrożeniem istniejącego modelu. Po wdrożeniu usługi Azure Machine Learning oferuje funkcje monitorowania dla modelu. Umożliwia on również przechowywać dane wejściowe są wysyłane do wdrożenia, którego można użyć danych dryfu analizy lub szkolenia nowych wersji modelu.
>
> Aby uzyskać więcej informacji na temat pojęć i terminów używanych w tym miejscu, zobacz [zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).
>
> Aby uzyskać ogólne informacje na temat procesu wdrażania, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy](setup-create-workspace.md).

    > [!TIP]
    > Przykłady języka Python, w tym artykule przyjęto założenie, że `ws` zmienna jest ustawiona do swojego obszaru roboczego usługi Azure Machine Learning.
    >
    > Przykłady interfejsu wiersza polecenia, użyj symbolu zastępczego z `myworkspace` i `myresourcegroup`. Zastąp te metody testami nazwę obszaru roboczego i grupę zasobów, która go zawiera.

* Aplikację Azure Machine Learning zestawu SDK. Aby uzyskać więcej informacji, zobacz sekcję zestawu SDK języka Python [Utwórz obszar roboczy](setup-create-workspace.md#sdk).

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i [rozszerzenie interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md).

* Uczonego modelu. Model musi utrwalony na jeden lub więcej plików w środowisku deweloperskim.

    > [!NOTE]
    > Aby zademonstrować, rejestrowanie modelu skonfigurowanych pod kątem spoza usługi Azure Machine Learning, przykładowych fragmentów kodu w tym artykule, użyj modeli utworzonych przez projektu analizy tonacji Twitter Paolo Ripamonti: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Zarejestruj modele

Rejestrowanie modelu pozwala na przechowywanie wersji i śledzenie metadane dotyczące modele w obszarze roboczym. W poniższych przykładach języka Python i interfejsu wiersza polecenia `models` katalog zawiera `model.h5`, `model.w2v`, `encoder.pkl`, i `tokenizer.pkl` plików. W tym przykładzie przekazywany pliki znajdujące się w `models` katalogu jako nowej rejestracji modelu o nazwie `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Aby uzyskać więcej informacji, zobacz [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) odwołania.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Aby uzyskać więcej informacji, zobacz [rejestru modelu uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) odwołania.


Aby uzyskać więcej informacji na temat rejestracji modelu ogólnie rzecz biorąc, zobacz [zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania definiuje środowiska używanego do uruchamiania wdrożony model. Konfiguracja wnioskowania odwołuje się do następujących plików, które są używane do uruchamiania modelu, gdy aplikacja jest wdrożona:

* Środowisko uruchomieniowe. Jedyna prawidłowa wartość dla środowiska uruchomieniowego jest obecnie środowiska Python.
* Skrypt wejścia. Ten plik (o nazwie `score.py`) ładuje modelu, po uruchomieniu wdrożonej usługi. Jest również odpowiedzialny za odbierania danych, przekazując je do modelu i następnie zwróceniem odpowiedzi.
* Plik środowiska conda. Ten plik definiuje pakiety języka Python, potrzebne do uruchomienia skryptu modelu i zapis. 

Poniższy kod przedstawia podstawowe wnioskowania konfigurację przy użyciu zestawu SDK języka Python:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Aby uzyskać więcej informacji, zobacz [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) odwołania.

Interfejs wiersza polecenia ładuje konfiguracji wnioskowania z pliku YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Skrypt wejścia

Skrypt wpis ma tylko dwie wymaganych funkcji, `init()` i `run(data)`. Te funkcje są używane do inicjowania usługi podczas uruchamiania i uruchomić model przy użyciu żądania danych przekazanego przez klienta. Resztę skryptu obsługuje ładowanie i uruchamianie modeli.

> [!IMPORTANT]
> Nie ma skrypt rodzajowy wpis, który działa w przypadku wszystkich modeli. Zawsze jest charakterystyczne dla modelu, który jest używany. Należy zrozumieć, jak załadować model, formatu danych, która oczekuje modelu oraz sposób oceniać dane przy użyciu modelu.

Poniższy kod języka Python jest skrypt przykładowy wpis (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Aby uzyskać więcej informacji na temat skryptów wpis zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Środowiska Conda

Poniższego kodu YAML opisano środowiska conda potrzebnych do uruchomienia skryptu modelu i wpis:

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
```

Aby uzyskać więcej informacji, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiowanie wdrożeń

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) pakiet zawiera klasy używane w ramach wdrożenia. Klasy, których używasz Określa, gdzie model jest wdrożony. Na przykład, aby wdrożyć jako usługę sieci web w usłudze Azure Kubernetes Service, należy użyć [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) umożliwia utworzenie konfiguracji wdrożenia.

Poniższy kod Python definiuje konfigurację wdrożenia dla wdrożenia lokalnego. Ta konfiguracja wdraża model jako usługę sieci web na komputerze lokalnym.

> [!IMPORTANT]
> Lokalne wdrożenie wymaga działająca instalacja [Docker](https://www.docker.com/) na komputerze lokalnym:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Aby uzyskać więcej informacji, zobacz [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) odwołania.

Interfejs wiersza polecenia powoduje załadowanie konfiguracji wdrożenia z pliku YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Wdrażanie do innego celu obliczeń, takich jak usługi Azure Kubernetes Service w chmurze platformy Azure jest łatwe jak zmiana konfiguracji wdrożenia. Aby uzyskać więcej informacji, zobacz [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Poniższy przykład załaduje informacji na temat zarejestrowanego modelu o nazwie `sentiment`, a następnie wdraża jako usługa o nazwie `sentiment`. Podczas wdrażania wnioskowanie o konfiguracji i konfiguracji wdrożenia są używane do utworzyć i skonfigurować środowisko usługi:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Aby uzyskać więcej informacji, zobacz [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) odwołania.

Aby wdrożyć model z interfejsu wiersza polecenia, należy użyć następującego polecenia. To polecenie wdraża zarejestrowanego modelu w wersji 1 (`sentiment:1`) przy użyciu wnioskowania i wdrażanie konfiguracji przechowywanej w `inferenceConfig.json` i `deploymentConfig.json` plików:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Aby uzyskać więcej informacji, zobacz [wdrożyć model uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) odwołania.

Aby uzyskać więcej informacji na temat wdrażania, zobacz [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Użycie odpowiedź na żądanie

Po wdrożeniu oceniania identyfikatora URI jest wyświetlany. Ten identyfikator URI może służyć przez klientów do przesyłania żądań do usługi. Poniższy przykład to podstawowy klienta języka Python, który przesyła dane do usługi i wyświetla odpowiedzi:

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

Aby uzyskać więcej informacji na temat sposobu korzystania z wdrożonej usługi, zobacz [utworzyć klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Kolejne kroki

* [Monitoruj swoje modele usługi Azure Machine Learning z usługą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Jak i gdzie umożliwia wdrażanie modeli](how-to-deploy-and-where.md)
* [Jak można utworzyć klienta we wdrożonym modelu](how-to-consume-web-service.md)