---
title: Jak i gdzie wdrożyć modele
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrożyć modele usługi Azure Machine Learning, w tym wystąpienia kontenerów platformy Azure, usługę Azure Kubernetes, usługę Azure IoT Edge i tablice bramy programowalne w terenie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 96d9a0722ae04dc150b639dced34fa290da93630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159420"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Wdrażanie modeli za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci web w chmurze platformy Azure lub na urządzeniach usługi Azure IoT Edge.

Przepływ pracy jest podobny bez względu na [to, gdzie wdrożysz](#target) model:

1. Zarejestrowanie modelu.
1. Przygotowanie do wdrożenia. (Określenie zasobów, użycia, docelowego obiektu obliczeniowego).
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj wdrożony model, nazywany również usługą sieci web.

Aby uzyskać więcej informacji na temat koncepcji dotyczących przepływu pracy wdrażania, zobacz [Zarządzanie, wdrażanie i monitorowanie modeli za pomocą usługi Azure Machine Learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć plików modelu i zależności podanych w [tym samouczku](https://aka.ms/azml-deploy-cloud).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub rozszerzenie kodu usługi Azure Machine Learning Visual [Studio.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Poniższy kod pokazuje, jak połączyć się z obszarem roboczym usługi Azure Machine Learning przy użyciu informacji buforowanych w lokalnym środowisku programistycznym:

+ **Używanie zestawu SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Aby uzyskać więcej informacji na temat korzystania z SDK do łączenia się z obszarem roboczym, zobacz [zestaw SDK usługi Azure Machine Learning dla](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) języka Python dokumentacji.

+ **Korzystanie z interfejsu wiersza polecenia**

   W przypadku korzystania z `-w` `--workspace-name` interfejsu wiersza polecenia należy użyć parametru lub parametru, aby określić obszar roboczy polecenia.

+ **Korzystanie z narzędzia Visual Studio Code**

   Korzystając z programu Visual Studio Code, należy wybrać obszar roboczy przy użyciu interfejsu graficznego. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli i zarządzanie nimi](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) w dokumentacji rozszerzenia kodu programu Visual Studio.

## <a name="register-your-model"></a><a id="registermodel"></a>Zarejestruj swój model

Zarejestrowany model jest kontenerem logicznym dla jednego lub więcej plików, które tworzą model. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, można zarejestrować je jako pojedynczy model w obszarze roboczym. Po zarejestrowaniu plików można następnie pobrać lub wdrożyć zarejestrowany model i otrzymać wszystkie zarejestrowane pliki.

> [!TIP]
> Podczas rejestrowania modelu, należy podać ścieżkę lokalizacji w chmurze (z przebiegu szkolenia) lub katalogu lokalnego. Ta ścieżka jest po prostu zlokalizować pliki do przekazania w ramach procesu rejestracji. Nie trzeba dopasowywać ścieżkę używaną w skrypcie wpisu. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](#load-model-files-in-your-entry-script).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym usługi Azure Machine Learning. Model może pochodzić z usługi Azure Machine Learning lub z innego miejsca. Podczas rejestrowania modelu, można opcjonalnie podać metadane dotyczące modelu. `tags` Słowniki `properties` i słowniki, które można zastosować do rejestracji modelu mogą być następnie używane do filtrowania modeli.

Poniższe przykłady pokazują, jak zarejestrować model.

### <a name="register-a-model-from-an-experiment-run"></a>Rejestrowanie modelu z przebiegu eksperymentu

Fragmenty kodu w tej sekcji pokazują, jak zarejestrować model z przebiegu szkolenia:

> [!IMPORTANT]
> Aby użyć tych fragmentów, należy wcześniej wykonać przebieg szkolenia i trzeba mieć `Run` dostęp do obiektu (przykład SDK) lub wartości identyfikatora uruchomienia (przykład interfejsu wiersza polecenia). Aby uzyskać więcej informacji na temat modeli szkoleniowych, zobacz [Konfigurowanie celów obliczeniowych dla szkolenia modelu](how-to-set-up-training-targets.md).

+ **Używanie zestawu SDK**

  Podczas korzystania z SDK do szkolenia modelu, można odbierać [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) obiektu lub [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) obiektu, w zależności od sposobu trenowania modelu. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez przebieg eksperymentu.

  + Zarejestruj model z `azureml.core.Run` obiektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Parametr `model_path` odnosi się do lokalizacji w chmurze modelu. W tym przykładzie używana jest ścieżka pojedynczego pliku. Aby dołączyć wiele plików do `model_path` rejestracji modelu, ustaw ścieżkę folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz dokumentację [Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Zarejestruj model z `azureml.train.automl.run.AutoMLRun` obiektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    W tym `metric` przykładzie `iteration` i parametry nie są określone, więc iteracji z najlepszej metryki podstawowej zostaną zarejestrowane. Wartość `model_id` zwrócona z przebiegu jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz dokumentację [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Parametr `--asset-path` odnosi się do lokalizacji w chmurze modelu. W tym przykładzie używana jest ścieżka pojedynczego pliku. Aby dołączyć wiele plików do `--asset-path` rejestracji modelu, ustaw ścieżkę folderu zawierającego pliki.

+ **Korzystanie z narzędzia Visual Studio Code**

  Rejestruj modele przy użyciu dowolnych plików modelu lub folderów przy użyciu rozszerzenia [kodu programu Visual Studio.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Model można zarejestrować, udostępniając ścieżkę lokalną modelu. Można podać ścieżkę folderu lub pojedynczego pliku. Ta metoda służy do rejestrowania modeli przeszkolonych w usłudze Azure Machine Learning, a następnie pobranych. Tej metody można również użyć do rejestrowania modeli przeszkolonych poza usługą Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Korzystanie z SDK i ONNX**

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

  Aby dołączyć wiele plików do `model_path` rejestracji modelu, ustaw ścieżkę folderu zawierającego pliki.

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Aby dołączyć wiele plików do `-p` rejestracji modelu, ustaw ścieżkę folderu zawierającego pliki.

**Szacowanie czasu:** około 10 sekund.

Aby uzyskać więcej informacji, zobacz dokumentację [klasy Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza usługą Azure Machine Learning, zobacz [Jak wdrożyć istniejący model.](how-to-deploy-existing-model.md)

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pojedyncze i wielomodelowe punkty końcowe
Usługa Azure ML obsługuje wdrażanie pojedynczych lub wielu modeli za jednym punktem końcowym.

Punkty końcowe wielu modeli używają udostępnionego kontenera do obsługi wielu modeli. Pomaga to zmniejszyć koszty ogólne, zwiększa wykorzystanie i umożliwia łączenie modułów w zespoły. Modele określone w skrypcie wdrażania są montowane i udostępniane na dysku kontenera obsługującego — można załadować je do pamięci na żądanie i uzyskać wynik na podstawie określonego modelu żądanego w czasie oceniania.

Przykład E2E, w którym pokazano, jak używać wielu modeli za pojedynczym konteneryzowanym punktem końcowym, zobacz [ten przykład](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Przygotowywanie do wdrożenia

Aby wdrożyć model jako usługę, potrzebne są następujące składniki:

* **Zdefiniuj środowisko wnioskowania**. To środowisko hermetyzuje zależności wymagane do uruchomienia modelu do wnioskowania.
* **Zdefiniuj kod punktacji**. Ten skrypt akceptuje żądania, ocenia żądania przy użyciu modelu i zwraca wyniki.
* **Zdefiniuj konfigurację wnioskowania**. Konfiguracja wnioskowania określa konfigurację środowiska, skrypt wejścia i inne składniki potrzebne do uruchomienia modelu jako usługi.

Po uzyskaniu niezbędnych składników można profilować usługę, która zostanie utworzona w wyniku wdrożenia modelu, aby zrozumieć jego wymagania dotyczące procesora CPU i pamięci.

### <a name="1-define-inference-environment"></a>1. Definiowanie środowiska wnioskowania

Konfiguracja wnioskowania opisuje sposób konfigurowania usługi sieci web zawierającej model. Jest używany później, podczas wdrażania modelu.

Konfiguracja wnioskowania używa środowisk usługi Azure Machine Learning do definiowania zależności oprogramowania potrzebnych do wdrożenia. Środowiska umożliwiają tworzenie, zarządzanie i ponowne używanie zależności oprogramowania wymaganych do szkolenia i wdrażania. Można utworzyć środowisko z niestandardowych plików zależności lub użyć jednego z wyselekcjonowanych środowisk usługi Azure Machine Learning. Poniższy YAML jest przykładem pliku zależności Conda do wnioskowania. Należy zauważyć, że należy wskazać azureml-defaults z verion >= 1.0.45 jako zależności pip, ponieważ zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web. Jeśli chcesz użyć automatycznego generowania schematu, skrypt `inference-schema` wejścia musi również zaimportować pakiety.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Jeśli zależność jest dostępna za pośrednictwem Conda i pip (z PyPi), firma Microsoft zaleca użycie wersji Conda, ponieważ pakiety Conda zazwyczaj są wyposażone w wstępnie utworzone pliki binarne, które sprawiają, że instalacja jest bardziej niezawodna.
>
> Aby uzyskać więcej informacji, zobacz [Opis Conda i Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Aby sprawdzić, czy zależność jest dostępna `conda search <package-name>` za pośrednictwem firmy Conda, [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)użyj polecenia lub użyj indeksów pakietów w i .

Za pomocą pliku zależności można utworzyć obiekt środowiska i zapisać go w obszarze roboczym do wykorzystania w przyszłości:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Zdefiniuj kod punktacji

Skrypt wejściowy odbiera dane przesyłane do wdrożonej usługi internetowej i przekazuje je do modelu. Następnie pobiera odpowiedź zwróconą przez model i zwraca ją do klienta. *Skrypt jest specyficzny dla modelu*. Musi zrozumieć dane, które oczekuje i zwraca modelu.

Skrypt zawiera dwie funkcje, które ładują i uruchamiają model:

* `init()`: Zazwyczaj ta funkcja ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, gdy zostanie uruchomiony kontener platformy Docker dla usługi sieci web.

* `run(input_data)`: Ta funkcja używa modelu do przewidywania wartości na podstawie danych wejściowych. Dane wejściowe i wyjściowe uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji. Można także pracować z nieprzetworzonymi danymi binarnymi. Dane można przekształcić przed wysłaniem ich do modelu lub przed zwróceniem do klienta.

#### <a name="load-model-files-in-your-entry-script"></a>Ładowanie plików modelu w skrypcie wprowadzania

Istnieją dwa sposoby lokalizowania modeli w skrypcie wejścia:
* `AZUREML_MODEL_DIR`: Zmienna środowiskowa zawierająca ścieżkę do lokalizacji modelu.
* `Model.get_model_path`: Interfejs API, który zwraca ścieżkę do pliku modelu przy użyciu nazwy zarejestrowanego modelu.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR jest zmienną środowiskową utworzoną podczas wdrażania usługi. Tej zmiennej środowiskowej można użyć do znalezienia lokalizacji wdrożonych modeli.

W poniższej tabeli opisano wartość AZUREML_MODEL_DIR w zależności od liczby wdrożonych modeli:

| wdrażania | Wartość zmiennej środowiskowej |
| ----- | ----- |
| Pojedynczy model | Ścieżka do folderu zawierającego model. |
| Wiele modeli | Ścieżka do folderu zawierającego wszystkie modele. Modele znajdują się według nazwy i`$MODEL_NAME/$VERSION`wersji w tym folderze ( ) |

Podczas rejestracji i wdrażania modelu modele są umieszczane w ścieżce AZUREML_MODEL_DIR, a ich oryginalne nazwy plików są zachowywane.

Aby uzyskać ścieżkę do pliku modelu w skrypcie wpisu, połącz zmienną środowiskową ze ścieżką pliku, której szukasz.

**Przykład pojedynczego modelu**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Przykład wielu modeli**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Podczas rejestrowania modelu, należy podać nazwę modelu, który jest używany do zarządzania modelu w rejestrze. Ta nazwa jest używana przy użyciu metody [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) do pobierania ścieżki pliku modelu lub plików w lokalnym systemie plików. Jeśli zarejestrujesz folder lub zbiór plików, ten interfejs API zwraca ścieżkę katalogu zawierającego te pliki.

Podczas rejestrowania modelu, nadać mu nazwę. Nazwa odpowiada, gdzie model jest umieszczony, lokalnie lub podczas wdrażania usługi.

#### <a name="optional-define-model-web-service-schema"></a>(Opcjonalnie) Definiowanie schematu usługi sieci Web modelu

Aby automatycznie wygenerować schemat dla usługi sieci web, podaj próbkę danych wejściowych i/lub wyjściowych w konstruktorze dla jednego z obiektów zdefiniowanego typu. Typ i przykład są używane do automatycznego tworzenia schematu. Usługa Azure Machine Learning następnie tworzy specyfikację [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) dla usługi sieci web podczas wdrażania.

Te typy są obecnie obsługiwane:

* `pandas`
* `numpy`
* `pyspark`
* Standardowy obiekt Pythona

Aby użyć generowania schematu, `inference-schema` dołącz pakiet open source w pliku zależności. Aby uzyskać więcej informacji [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)na temat tego pakietu, zobacz . Zdefiniuj formaty przykładowe dane wejściowe `input_sample` i wyjściowe w i `output_sample` zmiennych, które reprezentują formaty żądań i odpowiedzi dla usługi sieci web. Użyj tych próbek w dekoratorów funkcji `run()` wejścia i wyjścia na funkcji. Poniższy przykład scikit-learn używa generowania schematu.

##### <a name="example-entry-script"></a>Przykładowy skrypt wpisowy

W poniższym przykładzie pokazano, jak akceptować i zwracać dane JSON:

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

W poniższym przykładzie pokazano, jak `<key: value>` zdefiniować dane wejściowe jako słownik przy użyciu DataFrame. Ta metoda jest obsługiwana w celu korzystania z wdrożonej usługi sieci web z usługi Power BI. (Dowiedz[się więcej o korzystaniu z usługi sieci Web w usłudze Power BI).](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

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

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Dane binarne](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definiowanie konfiguracji wnioskowania
    
W poniższym przykładzie pokazano ładowanie środowiska z obszaru roboczego, a następnie przy użyciu go z konfiguracją wnioskowania:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Aby uzyskać więcej informacji na temat środowisk, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md)

Aby uzyskać więcej informacji na temat konceplowania wnioskowania, zobacz [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) dokumentacji klasy.

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Cli przykład InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja określa następujące ustawienia:

* Że model wymaga Pythona.
* [Skrypt wpisu](#script), który jest używany do obsługi żądań sieci web wysyłanych do wdrożonej usługi.
* Plik Conda, który opisuje pakiety Języka Python potrzebne do wnioskowania.

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Opcjonalnie) Profil modelu w celu określenia wykorzystania zasobów

Po zarejestrowaniu modelu i przygotowaniu innych składników niezbędnych do jego wdrożenia można określić procesor i pamięć, których będzie potrzebować wdrożona usługa. Profilowanie testuje usługę, która uruchamia model i zwraca informacje, takie jak użycie procesora CPU, użycie pamięci i opóźnienie odpowiedzi. Zawiera również zalecenie dla procesora CPU i pamięci na podstawie użycia zasobów.

Aby profilować swój model, będziesz potrzebował:
* Zarejestrowany model.
* Konfiguracja wnioskowania na podstawie skryptu wejścia i definicji środowiska wnioskowania.
* Zestaw danych tabelaryczne pojedynczej kolumny, w którym każdy wiersz zawiera ciąg reprezentujący przykładowe dane żądania.

> [!IMPORTANT]
> W tym momencie obsługujemy tylko profilowanie usług, które oczekują, że ich dane żądania będą ciągiem, na przykład: ciąg serializowany json, tekst, ciąg serializowany obraz, itp. Zawartość każdego wiersza zestawu danych (ciąg) zostaną umieszczone w treści żądania HTTP i wysyłane do usługi hermetyzacji modelu do oceniania.

Poniżej znajduje się przykład, jak można skonstruować zestaw danych wejściowych do profilu usługi, która oczekuje, że jego przychodzące dane żądania zawierają serializowane json. W takim przypadku utworzyliśmy zestaw danych oparty na stu wystąpieniach tej samej zawartości danych żądania. W rzeczywistych scenariuszach sugerujemy, aby użyć większych zestawów danych zawierających różne dane wejściowe, zwłaszcza jeśli użycie/zachowanie zasobu modelu jest zależne od danych wejściowych.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Po przygotowaniu zestawu danych zawierającego przykładowe dane żądania utwórz konfigurację wnioskowania. Konfiguracja wnioskowania opiera się na score.py i definicji środowiska. W poniższym przykładzie pokazano, jak utworzyć konfigurację wnioskowania i uruchomić profilowanie:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Następujące polecenie pokazuje, jak profilować model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Aby utrwalić informacje zwrócone przez profilowanie, użyj tagów lub właściwości dla modelu. Za pomocą tagów lub właściwości przechowuje dane z modelu w rejestrze modelu. Poniższe przykłady pokazują dodanie nowego `requestedCpu` tagu zawierającego informacje i `requestedMemoryInGb` informacje:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Wdrażanie w celu ukierunkowania

Wdrożenie używa konfiguracji wdrażania konfiguracji wnioskowania do wdrożenia modeli. Proces wdrażania jest podobny niezależnie od obiektu docelowego obliczeń. Wdrażanie w u usług AKS jest nieco inna, ponieważ należy podać odwołanie do klastra AKS.

### <a name="choose-a-compute-target"></a>Wybieranie celu obliczeniowego

Do hostowania wdrożenia usługi sieci web można użyć następujących obiektów docelowych lub zasobów obliczeniowych:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Definiowanie konfiguracji wdrożenia

Przed wdrożeniem modelu należy zdefiniować konfigurację wdrożenia. *Konfiguracja wdrożenia jest specyficzna dla obiektu docelowego obliczeń, który będzie obsługiwał usługę sieci web.* Na przykład podczas wdrażania modelu lokalnie, należy określić port, w którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wejścia. Służy do definiowania cech obiektu docelowego obliczeń, który będzie obsługiwał model i skrypt wejścia.

Może być również konieczne utworzenie zasobu obliczeniowego, jeśli na przykład nie masz jeszcze wystąpienia usługi Azure Kubernetes (AKS) skojarzonego z obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrożenia dla każdego obiektu docelowego obliczeń:

| Docelowy zasób obliczeniowy | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalny | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasy dla lokalnych, wystąpień kontenerów platformy Azure i usług `azureml.core.webservice`sieci web usługi AKS można importować z:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-ssl"></a>Zabezpieczanie wdrożeń za pomocą usługi SSL

Aby uzyskać więcej informacji na temat zabezpieczania wdrożenia usługi sieci web, zobacz [Zabezpieczanie usługi sieci web za pomocą ssl.](how-to-secure-web-service.md#enable)

### <a name="local-deployment"></a><a id="local"></a>Wdrożenie lokalne

Aby wdrożyć model lokalnie, musisz mieć docker zainstalowany na komputerze lokalnym.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację [localwebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć model przy użyciu interfejsu wiersza polecenia, należy użyć następującego polecenia. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz dokumentację [wdrażania modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Opis stanu usługi

Podczas wdrażania modelu może być widoczna zmiana stanu usługi podczas pełnego wdrażania.

W poniższej tabeli opisano różne stany usługi:

| Stan usługi sieci Web | Opis | Stan końcowy?
| ----- | ----- | ----- |
| Przejście | Usługa jest w trakcie wdrażania. | Nie |
| W niedobrej kondycji | Usługa została wdrożona, ale jest obecnie nieosiągalna.  | Nie |
| Niezdrówialne | Usługa nie może być wdrożona w tej chwili z powodu braku zasobów. | Nie |
| Niepowodzenie | Nie można wdrożyć usługi z powodu błędu lub awarii. | Tak |
| W dobrej kondycji | Usługa jest w dobrej kondycji i punkt końcowy jest dostępny. | Tak |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Usługa sieci web wystąpienia obliczeniowego (dev/test)

Zobacz [Wdrażanie modelu w wystąpieniu obliczeniowym usługi Azure Machine Learning.](how-to-deploy-local-container-notebook-vm.md)

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Wystąpienia kontenera platformy Azure (dev/test)

Zobacz [Wdrażanie w wystąpieniach kontenerów platformy Azure](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Usługa Azure Kubernetes (dev/test i produkcja)

Zobacz [Wdrażanie usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Testowanie A/B (kontrolowane wdrażanie)
Aby uzyskać więcej informacji, zobacz [Kontrolowane wdrażanie modeli ml.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Korzystaj z usług sieci Web

Każda wdrożona usługa sieci web zapewnia punkt końcowy REST, dzięki czemu można tworzyć aplikacje klienckie w dowolnym języku programowania.
Jeśli włączono uwierzytelnianie oparte na kluczu dla usługi, musisz podać klucz usługi jako token w nagłówku żądania.
Jeśli włączono uwierzytelnianie oparte na tokenie dla usługi, musisz podać token sieci Web JSON usługi Azure Machine Learning (JWT) jako token na nośnik w nagłówku żądania. 

Podstawową różnicą jest to, że **klucze są statyczne i mogą być generowane ręcznie,** a **tokeny muszą być odświeżane po wygaśnięciu.** Ujednolici oparte na kluczach jest obsługiwana dla wystąpienia kontenera platformy Azure i usługi Azure Kubernetes wdrożonych usług sieci web, a uszanie oparte na tokenach jest dostępne **tylko** dla wdrożeń usługi Azure Kubernetes. Aby uzyskać więcej informacji [i](how-to-setup-authentication.md#web-service-authentication) przykłady określonego kodu, zobacz instrukcje dotyczące uwierzytelniania.

> [!TIP]
> Można pobrać dokument JSON schematu po wdrożeniu usługi. Użyj [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) z wdrożonej usługi sieci web `service.swagger_uri`(na przykład ), aby uzyskać identyfikator URI do pliku Swagger lokalnej usługi sieci web.

### <a name="request-response-consumption"></a>Zużycie odpowiedzi na żądanie

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

Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji klienckich w celu korzystania z usług sieci web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (specyfikacja OpenAPI)

Jeśli użyto automatycznego generowania schematu z wdrożeniem, można uzyskać adres specyfikacji OpenAPI dla usługi przy użyciu [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Na przykład `print(service.swagger_uri)`.) Użyj żądania GET lub otwórz identyfikator URI w przeglądarce, aby pobrać specyfikację.

Następujący dokument JSON jest przykładem schematu (specyfikacja OpenAPI) wygenerowanego dla wdrożenia:

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

Aby uzyskać więcej informacji, zobacz [specyfikacja OpenAPI](https://swagger.io/specification/).

Dla narzędzia, które można tworzyć biblioteki klientów ze specyfikacji, zobacz [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Wnioskowanie wsadowe
Obiekty docelowe obliczeń usługi Azure Machine Learning są tworzone i zarządzane przez usługę Azure Machine Learning. Mogą one służyć do przewidywania partii z potoków usługi Azure Machine Learning.

Aby zapoznać się z instruktażem wnioskowania wsadowego za pomocą usługi Azure Machine Learning Compute, zobacz [Jak uruchomić prognozy partii.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Wnioskowanie o krawędzi IoT
Obsługa wdrażania na krawędzi jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Wdrażanie usługi Azure Machine Learning jako modułu usługi IoT Edge.](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)


## <a name="update-web-services"></a><a id="update"></a>Aktualizowanie usług sieci Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Ciągłe wdrażanie modeli

Modele można stale wdrażać przy użyciu rozszerzenia uczenia maszynowego dla [usługi Azure DevOps](https://azure.microsoft.com/services/devops/). Za pomocą rozszerzenia uczenia maszynowego dla usługi Azure DevOps wyzwolić potok wdrażania, gdy nowy model uczenia maszynowego jest zarejestrowany w obszarze roboczym usługi Azure Machine Learning.

1. Zarejestruj się w [usłudze Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), co umożliwia ciągłą integrację i dostarczanie aplikacji do dowolnej platformy lub chmury. (Należy zauważyć, że potoki platformy Azure nie są takie same jak [potoki usługi Machine Learning).](concept-ml-pipelines.md#compare)

1. [Tworzenie projektu devops platformy Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Zainstaluj [rozszerzenie uczenia maszynowego dla potoków platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Użyj połączeń usługi, aby skonfigurować połączenie jednostkowe usługi do obszaru roboczego usługi Azure Machine Learning, dzięki czemu można uzyskać dostęp do artefaktów. Przejdź do ustawień projektu, wybierz **pozycję Połączenia usługi**, a następnie wybierz pozycję Azure Resource **Manager**:

    [![Wybieranie usługi Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na liście **Poziom zakresu** wybierz pozycję **AzureMLWorkspace**, a następnie wprowadź pozostałe wartości:

    ![Wybieranie obszaru roboczego usługi AzureML](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Aby stale wdrażać model uczenia maszynowego przy użyciu usługi Azure Pipelines, w ramach potoków wybierz **wersję**. Dodaj nowy artefakt, a następnie wybierz artefakt **modelu usługi AzureML** i połączenie usługi utworzone wcześniej. Wybierz model i wersję, aby wyzwolić wdrożenie:

    [![Wybierz model usługi AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Włącz wyzwalacz modelu w artefakt modelu. Po włączeniu wyzwalacza, za każdym razem, gdy określona wersja (czyli najnowsza wersja) tego modelu jest rejestrowana w obszarze roboczym, potok wydania usługi Azure DevOps jest wyzwalany.

    [![Włączanie wyzwalacza modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Aby uzyskać więcej przykładowych projektów i przykładów, zobacz te przykładowe repozytoria w usłudze GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Pobieranie modelu
Jeśli chcesz pobrać model, aby użyć go we własnym środowisku wykonywania, możesz to zrobić za pomocą następujących poleceń SDK / CLI:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Cli:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Wersja zapoznawcza) Wdrożenie modelu bez kodu

Wdrożenie modelu bez kodu jest obecnie w wersji zapoznawczej i obsługuje następujące struktury uczenia maszynowego:

### <a name="tensorflow-savedmodel-format"></a>Format SavedModel tensorflow
Modele tensorflow muszą być zarejestrowane w **formacie SavedModel** do pracy z wdrożeniem modelu bez kodu.

Zapoznaj się [z tym linkiem,](https://www.tensorflow.org/guide/saved_model) aby uzyskać informacje na temat tworzenia SavedModel.

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

Rejestracja i wdrażanie modelu ONNX jest obsługiwana dla dowolnego wykresu wnioskowania ONNX. Kroki przetwarzania wstępnego i postprocesu nie są obecnie obsługiwane.

Oto przykład sposobu rejestrowania i wdrażania modelu MNIST ONNX:

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

Jeśli używasz Pytorch, [Eksportowanie modeli z PyTorch do ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) ma szczegółowe informacje na temat konwersji i ograniczeń. 

### <a name="scikit-learn-models"></a>Modele scikit-learn

Żadne wdrożenie modelu kodu nie jest obsługiwane dla wszystkich wbudowanych typów modeli scikit-learn.

Oto przykład jak zarejestrować i wdrożyć model sklearn bez dodatkowego kodu:

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

UWAGA: Te zależności są zawarte w wstępnie utworzonym kontenerze wnioskowania sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modele pakietów

W niektórych przypadkach można utworzyć obraz platformy Docker bez wdrażania modelu (jeśli na przykład planujesz [wdrożyć w usłudze Azure App Service).](how-to-deploy-app-service.md) Możesz też pobrać obraz i uruchomić go w lokalnej instalacji platformy Docker. Możesz nawet pobrać pliki używane do tworzenia obrazu, sprawdzić je, zmodyfikować i ręcznie utworzyć obraz.

Opakowanie modelu umożliwia wykonywanie tych czynności. Pakiety wszystkie zasoby potrzebne do hostowania modelu jako usługi sieci web i pozwala na pobranie w pełni zbudowany obraz platformy Docker lub pliki potrzebne do jego utworzenia. Istnieją dwa sposoby korzystania z opakowania modelu:

**Pobierz model w pakiecie:** Pobierz obraz platformy Docker zawierający model i inne pliki potrzebne do hostowania go jako usługi sieci web.

**Generowanie pliku dockerfile:** Pobierz plik dockerfile, model, skrypt wejścia i inne zasoby potrzebne do utworzenia obrazu platformy Docker. Następnie można sprawdzić pliki lub wprowadzić zmiany przed utworzeniem obrazu lokalnie.

Oba pakiety mogą służyć do uzyskania lokalnego obrazu platformy Docker.

> [!TIP]
> Tworzenie pakietu jest podobne do wdrażania modelu. Używasz zarejestrowanego modelu i konfiguracji wnioskowania.

> [!IMPORTANT]
> Aby pobrać w pełni zbudowany obraz lub utworzyć obraz lokalnie, musisz mieć zainstalowany [program Docker](https://www.docker.com) w środowisku programistycznym.

### <a name="download-a-packaged-model"></a>Pobieranie modelu w pakiecie

Poniższy przykład tworzy obraz, który jest zarejestrowany w rejestrze kontenerów platformy Azure dla obszaru roboczego:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po utworzeniu pakietu można `package.pull()` użyć do ściągania obrazu do lokalnego środowiska platformy Docker. Dane wyjściowe tego polecenia będą wyświetlane nazwę obrazu. Przykład: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po pobraniu modelu `docker images` użyj polecenia, aby wyświetlić listę obrazów lokalnych:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Aby uruchomić kontener lokalny na podstawie tego obrazu, użyj następującego polecenia, aby uruchomić nazwany kontener z powłoki lub wiersza polecenia. Zastąp `<imageid>` wartość identyfikatorem obrazu `docker images` zwróconym przez polecenie.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

To polecenie uruchamia najnowszą wersję `myimage`obrazu o nazwie . Mapuje port lokalny 6789 do portu w kontenerze, na którym nasłuchuje usługa internetowa (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po uruchomieniu kontenera można przesyłać `http://localhost:6789/score`żądania do .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generowanie pliku dockerfile i zależności

W poniższym przykładzie pokazano, jak pobrać dockerfile, modelu i innych zasobów potrzebnych do tworzenia obrazu lokalnie. Parametr `generate_dockerfile=True` wskazuje, że chcesz pliki, a nie w pełni zbudowany obraz.

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

Ten kod pobiera pliki potrzebne do utworzenia obrazu do `imagefiles` katalogu. Plik Dockerfile zawarte w zapisanych plików odwołuje się do obrazu podstawowego przechowywane w rejestrze kontenerów platformy Azure. Podczas tworzenia obrazu na lokalnej instalacji platformy Docker, należy użyć adresu, nazwy użytkownika i hasła do uwierzytelniania w rejestrze. Aby utworzyć obraz przy użyciu lokalnej instalacji platformy Docker, wykonaj następujące czynności:

1. W przypadku sesji powłoki lub wiersza polecenia użyj następującego polecenia, aby uwierzytelnić platformę Docker za pomocą rejestru kontenerów platformy Azure. Zamień `<address>`i `<username>` `<password>` wartości pobrane `package.get_container_registry()`przez .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Aby utworzyć obraz, użyj następującego polecenia. Zastąp `<imagefiles>` ścieżką katalogu, w którym `package.save()` zapisane są pliki.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    To polecenie ustawia nazwę `myimage`obrazu na .

Aby sprawdzić, czy obraz jest `docker images` zbudowany, użyj polecenia. Powinien być `myimage` widoczny na liście:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Aby uruchomić nowy kontener na podstawie tego obrazu, użyj następującego polecenia:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

To polecenie uruchamia najnowszą wersję `myimage`obrazu o nazwie . Mapuje port lokalny 6789 do portu w kontenerze, na którym nasłuchuje usługa internetowa (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po uruchomieniu kontenera można przesyłać `http://localhost:6789/score`żądania do .

### <a name="example-client-to-test-the-local-container"></a>Przykładowy klient do testowania kontenera lokalnego

Poniższy kod jest przykładem klienta języka Python, który może być używany z kontenerem:

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

Na przykład klienci w innych językach programowania zobacz [Korzystanie z modeli wdrożonych jako usługi sieci web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zatrzymywać kontener platformy Docker

Aby zatrzymać kontener, użyj następującego polecenia z innej powłoki lub wiersza polecenia:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wdrożoną usługę `service.delete()`sieci web, użyj programu .
Aby usunąć zarejestrowany model, użyj programu `model.delete()`.

Aby uzyskać więcej informacji, zobacz dokumentację witryn [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) i [Model.delete().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Tworzenie skryptów wpisu zaawansowanego

<a id="binary"></a>

### <a name="binary-data"></a>Dane binarne

Jeśli model akceptuje dane binarne, takie jak `score.py` obraz, należy zmodyfikować plik używany do wdrożenia, aby zaakceptować nieprzetworzone żądania HTTP. Aby zaakceptować nieprzetworzone dane, należy użyć `AMLRequest` klasy w skrypcie wejścia i dodać `@rawhttp` dekoratora do `run()` funkcji.

Oto `score.py` przykład, który akceptuje dane binarne:

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
> Klasa `AMLRequest` znajduje się `azureml.contrib` w obszarze nazw. Jednostki w tej przestrzeni nazw często się zmieniają, gdy pracujemy nad ulepszeniem usługi. Wszystko w tej przestrzeni nazw należy uznać za podgląd, który nie jest w pełni obsługiwany przez firmę Microsoft.
>
> Jeśli chcesz przetestować to w lokalnym środowisku programistycznym, można zainstalować składniki za pomocą następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowy przydział zasobów między źródłami (CORS)

Udostępnianie zasobów między źródłami to sposób zezwalania na żądania zasobów na stronie sieci Web z innej domeny. CORS działa za pośrednictwem nagłówków HTTP wysłanych z żądaniem klienta i zwróconych z odpowiedzią na usługę. Aby uzyskać więcej informacji na temat cors i prawidłowych nagłówków, zobacz [Udostępnianie zasobów między źródłami](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) w Wikipedii.

Aby skonfigurować wdrożenie modelu do obsługi `AMLResponse` usługi CORS, należy użyć klasy w skrypcie wprowadzania. Ta klasa umożliwia ustawienie nagłówków na obiekcie odpowiedzi.

Poniższy przykład `Access-Control-Allow-Origin` ustawia nagłówek odpowiedzi ze skryptu wpisu:

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
> Klasa `AMLResponse` znajduje się `azureml.contrib` w obszarze nazw. Jednostki w tej przestrzeni nazw często się zmieniają, gdy pracujemy nad ulepszeniem usługi. Wszystko w tej przestrzeni nazw należy uznać za podgląd, który nie jest w pełni obsługiwany przez firmę Microsoft.
>
> Jeśli chcesz przetestować to w lokalnym środowisku programistycznym, można zainstalować składniki za pomocą następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Bezpieczne usługi sieci Web usługi azure machine learning za pomocą ssl](how-to-secure-web-service.md)
* [Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy dla wdrożeń modelu](how-to-use-event-grid.md)

