---
title: Jak i gdzie wdrażać modele
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele Azure Machine Learning, w tym Azure Container Instances, Kubernetes usługi Azure, Azure IoT Edge i programowalne pola tablic bram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4a0736267ca00b67f35abc7cf263e7cf19543d81
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932123"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Wdrażanie modeli przy użyciu Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci Web w chmurze platformy Azure lub Azure IoT Edge urządzeń.

Przepływ pracy jest podobny do tego, [gdzie wdrażasz](#target) model:

1. Zarejestruj model.
1. Przygotuj się do wdrożenia. (Określ zasoby, użycie, cel obliczeń).
1. Wdróż model do obiektu docelowego obliczeń.
1. Przetestuj wdrożony model, nazywany również usługą sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Poniższy kod przedstawia sposób nawiązywania połączenia z obszarem roboczym Azure Machine Learning przy użyciu informacji w pamięci podręcznej w lokalnym środowisku programistycznym:

+ **Korzystanie z zestawu SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Aby uzyskać więcej informacji na temat używania zestawu SDK do łączenia się z obszarem roboczym, zobacz dokumentację [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Korzystanie z interfejsu wiersza polecenia**

   Korzystając z interfejsu wiersza polecenia, należy użyć parametru `-w` lub `--workspace-name`, aby określić obszar roboczy do użycia.

+ **Używanie VS Code**

   Gdy używasz VS Code, wybierz obszar roboczy przy użyciu interfejsu graficznego. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli i zarządzanie nimi](how-to-vscode-tools.md#deploy-and-manage-models) w dokumentacji rozszerzenia vs Code.

## <a id="registermodel"></a>Zarejestruj model

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](#locate-model-files-in-your-entry-script).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub w innym miejscu. W poniższych przykładach pokazano, jak zarejestrować model.

### <a name="register-a-model-from-an-experiment-run"></a>Rejestrowanie modelu na podstawie przebiegu eksperymentu

Fragmenty kodu w tej sekcji pokazują, jak zarejestrować model z poziomu przebiegu szkoleniowego:

> [!IMPORTANT]
> Aby móc korzystać z tych fragmentów, musisz wcześniej wykonać przebieg szkolenia i uzyskać dostęp do obiektu `Run` (przykład SDK) lub wartość identyfikatora uruchomienia (przykład interfejsu wiersza polecenia). Aby uzyskać więcej informacji na temat modeli szkoleniowych, zobacz [set up COMPUTE targets for model Training](how-to-set-up-training-targets.md).

+ **Korzystanie z zestawu SDK**

  Gdy korzystasz z zestawu SDK do uczenia modelu, możesz otrzymać obiekt [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) lub obiekt [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) , w zależności od tego, jak został przeszkolony model. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez uruchomienie eksperymentu.

  + Zarejestruj model na podstawie obiektu `azureml.core.Run`:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić wiele plików w rejestracji modelu, należy ustawić `model_path` na ścieżkę folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [uruchamiania. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Zarejestruj model na podstawie obiektu `azureml.train.automl.run.AutoMLRun`:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    W tym przykładzie `metric` i `iteration` parametry nie są określone, więc iteracja z najlepszą metryką podstawową zostanie zarejestrowana. Wartość `model_id` zwracana z przebiegu jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz dokumentację [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić wiele plików w rejestracji modelu, należy ustawić `--asset-path` na ścieżkę folderu zawierającego pliki.

+ **Używanie VS Code**

  Zarejestruj modele przy użyciu dowolnych plików lub folderów modelu przy użyciu rozszerzenia [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Model można zarejestrować, podając ścieżkę lokalną modelu. Można podać ścieżkę do folderu lub pojedynczego pliku. Za pomocą tej metody można rejestrować modele przeszkolone przy użyciu Azure Machine Learning, a następnie pobrać. Tej metody można również użyć do rejestrowania modeli przeszkolonych poza Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Korzystanie z zestawu SDK i ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
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

  Aby uwzględnić wiele plików w rejestracji modelu, należy ustawić `model_path` na ścieżkę folderu zawierającego pliki.

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Aby uwzględnić wiele plików w rejestracji modelu, należy ustawić `-p` na ścieżkę folderu zawierającego pliki.

**Szacowany czas**: około 10 sekund.

Aby uzyskać więcej informacji, zobacz dokumentację [klasy model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza Azure Machine Learning, zobacz [jak wdrożyć istniejący model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

Do hostowania wdrożenia usługi sieci Web można użyć następujących obiektów docelowych obliczeń lub zasobów obliczeniowych:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Przygotowywanie do wdrożenia

Aby wdrożyć model, potrzebne są następujące elementy:

* **Skrypt wejściowy**. Ten skrypt akceptuje żądania, ocenia żądania przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > * Skrypt wejścia jest specyficzny dla modelu. Musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    >   Jeśli dane żądania są w formacie, który nie jest użyteczny przez model, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed zwróceniem jej do klienta.
    >
    > * Zestaw SDK Azure Machine Learning nie umożliwia usługom sieci Web ani IoT Edge wdrożeń w celu uzyskania dostępu do magazynu danych lub zestawów. Jeśli wdrożony model musi uzyskać dostęp do danych przechowywanych poza wdrożeniem, takich jak dane na koncie usługi Azure Storage, należy opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    >   Alternatywą, która może obsłużyć twój scenariusz, jest [Prognoza wsadowa](how-to-run-batch-predictions.md), która zapewnia dostęp do magazynów danych podczas oceniania.

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania.

* **Konfiguracja wdrożenia** dla elementu docelowego obliczeń, który hostuje wdrożony model. Ta konfiguracja zawiera opis zagadnień dotyczących pamięci i procesora CPU wymaganych do uruchomienia modelu.

Te elementy są hermetyzowane w *konfiguracji wnioskowania* i *konfiguracji wdrożenia*. Konfiguracja wnioskowania odwołuje się do skryptu wejścia i innych zależności. Te konfiguracje można definiować programowo, gdy używasz zestawu SDK do wdrożenia. Użytkownik definiuje je w plikach JSON podczas korzystania z interfejsu wiersza polecenia.

### <a id="script"></a>1. Zdefiniuj skrypt i zależności wpisu

Skrypt wejścia odbiera dane przesyłane do wdrożonej usługi sieci Web i przekazuje je do modelu. Następnie pobiera odpowiedź zwróconą przez model i zwraca go do klienta. *Skrypt jest specyficzny dla modelu*. Musi on zrozumieć dane, które są oczekiwane i zwracane przez model.

Skrypt zawiera dwie funkcje, które ładują i uruchamiają model:

* `init()`: Zazwyczaj ta funkcja ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, gdy zostanie uruchomiony kontener platformy Docker dla usługi sieci Web.

* `run(input_data)`: Ta funkcja używa modelu do przewidywania wartości na podstawie danych wejściowych. Dane wejściowe i wyjściowe przebiegu zazwyczaj używają formatu JSON do serializacji i deserializacji. Możesz również korzystać z nieprzetworzonych danych binarnych. Dane można przekształcić przed wysłaniem ich do modelu lub przed zwróceniem ich do klienta.

#### <a name="locate-model-files-in-your-entry-script"></a>Lokalizowanie plików modelu w skrypcie wprowadzania

Istnieją dwa sposoby lokalizowania modeli w skrypcie wprowadzania:
* `AZUREML_MODEL_DIR`: zmienna środowiskowa zawierająca ścieżkę do lokalizacji modelu.
* `Model.get_model_path`: interfejs API, który zwraca ścieżkę do pliku modelu przy użyciu nazwy zarejestrowanego modelu.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR jest zmienną środowiskową utworzoną podczas wdrażania usługi. Możesz użyć tej zmiennej środowiskowej, aby znaleźć lokalizację wdrożonych modeli.

W poniższej tabeli opisano wartość AZUREML_MODEL_DIR w zależności od liczby wdrożonych modeli:

| Wdrożenie | Wartość zmiennej środowiskowej |
| ----- | ----- |
| Jeden model | Ścieżka do folderu zawierającego model. |
| Wiele modeli | Ścieżka do folderu zawierającego wszystkie modele. Modele są zlokalizowane według nazwy i wersji w tym folderze (`$MODEL_NAME/$VERSION`) |

Aby uzyskać ścieżkę do pliku w modelu, Połącz zmienną środowiskową z szukaną nazwą pliku.
Nazwy plików modelu są zachowywane podczas rejestracji i wdrożenia. 

**Przykład pojedynczego modelu**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Przykład wielu modeli**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Podczas rejestrowania modelu należy podać nazwę modelu, która jest używana do zarządzania modelem w rejestrze. Ta nazwa jest używana z metodą [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) w celu pobrania ścieżki pliku lub plików modelu w lokalnym systemie plików. Po zarejestrowaniu folderu lub kolekcji plików ten interfejs API zwróci ścieżkę do katalogu, który zawiera te pliki.

Po zarejestrowaniu modelu nadaj mu nazwę. Nazwa odnosi się do lokalizacji modelu, lokalnie lub podczas wdrażania usługi.

> [!IMPORTANT]
> Jeśli używasz automatycznej uczenia maszynowego do uczenia modelu, wartość `model_id` jest używana jako nazwa modelu. Aby zapoznać się z przykładem rejestrowania i wdrażania modelu przeszkolonego za pomocą automatycznego uczenia maszynowego, zobacz [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) w witrynie GitHub.

Poniższy przykład zwróci ścieżkę do pojedynczego pliku o nazwie `sklearn_mnist_model.pkl` (który został zarejestrowany przy użyciu nazwy `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Obowiązkowe Automatyczne generowanie schematu

Aby automatycznie wygenerować schemat dla usługi sieci Web, podaj przykład danych wejściowych i/lub wyjściowych w konstruktorze dla jednego z obiektów typu zdefiniowane. Typ i przykład są używane do automatycznego tworzenia schematu. Azure Machine Learning następnie tworzy specyfikację [openapi](https://swagger.io/docs/specification/about/) (Swagger) dla usługi sieci Web podczas wdrażania.

Te typy są obecnie obsługiwane:

* `pandas`
* `numpy`
* `pyspark`
* Standardowy obiekt języka Python

Aby użyć generacji schematu, należy uwzględnić pakiet `inference-schema` w pliku środowiska Conda. Aby uzyskać więcej informacji na temat tego pakietu, zobacz [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Przykładowy plik zależności

Następujący YAML jest przykładem pliku zależności Conda dla wnioskowania:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Jeśli zależność jest dostępna zarówno w Conda, jak i PIP (od PyPi), firma Microsoft zaleca korzystanie z wersji Conda, ponieważ pakiety Conda zwykle są dostarczane z wstępnie skompilowanymi plikami binarnymi, które sprawiają, że instalacja jest bardziej niezawodna.
>
> Aby uzyskać więcej informacji, zobacz [Omówienie Conda i PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Aby sprawdzić, czy zależność jest dostępna za pomocą Conda, użyj polecenia `conda search <package-name>` lub użyj indeksów pakietów w [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) i [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Jeśli chcesz użyć automatycznej generacji schematu, skrypt wejściowy musi zaimportować pakiety `inference-schema`.

Zdefiniuj Przykładowe formaty danych wejściowych i wyjściowych w zmiennych `input_sample` i `output_sample`, które reprezentują formaty żądań i odpowiedzi dla usługi sieci Web. Użyj tych przykładów w funkcji Input i Output dekoratory na funkcji `run()`. Poniższy przykład scikit-Dowiedz się, jak użyć generowania schematu.

##### <a name="example-entry-script"></a>Przykładowy skrypt wprowadzania

W poniższym przykładzie pokazano, jak przyjmować i zwracać dane JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Poniższy przykład ilustruje sposób definiowania danych wejściowych jako słownika `<key: value>` przy użyciu ramki Dataframe. Ta metoda jest obsługiwana w przypadku używania wdrożonej usługi sieci Web na podstawie Power BI. ([Dowiedz się więcej na temat korzystania z usługi sieci Web z programu Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)).

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Aby uzyskać więcej przykładów, zobacz następujące skrypty:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Dane binarne

Jeśli model akceptuje dane binarne, takie jak obraz, należy zmodyfikować plik `score.py` używany we wdrożeniu do akceptowania żądań nieprzetworzonych HTTP. Aby zaakceptować dane pierwotne, użyj klasy `AMLRequest` w skrypcie wpisu i Dodaj `@rawhttp` dekoratora do funkcji `run()`.

Oto przykład `score.py`, który akceptuje dane binarne:

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Klasa `AMLRequest` znajduje się w przestrzeni nazw `azureml.contrib`. Jednostki w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. Wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza, która nie jest w pełni obsługiwana przez firmę Microsoft.
>
> Jeśli konieczne jest przetestowanie go w lokalnym środowisku programistycznym, można zainstalować składniki przy użyciu następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Udostępnianie zasobów między źródłami to sposób zezwalania na żądanie zasobów na stronie sieci Web z innej domeny. Mechanizm CORS działa przez nagłówki HTTP wysyłane z żądaniem klienta i zwracane z odpowiedzią usługi. Aby uzyskać więcej informacji na temat mechanizmu CORS i prawidłowych nagłówków, zobacz [udostępnianie zasobów między źródłami](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) w witrynie Wikipedia.

Aby skonfigurować wdrożenie modelu do obsługi mechanizmu CORS, użyj klasy `AMLResponse` w skrypcie wpisu. Ta klasa umożliwia ustawianie nagłówków obiektu Response.

Poniższy przykład ustawia nagłówek `Access-Control-Allow-Origin` odpowiedzi ze skryptu wpisu:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Klasa `AMLResponse` znajduje się w przestrzeni nazw `azureml.contrib`. Jednostki w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. Wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza, która nie jest w pełni obsługiwana przez firmę Microsoft.
>
> Jeśli konieczne jest przetestowanie go w lokalnym środowisku programistycznym, można zainstalować składniki przy użyciu następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Zdefiniuj InferenceConfig

Konfiguracja wnioskowania opisuje, jak skonfigurować model do tworzenia prognoz. Ta konfiguracja nie jest częścią skryptu wejścia. Odwołuje się do skryptu wejścia i służy do lokalizowania wszystkich zasobów wymaganych przez wdrożenie. Jest on używany później podczas wdrażania modelu.

Konfiguracja wnioskowania może używać środowisk Azure Machine Learning do definiowania zależności oprogramowania wymaganego do wdrożenia. Środowiska umożliwiają tworzenie i ponowne używanie zależności oprogramowania wymaganych do szkolenia i wdrażania, a także zarządzanie nimi. Poniższy przykład ilustruje ładowanie środowiska z obszaru roboczego, a następnie używanie go z konfiguracją wnioskowania:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Można również bezpośrednio określić zależności bez użycia środowiska. W poniższym przykładzie pokazano, jak utworzyć konfigurację wnioskowania, która ładuje zależności oprogramowania z pliku Conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Aby uzyskać więcej informacji, zobacz dokumentację klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Przykład interfejsu wiersza polecenia InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja określa następujące ustawienia:

* Model wymaga języka Python.
* [Skrypt wejścia](#script), który jest używany do obsługi żądań sieci Web wysyłanych do wdrożonej usługi.
* Plik Conda, który opisuje pakiety języka Python, które są zbędne do wnioskowania.

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Zdefiniuj konfigurację wdrożenia

Przed wdrożeniem modelu należy zdefiniować konfigurację wdrożenia. *Konfiguracja wdrożenia jest specyficzna dla elementu docelowego obliczeń, który będzie hostować usługę sieci Web.* Na przykład podczas lokalnego wdrażania modelu należy określić port, w którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wejścia. Służy do definiowania właściwości obiektu docelowego obliczeń, który będzie hostować model i skrypt wejścia.

Może być również konieczne utworzenie zasobu obliczeniowego, jeśli na przykład nie masz jeszcze wystąpienia usługi Azure Kubernetes Service (AKS) skojarzonego z Twoim obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrożenia dla każdego obiektu docelowego obliczeń:

| Docelowy zasób obliczeniowy | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalna | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasy dla lokalnych, Azure Container Instances i usług sieci Web AKS można zaimportować z `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilowanie

Przed wdrożeniem modelu jako usługi można go profilować, aby określić optymalne wymagania dotyczące procesora i pamięci. Aby profilować model, można użyć zestawu SDK lub interfejsu wiersza polecenia. W poniższych przykładach pokazano, jak profilować model przy użyciu zestawu SDK.

> [!IMPORTANT]
> W przypadku korzystania z profilowania konfiguracja wnioskowania nie może odwoływać się do Azure Machine Learning środowiska. Zamiast tego należy zdefiniować zależności oprogramowania przy użyciu `conda_file` parametru `InferenceConfig` obiektu.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Ten kod wyświetla wynik podobny do następującego:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Wyniki profilowania modelu są emitowane jako obiekt `Run`.

Aby uzyskać informacje na temat korzystania z profilowania z interfejsu wiersza polecenia, zobacz [AZ ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Aby uzyskać więcej informacji, zobacz następujące dokumenty:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profil ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Schemat pliku konfiguracji wnioskowania](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Wdróż do celu

Wdrożenie używa konfiguracji wdrożenia konfiguracji wnioskowania do wdrożenia modeli. Proces wdrażania jest podobny niezależnie od elementu docelowego obliczeń. Wdrażanie do AKS jest nieco inne, ponieważ należy podać odwołanie do klastra AKS.

### <a id="local"></a>Wdrożenie lokalne

Aby lokalnie wdrożyć model, należy zainstalować platformę Docker na komputerze lokalnym.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)i usługi sieci [Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć model przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Więcej informacji można znaleźć w dokumentacji [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Usługa sieci Web notesu maszyny wirtualnej (Tworzenie i testowanie)

Zobacz [Wdrażanie modelu na potrzeby Azure Machine Learning notesu maszyn wirtualnych](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (Tworzenie i testowanie)

Zobacz [wdrażanie do Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Usługa Azure Kubernetes (Tworzenie i testowanie i produkcja)

Zobacz [wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Korzystaj z usług sieci Web

Każda wdrożona usługa sieci Web udostępnia interfejs API REST, dzięki czemu można tworzyć aplikacje klienckie w różnych językach programowania.
Jeśli włączono uwierzytelnianie klucza dla usługi, musisz podać klucz usługi jako token w nagłówku żądania.
Jeśli włączono uwierzytelnianie tokenu dla usługi, musisz podać Azure Machine Learning token JWT jako token okaziciela w nagłówku żądania.

> [!TIP]
> Po wdrożeniu usługi można pobrać dokument JSON schematu. Użyj [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) ze wdrożonej usługi sieci Web (na przykład `service.swagger_uri`), aby uzyskać identyfikator URI do pliku struktury Swagger lokalnej usługi sieci Web.

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

Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji klienckich do korzystania z usług sieci Web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (Specyfikacja OpenAPI)

Jeśli używasz automatycznej generacji schematu w ramach wdrożenia, możesz uzyskać adres specyfikacji OpenAPI dla usługi przy użyciu [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Na przykład `print(service.swagger_uri)`.) Użyj żądania GET lub Otwórz identyfikator URI w przeglądarce, aby pobrać specyfikację.

Poniższy dokument JSON jest przykładem schematu (Specyfikacja OpenAPI) wygenerowanego dla wdrożenia:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

Aby uzyskać więcej informacji, zobacz [Specyfikacja openapi](https://swagger.io/specification/).

Aby uzyskać narzędzie, które może tworzyć biblioteki klienckie ze specyfikacją, zobacz [Swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Wnioskowanie partii
Azure Machine Learning obiekty docelowe obliczeń są tworzone i zarządzane przez Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się z przewodnikiem dotyczącym wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zobacz [jak uruchamiać przewidywania wsadowe](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Machine Learning jako moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Aktualizowanie usług sieci Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Ciągle Wdrażaj modele

Możesz ciągle wdrażać modele przy użyciu rozszerzenia Machine Learning dla [usługi Azure DevOps](https://azure.microsoft.com/services/devops/). Możesz użyć rozszerzenia Machine Learning dla usługi Azure DevOps, aby wyzwolić potok wdrożenia w przypadku zarejestrowania nowego modelu uczenia maszynowego w obszarze roboczym Azure Machine Learning.

1. Utwórz konto, aby uzyskać [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), które zapewnia ciągłą integrację i dostarczanie aplikacji na dowolną platformę lub chmurę. (Należy zauważyć, że Azure Pipelines nie są takie same jak [potoki Machine Learning](concept-ml-pipelines.md#compare).)

1. [Utwórz projekt usługi Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Zainstaluj [rozszerzenie Machine Learning dla Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Użyj połączeń usługi, aby skonfigurować połączenie główne usługi z obszarem roboczym Azure Machine Learning, aby umożliwić dostęp do artefaktów. Przejdź do pozycji Ustawienia projektu, wybierz pozycję **połączenia usługi**, a następnie wybierz pozycję **Azure Resource Manager**:

    [![wybierz Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na liście **poziom zakresu** wybierz pozycję **AzureMLWorkspace**, a następnie wprowadź resztę wartości:

    ![Wybierz AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Aby ciągle wdrożyć model uczenia maszynowego przy użyciu Azure Pipelines, w obszarze potoki wybierz pozycję **Zwolnij**. Dodaj nowy artefakt, a następnie wybierz artefakt **modelu usługi Azure** i utworzone wcześniej połączenie z usługą. Wybierz model i wersję, aby wyzwolić wdrożenie:

    [![wybierz model Azure](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Włącz wyzwalacz modelu w artefaktie modelu. Po włączeniu wyzwalacza za każdym razem, gdy określona wersja (czyli Najnowsza wersja) tego modelu jest zarejestrowana w obszarze roboczym, zostanie wyzwolony potok wydania usługi Azure DevOps.

    [![włączyć wyzwalacz modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Aby uzyskać więcej przykładowych projektów i przykładów, zobacz te przykładowe repozytoria w witrynie GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Pobierz model
Jeśli chcesz pobrać model do użycia w środowisku wykonawczym, możesz to zrobić za pomocą następujących poleceń zestawu SDK/interfejsu wiersza polecenia:

ZESTAWIE
```python
model_path = Model(ws,'mymodel').download()
```

Interfejs
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Wdrożenie modelu bez kodu jest obecnie dostępne w wersji zapoznawczej i obsługuje następujące platformy uczenia maszynowego:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modele ONNX

Rejestracja i wdrożenie modelu ONNX są obsługiwane w przypadku grafu wnioskowania ONNX. Kroki preprocesora i PostProcess nie są obecnie obsługiwane.

Oto przykład sposobu rejestrowania i wdrażania modelu ONNX MNIST ręcznie:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit — uczenie modeli

Żadne wdrożenie modelu kodu nie jest obsługiwane dla wszystkich wbudowanych typów modeli scikit.

Oto przykład sposobu rejestrowania i wdrażania modelu skryptu sklearn bez dodatkowego kodu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Uwaga: te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania skryptu sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modele pakietów

W niektórych przypadkach może być konieczne utworzenie obrazu platformy Docker bez wdrażania modelu (Jeśli na przykład planujesz [wdrożyć program w Azure App Service](how-to-deploy-app-service.md)). Możesz też chcieć pobrać obraz i uruchomić go w lokalnej instalacji platformy Docker. Możesz nawet pobrać pliki użyte do skompilowania obrazu, sprawdzić je, zmodyfikować i ręcznie utworzyć obraz.

Pakowanie modeli umożliwia wykonywanie tych czynności. Wszystkie zasoby potrzebne do hostowania modelu jako usługi sieci Web i umożliwiają pobranie w pełni skompilowanego obrazu platformy Docker lub plików niezbędnych do jego skompilowania. Istnieją dwa sposoby używania pakowania modelu:

**Pobierz spakowany model:** Pobierz obraz platformy Docker zawierający model i inne pliki niezbędne do hostowania go jako usługi sieci Web.

**Generuj element pliku dockerfile:** Pobierz pliku dockerfile, model, skrypt wejściowy i inne zasoby, które są konieczne do skompilowania obrazu platformy Docker. Następnie można przeprowadzić inspekcję plików lub wprowadzić zmiany przed rozpoczęciem tworzenia obrazu lokalnie.

Oba pakiety mogą służyć do pobrania lokalnego obrazu platformy Docker.

> [!TIP]
> Tworzenie pakietu jest podobne do wdrażania modelu. Używasz zarejestrowanego modelu i konfiguracji wnioskowania.

> [!IMPORTANT]
> Aby pobrać pełny obraz lub utworzyć obraz lokalnie, należy zainstalować [platformę Docker](https://www.docker.com) w środowisku deweloperskim.

### <a name="download-a-packaged-model"></a>Pobierz spakowany model

Poniższy przykład tworzy obraz, który jest rejestrowany w usłudze Azure Container Registry dla Twojego obszaru roboczego:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po utworzeniu pakietu można użyć `package.pull()`, aby ściągnąć obraz do lokalnego środowiska Docker. W danych wyjściowych tego polecenia zostanie wyświetlona nazwa obrazu. Na przykład: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po pobraniu modelu Użyj polecenia `docker images`, aby wyświetlić listę obrazów lokalnych:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Aby uruchomić kontener lokalny na podstawie tego obrazu, użyj następującego polecenia, aby uruchomić nazwany kontener z powłoki lub wiersza polecenia. Zastąp wartość `<imageid>` IDENTYFIKATORem obrazu zwracanym przez polecenie `docker images`.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage`. Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Generowanie pliku dockerfile i zależności

Poniższy przykład pokazuje, jak pobrać pliku dockerfile, model i inne zasoby, które są konieczne do lokalnego utworzenia obrazu. Parametr `generate_dockerfile=True` wskazuje, że chcesz, aby pliki, a nie w pełni skompilowany obraz.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ten kod pobiera pliki, które są konieczne do skompilowania obrazu do katalogu `imagefiles`. Pliku dockerfile zawarte w zapisanych plikach odwołuje się do obrazu podstawowego przechowywanego w rejestrze kontenerów platformy Azure. Podczas tworzenia obrazu w lokalnej instalacji platformy Docker należy użyć adresu, nazwy użytkownika i hasła w celu uwierzytelnienia w rejestrze. Wykonaj następujące kroki, aby skompilować obraz przy użyciu lokalnej instalacji platformy Docker:

1. Z poziomu powłoki lub sesji wiersza polecenia Użyj następującego polecenia, aby uwierzytelnić platformę Docker za pomocą usługi Azure Container Registry. Zastąp `<address>`, `<username>`i `<password>` wartościami pobranymi przez `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Aby skompilować obraz, użyj następującego polecenia. Zastąp `<imagefiles>` ścieżką katalogu, w którym `package.save()` zapisano pliki.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    To polecenie ustawia nazwę obrazu do `myimage`.

Aby sprawdzić, czy obraz został skompilowany, użyj polecenia `docker images`. Na liście powinien być widoczny obraz `myimage`:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Aby rozpocząć nowy kontener oparty na tym obrazie, użyj następującego polecenia:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage`. Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Przykładowy klient do testowania lokalnego kontenera

Poniższy kod stanowi przykład klienta języka Python, który może być używany z kontenerem:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Na przykład klientów w innych językach programowania można znaleźć w temacie [Korzystanie z modeli wdrożonych jako usługi sieci Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zatrzymaj kontener platformy Docker

Aby zatrzymać kontener, użyj następującego polecenia z innej powłoki lub wiersza polecenia:

```bash
docker kill mycontainer
```

## <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Wdrożenie modelu bez kodu jest obecnie dostępne w wersji zapoznawczej i obsługuje następujące platformy uczenia maszynowego:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modele ONNX

Rejestracja i wdrożenie modelu ONNX są obsługiwane w przypadku grafu wnioskowania ONNX. Kroki preprocesora i PostProcess nie są obecnie obsługiwane.

Oto przykład sposobu rejestrowania i wdrażania modelu ONNX MNIST ręcznie:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit — uczenie modeli

Żadne wdrożenie modelu kodu nie jest obsługiwane dla wszystkich wbudowanych typów modeli scikit.

Oto przykład sposobu rejestrowania i wdrażania modelu skryptu sklearn bez dodatkowego kodu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Uwaga: te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania skryptu sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wdrożoną usługę sieci Web, użyj `service.delete()`.
Aby usunąć zarejestrowany model, użyj `model.delete()`.

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) i [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie Azure Machine Learning usług sieci Web przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)

