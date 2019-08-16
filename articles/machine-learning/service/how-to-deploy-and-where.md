---
title: Jak i gdzie wdrażać modele
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak i gdzie wdrożyć modele usług Azure Machine Learning, w tym: Azure Container Instances, usługa Azure Kubernetes, Azure IoT Edge i tablice bram do programowania pól.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c0c3ade3fd089a4819b8836b07e249fc32c06e0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543611"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Wdrażaj modele za pomocą usługi Azure Machine Learning

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci Web w chmurze platformy Azure lub IoT Edge urządzeń.

Przepływ pracy jest podobny bez względu na [to, gdzie wdrażasz](#target) model:

1. Należy zarejestrować model.
1. Przygotuj do wdrożenia (Określ zasoby, użycie, cel obliczeń).
1. Wdróż model do obiektu docelowego obliczeń.
1. Przetestuj wdrożony model, nazywany również usługą sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli za pomocą usługi Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

- Model. Jeśli nie masz nauczonego modelu, możesz użyć modelu & plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://aka.ms/aml-sdk)lub [rozszerzenia Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Poniższy kod ilustruje sposób nawiązywania połączenia z obszarem roboczym usługi Azure Machine Learning przy użyciu informacji w pamięci podręcznej w lokalnym środowisku programistycznym:

**Korzystanie z zestawu SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Aby uzyskać więcej informacji na temat łączenia się z obszarem roboczym przy użyciu zestawu SDK, zobacz [zestaw sdk Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Korzystanie z interfejsu wiersza polecenia**

Korzystając z interfejsu wiersza polecenia, należy `-w` użyć `--workspace-name` parametru lub, aby określić obszar roboczy dla poleceń.

**Używanie VS Code**

W przypadku korzystania z VS Code obszar roboczy jest wybierany przy użyciu interfejsu graficznego. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli i zarządzanie nimi](how-to-vscode-tools.md#deploy-and-manage-models) w dokumentacji rozszerzenia vs Code.

## <a id="registermodel"></a>Zarejestruj model

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Podczas rejestrowania modelu można podać ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. nie musi być zgodna z ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [co to jest get_model_path](#what-is-get_model_path).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub mogą pochodzić z innej lokalizacji. W poniższych przykładach pokazano, jak zarejestrować model:

### <a name="register-a-model-from-an-experiment-run"></a>Rejestrowanie modelu na podstawie przebiegu eksperymentu

Fragmenty kodu w tej sekcji pokazują, jak zarejestrować model z poziomu przebiegu szkoleniowego:

> [!IMPORTANT]
> W tych fragmentach kodu przyjęto założenie, że wcześniej przeprowadzono przebieg szkolenia i `run` masz dostęp do obiektu (przykład SDK) lub wartość identyfikatora uruchomienia (przykład interfejsu wiersza polecenia). Aby uzyskać więcej informacji na temat modeli szkoleniowych, zobacz [Tworzenie i używanie obiektów docelowych obliczeń do szkolenia modeli](how-to-set-up-training-targets.md).

+ **Korzystanie z zestawu SDK**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  `model_path` Odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka do pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` dla katalogu, który zawiera pliki.

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka do pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `--asset-path` dla katalogu, który zawiera pliki.

+ **Używanie VS Code**

  Zarejestruj modele przy użyciu dowolnych plików lub folderów modelu z rozszerzeniem [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Model można zarejestrować, podając **ścieżkę lokalną** do modelu. Możesz podać folder lub pojedynczy plik. Tej metody można użyć do zarejestrowania obu modeli przeszkolonych za pomocą usługi Azure Machine Learning, a następnie pobrania lub modeli przeszkolonych poza Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Przykład ONNX z zestawem SDK języka Python:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` dla katalogu, który zawiera pliki.

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Aby uwzględnić w rejestracji modelu wiele plików, ustaw `-p` dla katalogu, który zawiera pliki.

**Szacowany czas**: Około 10 sekund.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [klasa modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza usługą Azure Machine Learning, zobacz [jak wdrożyć istniejący model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

Poniższe cele obliczeniowe lub zasoby obliczeniowe mogą służyć do hostowania wdrożenia usługi sieci Web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Przygotowywanie do wdrożenia

Wdrożenie modelu wymaga kilku czynności:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.

    > [!IMPORTANT]
    > Zestaw SDK Azure Machine Learning nie udostępnia metody usługi sieci Web lub IoT Edge wdrożenia w celu uzyskania dostępu do magazynu danych lub zestawów. Jeśli chcesz, aby wdrożony model miał dostęp do danych przechowywanych poza wdrożeniem, na przykład na koncie usługi Azure Storage, musisz opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    > Kolejną alternatywą, która może posłużyć do danego scenariusza, są [przewidywania wsadowe](how-to-run-batch-predictions.md), które zapewniają dostęp do magazynów danych podczas oceniania.

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

* __Konfiguracja wdrożenia__ dla elementu docelowego obliczeń, który hostuje wdrożony model. Ta konfiguracja zawiera opis zagadnień dotyczących pamięci i procesora CPU wymaganych do uruchomienia modelu.

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__i __konfiguracji wdrożenia__. Konfiguracja wnioskowania odwołuje się do skryptu wejścia i innych zależności. Te konfiguracje są definiowane programowo podczas korzystania z zestawu SDK oraz jako pliki JSON w przypadku korzystania z interfejsu wiersza polecenia w celu wykonania wdrożenia.

### <a id="script"></a> 1. Definiowanie zależności & skryptu

Skrypt wejścia odbiera dane przesyłane do wdrożonej usługi sieci Web i przekazuje je do modelu. Następnie pobiera odpowiedź zwróconą przez model i zwraca go do klienta. **Skrypt jest specyficzny dla modelu**; musi on zrozumieć dane, które są oczekiwane i zwracane przez model.

Skrypt zawiera dwie funkcje, które ładują i uruchamiają model:

* `init()`: Zazwyczaj ta funkcja ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, gdy zostanie uruchomiony kontener platformy Docker dla usługi sieci Web.

* `run(input_data)`: Ta funkcja używa modelu do przewidywania wartości na podstawie danych wejściowych. Dane wejściowe i wyjściowe do przebiegu zazwyczaj używają formatu JSON do serializacji i deserializacji. Możesz również korzystać z nieprzetworzonych danych binarnych. Dane można przekształcić przed wysłaniem do modelu lub przed powrotem do klienta.

#### <a name="what-is-get_model_path"></a>Co to jest get_model_path?

Podczas rejestrowania modelu należy podać nazwę modelu służącą do zarządzania modelem w rejestrze. Ta nazwa jest używana z [modelem. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) , aby pobrać ścieżkę plików modelu w lokalnym systemie plików. Po zarejestrowaniu folderu lub kolekcji plików ten interfejs API zwróci ścieżkę do katalogu, który zawiera te pliki.

Po zarejestrowaniu modelu nadaj mu nazwę, która odnosi się do lokalizacji modelu, lokalnie lub podczas wdrażania usługi.

Poniższy przykład zwróci ścieżkę do pojedynczego pliku o nazwie `sklearn_mnist_model.pkl` (który został zarejestrowany przy użyciu nazwy `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-schema-generation"></a>Obowiązkowe Automatyczne generowanie schematu

Aby automatycznie wygenerować schemat dla usługi sieci Web, podaj przykład danych wejściowych i/lub danych wyjściowych w konstruktorze dla jednego z obiektów typu zdefiniowanego, a typ i przykład są używane do automatycznego tworzenia schematu. Usługa Azure Machine Learning następnie tworzy specyfikację [openapi](https://swagger.io/docs/specification/about/) (Swagger) dla usługi sieci Web podczas wdrażania.

Obecnie obsługiwane są następujące typy:

* `pandas`
* `numpy`
* `pyspark`
* standardowy obiekt języka Python

Aby użyć generacji schematu, Dołącz `inference-schema` pakiet do pliku środowiska Conda.

##### <a name="example-dependencies-file"></a>Przykładowy plik zależności

Następujący YAML jest przykładem pliku zależności Conda dla wnioskowania:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Jeśli chcesz użyć automatycznej generacji schematu, skrypt wejściowy **musi** importować `inference-schema` pakiety.

Zdefiniuj formaty próbek wejściowych i wyjściowych w `input_sample` zmiennych i `output_sample` , które reprezentują formaty żądań i odpowiedzi dla usługi sieci Web. Te przykłady są używane w funkcji Input i Output dekoratory `run()` funkcji. W poniższym przykładzie scikit jest stosowana funkcja generowania schematu.

##### <a name="example-entry-script"></a>Przykładowy skrypt wprowadzania

W poniższym przykładzie pokazano, jak przyjmować i zwracać dane JSON:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Poniższy przykład ilustruje sposób definiowania danych wejściowych jako `<key: value>` słownika przy użyciu ramki Dataframe. Ta metoda jest obsługiwana w przypadku używania wdrożonej usługi sieci Web z Power BI ([Dowiedz się więcej na temat korzystania z usługi sieci Web z programu Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Więcej przykładowych skryptów można znaleźć w następujących przykładach:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Dane binarne

Jeśli model akceptuje dane binarne, takie jak obraz, należy zmodyfikować `score.py` plik używany dla danego wdrożenia do akceptowania żądań HTTP raw. Aby zaakceptować dane pierwotne, użyj `AMLRequest` klasy w skrypcie wpisu i `@rawhttp` Dodaj dekoratora do `run()` funkcji.

Oto przykład `score.py` , który akceptuje dane binarne:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Klasa znajduje się `azureml.contrib` w przestrzeni nazw. `AMLRequest` Elementy w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.
>
> Jeśli musisz przetestować to w lokalnym środowisku programistycznym, możesz zainstalować składniki za pomocą następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Udostępnianie zasobów między źródłami to sposób zezwalania na żądanie zasobów na stronie sieci Web z innej domeny. Mechanizm CORS działa na podstawie nagłówków HTTP wysyłanych z żądaniem klienta i zwróconych z odpowiedzią usługi. Aby uzyskać więcej informacji na temat mechanizmu CORS i prawidłowych nagłówków, zobacz [udostępnianie zasobów między źródłami](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) w witrynie Wikipedia.

Aby skonfigurować wdrożenie modelu do obsługi mechanizmu CORS, użyj `AMLResponse` klasy w skrypcie wpisu. Ta klasa umożliwia ustawianie nagłówków obiektu Response.

Poniższy przykład ustawia `Access-Control-Allow-Origin` nagłówek odpowiedzi ze skryptu wprowadzania:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Klasa znajduje się `azureml.contrib` w przestrzeni nazw. `AMLResponse` Elementy w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.
>
> Jeśli musisz przetestować to w lokalnym środowisku programistycznym, możesz zainstalować składniki za pomocą następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definiowanie InferenceConfig

Konfiguracja wnioskowania opisuje, jak skonfigurować model do tworzenia prognoz. Ta konfiguracja nie jest częścią skryptu Wejścia; odwołuje się do skryptu wejścia i służy do lokalizowania wszystkich zasobów wymaganych przez wdrożenie. Jest on używany później podczas rzeczywistego wdrażania modelu.

W poniższym przykładzie pokazano, jak utworzyć konfigurację wnioskowania. Ta konfiguracja określa środowisko uruchomieniowe, skrypt wpisu oraz (opcjonalnie) plik środowiska Conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Aby uzyskać więcej informacji, zobacz odwołanie do klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Przykład interfejsu wiersza polecenia InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja zawiera następujące elementy:

* Że ten model wymaga języka Python
* [Skrypt wejścia](#script), który jest używany do obsługi żądań sieci Web wysyłanych do wdrożonej usługi
* Plik Conda, który opisuje pakiety języka Python, które są konieczne do wnioskowania

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiowanie konfiguracji wdrożenia

Przed wdrożeniem należy zdefiniować konfigurację wdrożenia. __Konfiguracja wdrożenia jest specyficzna dla elementu docelowego obliczeń, który będzie hostować usługę sieci Web__. Na przykład podczas wdrażania lokalnego należy określić port, w którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wejścia. Służy do definiowania właściwości obiektu docelowego obliczeń, który będzie hostować model i skrypt wejścia.

Może być również konieczne utworzenie zasobu obliczeniowego. Na przykład jeśli nie masz jeszcze usługi Azure Kubernetes skojarzonej z Twoim obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrożenia dla każdego obiektu docelowego obliczeń:

| Obliczeniowego elementu docelowego | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalny | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Wystąpienie kontenera platformy Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Każdą z tych klas dla lokalnych, ACI i AKS usług sieci Web można zaimportować z `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> Przed wdrożeniem modelu jako usługi można go profilować, aby określić optymalne wymagania dotyczące procesora i pamięci. Możesz profilować model przy użyciu zestawu SDK lub interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz Dokumentacja profilu [profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) i [AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) Reference.
>
> Wyniki profilowania modelu są emitowane jako `Run` obiekt. Aby uzyskać więcej informacji, zobacz odwołanie do klasy [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Wdróż do celu

Wdrożenie używa konfiguracji wdrożenia konfiguracji wnioskowania do wdrożenia modeli. Proces wdrażania jest podobny niezależnie od elementu docelowego obliczeń. Wdrażanie do AKS jest nieco inne, ponieważ należy podać odwołanie do klastra AKS.

### <a id="local"></a>Wdrożenie lokalne

Aby wdrożyć lokalnie, należy zainstalować platformę Docker na komputerze lokalnym.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)i usługi sieci [Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

### <a id="notebookvm"></a>Usługa sieci Web NotebookVM (DEVTEST)

Zobacz [Wdrażanie modelu na maszynach wirtualnych z notesem](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Zobacz [wdrażanie do Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Usługa Azure Kubernetes Service (DEVTEST & PRODUCTion)

Zobacz [wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Korzystaj z usług sieci Web

Każda wdrożona usługa sieci Web udostępnia interfejs API REST, dzięki czemu można tworzyć aplikacje klienckie w różnych językach programowania. Jeśli włączono uwierzytelnianie klucza dla usługi, musisz podać klucz usługi jako token w nagłówku żądania.
Jeśli włączono uwierzytelnianie tokenu dla usługi, musisz podać Azure Machine Learning token JWT jako token okaziciela w nagłówku żądania.

> [!TIP]
> Dokument JSON schematu można pobrać po wdrożeniu usługi. Użyj [Właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) ze wdrożonej usługi sieci Web, takiej jak `service.swagger_uri`, aby uzyskać identyfikator URI do pliku struktury Swagger lokalnej usługi sieci Web.

### <a name="request-response-consumption"></a>Żądanie — użycie odpowiedzi

Oto przykład sposobu wywoływania usługi w języku Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji klienckich do korzystania z usług WebService](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (Specyfikacja OpenAPI)

Jeśli użyto automatycznej generacji schematu we wdrożeniu, możesz uzyskać adres specyfikacji OpenAPI dla usługi przy użyciu [Właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Na przykład `print(service.swagger_uri)`. Aby pobrać specyfikację, użyj żądania GET (lub Otwórz identyfikator URI w przeglądarce).

Poniższy dokument JSON jest przykładem schematu (Specyfikacja OpenAPI) wygenerowanego dla wdrożenia:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat specyfikacji, zobacz artykuł [Specyfikacja Open API](https://swagger.io/specification/).

Aby uzyskać narzędzie, które może tworzyć biblioteki klienckie ze specyfikacją, zobacz [Swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Wnioskowanie partii
Azure Machine Learning cele obliczeń są tworzone i zarządzane przez usługę Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się ze wskazówkami dotyczącymi wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zapoznaj się z artykułem [jak uruchamiać przewidywania wsadowe](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz artykuł [wdrażanie Azure Machine Learning jako moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Aktualizowanie usług sieci Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Ciągłe wdrażanie modelu 

Możesz ciągle wdrażać modele przy użyciu rozszerzenia Machine Learning dla [usługi Azure DevOps](https://azure.microsoft.com/services/devops/). Za pomocą rozszerzenia Machine Learning dla usługi Azure DevOps, można wyzwolić potok wdrożenia, gdy nowy model uczenia maszynowego jest zarejestrowany w obszarze roboczym usługi Azure Machine Learning. 

1. Utwórz konto w celu [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), które zapewnia ciągłą integrację i dostarczanie aplikacji na dowolną platformę/dowolną chmurę. Azure Pipelines [różni się od potoków ml](concept-ml-pipelines.md#compare). 

1. [Utwórz projekt usługi Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Zainstaluj [rozszerzenie Machine Learning dla Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Użyj __połączeń usługi__ , aby skonfigurować połączenie główne usługi z obszarem roboczym usługi Azure Machine Learning, aby uzyskać dostęp do wszystkich artefaktów. Przejdź do pozycji Ustawienia projektu, kliknij pozycję połączenia usług, a następnie wybierz pozycję Azure Resource Manager.

    ![Widok-usługa-połączenie](media/how-to-deploy-and-where/view-service-connection.png) 

1. Zdefiniuj AzureMLWorkspace jako __poziom zakresu__ i Wypełnij kolejne parametry.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Następnie, aby ciągle wdrożyć model uczenia maszynowego przy użyciu Azure Pipelines, w obszarze potoki wybierz pozycję __Zwolnij__. Dodaj nowy artefakt, wybierz opcję artefakt modelu usługi Azure i połączenie z usługą, które zostało utworzone w poprzednim kroku. Wybierz model i wersję, aby wyzwolić wdrożenie. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Włącz wyzwalacz modelu w artefaktie modelu. Przez włączenie wyzwalacza, za każdym razem, gdy określona wersja (tj. Najnowsza wersja tego modelu jest zarejestrowana w Twoim obszarze roboczym, zostanie wyzwolony potok wydania usługi Azure DevOps. 

    ![Włączanie-model-wyzwalacz](media/how-to-deploy-and-where/set-modeltrigger.png)

Aby uzyskać więcej przykładowych projektów i przykładów, zobacz następujące przykładowe repozytoria:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.
Aby usunąć zarejestrowanego modelu, użyj `model.delete()`.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną usługi [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)i [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Następne kroki
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)

