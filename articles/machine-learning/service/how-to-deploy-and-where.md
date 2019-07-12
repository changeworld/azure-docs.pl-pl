---
title: Jak i gdzie umożliwia wdrażanie modeli
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak i gdzie wdrożyć swoje modele usługi Azure Machine Learning, w tym: Usługa Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge i programowalny bramy tablic.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: fb23e61142a639420d74c08e5a9a41324acab18b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706283"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Wdrażaj modele za pomocą usługi Azure Machine Learning

Dowiedz się, jak wdrożyć usługi uczenia maszynowego model jako usługę sieci web w chmurze platformy Azure lub na urządzeniach usługi IoT Edge. 

Przepływ pracy jest podobna, bez względu na to [gdzie wdrażanie](#target) modelu:

1. Należy zarejestrować model.
1. Przygotowanie do wdrożenia (Określ cel obliczenia zasobów, użycie)
1. Wdróż model obliczeniowego elementu docelowego.
1. Przetestuj wdrożony model, nazywany również usługę sieci web.

Aby uzyskać więcej informacji na temat pojęć, które są zaangażowane w przepływ pracy wdrażania, zobacz [zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Model. Jeśli nie masz trenowanego modelu, można użyć danego modelu i zależności pliki udostępniane w [w tym samouczku](https://aka.ms/azml-deploy-cloud).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), [zestawu SDK usługi Azure Machine Learning Python](https://aka.ms/aml-sdk), lub [rozszerzenia usługi Azure Machine Learning programu Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a> Zarejestruj swój model

Model zarejestrowany kontener logiczny dla jeden lub więcej plików, które tworzą model. Na przykład w przypadku modelu, który jest przechowywany w wielu plikach mogą je zarejestrować jako pojedynczego modelu w obszarze roboczym. Po rejestracji można, a następnie pobrać lub zarejestrowanego modelu wdrażania i otrzymywać wszystkie pliki, które zostały zarejestrowane.

Modele uczenia maszynowego są rejestrowane w obszarze roboczym usługi Azure Machine Learning. Model mogą pochodzić z usługi Azure Machine Learning lub mogą pochodzić z innej lokalizacji. W poniższych przykładach pokazano, jak zarejestrować modelu z pliku:

### <a name="register-a-model-from-an-experiment-run"></a>Zarejestruj model z uruchamianie eksperymentu

+ **Przykład Scikit-Learn, przy użyciu zestawu SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Aby dołączyć wiele plików rejestracji modelu, należy ustawić `model_path` do katalogu, który zawiera pliki.

+ **Przy użyciu interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Aby dołączyć wiele plików rejestracji modelu, należy ustawić `--asset-path` do katalogu, który zawiera pliki.

+ **Za pomocą programu VS Code**

  Zarejestruj modele przy użyciu dowolnego modelu pliki lub foldery z [programu VS Code](how-to-vscode-tools.md#deploy-and-manage-models) rozszerzenia.

### <a name="register-an-externally-created-model"></a>Zarejestruj model utworzone zewnętrznie

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Możesz zarejestrować model utworzone zewnętrznie, zapewniając **ścieżkę lokalną** do modelu. Możesz podać folder lub pojedynczy plik.

+ **Przykład ONNX za pomocą zestawu SDK języka Python:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Aby dołączyć wiele plików rejestracji modelu, należy ustawić `model_path` do katalogu, który zawiera pliki.

+ **Przy użyciu interfejsu wiersza polecenia**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Aby dołączyć wiele plików rejestracji modelu, należy ustawić `-p` do katalogu, który zawiera pliki.

**Szacowany czas**: Około 10 sekund.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [klasa modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami skonfigurowanych pod kątem poza usługi Azure Machine Learning, zobacz [wdrażanie istniejącego modelu](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Wybierz cel obliczenia

Następujące obliczeniowych elementów docelowych, lub zasoby obliczeniowe, mogą być używane do obsługi wdrożenia usługi sieci web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Przygotowywanie do wdrożenia

Aby wdrożyć jako usługę sieci web, należy utworzyć konfigurację wnioskowania (`InferenceConfig`) i konfiguracji wdrożenia. Wnioskowanie lub oceniania modelu, jest faza użycia wdrożony model do przewidywania najczęściej w danych produkcyjnych. W pliku config wnioskowania należy określić skrypty i zależności niezbędne do obsługi modelu. W konfiguracji wdrożenia, należy określić szczegóły dotyczące obsługi modelu w elemencie docelowym obliczeń.


### <a id="script"></a> 1. Definiowanie zależności & skryptów wpis, na których

Skrypt wejścia odbiera dane przesłane do wdrożonej usługi sieci web i przekazuje je do modelu. Następnie pobiera odpowiedź zwrócona przez model i zwraca go do klienta. **Skrypt jest specyficzny dla modelu**; należy zrozumieć, dane, które oczekuje modelu i zwraca.

Skrypt zawiera dwie funkcje, które ładują i uruchomić model:

* `init()`: Zazwyczaj ta funkcja ładuje modelu do obiektów globalnych. Ta funkcja jest uruchamiany tylko raz, po uruchomieniu kontenera platformy Docker dla usługi sieci web.

* `run(input_data)`: Ta funkcja wykorzystuje model do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe, uruchom zazwyczaj korzystają w ramach serializacji i deserializacji JSON. Może również współdziałać z nieprzetworzone dane binarne. Można przekształcać dane przed wysłaniem do modelu lub przed zwróceniem do klienta.

#### <a name="what-is-getmodelpath"></a>Co to jest get_model_path?

Po zarejestrowaniu modelu, można podać nazwę modelu używanym do zarządzania modelem w rejestrze. Możesz użyć tej nazwy z [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pobrać ścieżki plików modelu w lokalnym systemie plików. Jeśli zarejestrujesz się, folder lub zbiór plików, ten interfejs API zwraca ścieżkę do katalogu zawierającego te pliki.

Po zarejestrowaniu modelu, nadajesz mu nazwę, która odpowiada gdzie umieścić model lokalnie lub w trakcie wdrażania usługi.

Poniższym przykładzie zwraca ścieżkę do jednego pliku o nazwie `sklearn_mnist_model.pkl` (który został zarejestrowany z nazwą `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(Opcjonalnie) Automatyczne generowanie schematu struktury Swagger

Aby automatycznie wygenerować schematu dla usługi sieci web, podaj przykładowe dane wejściowe i/lub dane wyjściowe w konstruktora dla poszczególnych obiektów zdefiniowanego typu oraz typ i przykładowe są używane do automatycznie tworzony jest schemat. Usługa Azure Machine Learning utworzy [OpenAPI](https://swagger.io/docs/specification/about/) specyfikacji (Swagger) dla usługi sieci web podczas wdrażania.

Obecnie obsługiwane są następujące typy:

* `pandas`
* `numpy`
* `pyspark`
* Standardowa obiektu języka Python

Aby użyć generowanie schematu, dołącz `inference-schema` pakietu w pliku środowiska conda. W poniższym przykładzie użyto `[numpy-support]` ponieważ skrypt wejścia używa typ parametru numpy: 

#### <a name="example-dependencies-file"></a>Przykładowy plik zależności
Poniższego kodu YAML znajduje się przykład pliku zależności Conda wnioskowania.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Jeśli chcesz używać schematu automatycznego generowania skryptu wpis **musi** zaimportować `inference-schema` pakietów. 

Zdefiniuj wejściowe i wyjściowe przykładowe formaty w `input_sample` i `output_sample` zmiennych, które reprezentują formatów żądania i odpowiedzi usługi sieci web. Za pomocą tych przykładów w danych wejściowych i wyjściowych dekoratory funkcji na `run()` funkcji. Scikit-Dowiedz się, w poniższym przykładzie użyto generowanie schematu.

> [!TIP]
> Po wdrożeniu usługi, użyj `swagger_uri` właściwość służąca do pobierania dokumentów JSON schematu.

#### <a name="example-entry-script"></a>Przykładowy skrypt programu wpisu

Poniższy przykład pokazuje, jak akceptują i zwracają dane JSON:

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Przykładowy skrypt z danymi wejściowymi słownika (zużycie pomocy technicznej usługi Power BI)

Poniższy przykład ilustruje sposób definiowania danych wejściowych jako < klucz: wartość > słownika, przy użyciu ramki danych. Ta metoda jest obsługiwana, co umożliwia korzystanie z wdrożonej usługi sieci web w usłudze Power BI ([Dowiedz się więcej na temat sposobu korzystania z usługi sieci web w usłudze Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
Aby uzyskać więcej przykładowe skrypty zobacz następujące przykłady:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Ocenianie względem danych binarnych: [Jak używać usługi sieci web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Zdefiniuj swoje InferenceConfig

Konfiguracja wnioskowania opisuje sposób konfigurowania model do przewidywania przyszłych zdarzeń. Poniższy przykład pokazuje, jak utworzyć konfigurację wnioskowania. Ta konfiguracja określa środowisko wykonawcze, skrypt wejścia i (opcjonalnie) plikiem środowiska conda:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Aby uzyskać więcej informacji, zobacz [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) klasy odniesienia.

Aby uzyskać informacji na temat korzystania z niestandardowego obrazu platformy Docker przy użyciu konfiguracji wnioskowania, zobacz [sposób wdrażania modelu przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Przykład interfejsu wiersza polecenia InferenceConfig

Poniższy dokument JSON jest wnioskowania przykładowej konfiguracji do użycia z usługą machine learning interfejs wiersza polecenia:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

Następujące jednostki są prawidłowe w tym pliku:

* __entryScript__: Ścieżka do pliku lokalnego, który zawiera kod, aby uruchomić dla obrazu.
* __Środowisko uruchomieniowe__: Które środowisko uruchomieniowe na potrzeby obrazu. Bieżący obsługiwane środowiska uruchomieniowe są "spark-py" i "python".
* __condaFile__ (opcjonalnie): Ścieżka do pliku lokalnego, zawierający definicja środowiska conda, aby użyć obrazu.
* __extraDockerFileSteps__ (opcjonalnie): Ścieżka do pliku lokalnego, zawierający dodatkowe kroki platformy Docker, aby uruchomić podczas konfigurowania obrazu.
* __sourceDirectory__ (opcjonalnie): Ścieżka do folderów, która zawiera wszystkie pliki do utworzenia obrazu.
* __enableGpu__ (opcjonalnie): Umożliwia określenie, czy włączyć GPU obsługuje na obrazie. Obraz procesora GPU musi być używana w Microsoft Azure Services, takich jak usługi Azure Container Instances, obliczeniowego usługi Azure Machine Learning, Azure Virtual Machines i Azure Kubernetes Service. Wartość domyślna to False.
* __baseImage__ (opcjonalnie): Obraz niestandardowy do użycia jako obraz podstawowy. Jeśli podano nie obraz podstawowy, obrazu podstawowego zostaną użyte podstawie wylogować się z podanych parametrów środowiska uruchomieniowego.
* __baseImageRegistry__ (opcjonalnie): Rejestr obrazu, który zawiera obraz podstawowy.
* __cudaVersion__ (opcjonalnie): Wersja architektury CUDA, aby zainstalować dla obrazów wymagających wsparciem procesora GPU. Obraz procesora GPU musi być używana w Microsoft Azure Services, takich jak usługi Azure Container Instances, obliczeniowego usługi Azure Machine Learning, Azure Virtual Machines i Azure Kubernetes Service. Obsługiwane wersje to 9.0, 9.1 i 10.0. Jeśli ustawiono wartość "enable_gpu", wartość domyślna to "9.1".

Te jednostki są mapowane do parametrów [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) klasy.

Te następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracji zawiera następujące elementy:

* Katalog, który zawiera zasoby potrzebne do wnioskowania
* Czy ten model wymaga języka Python
* [Skrypt wejścia](#script), które jest używane do obsługi żądań sieci web wysyłanych do wdrożonej usługi
* Plik conda, opisujący pakiety Python niezbędne do wnioskowania

Aby uzyskać informacji na temat korzystania z niestandardowego obrazu platformy Docker przy użyciu konfiguracji wnioskowania, zobacz [sposób wdrażania modelu przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiowanie konfiguracji wdrożenia

Przed wdrożeniem, należy zdefiniować konfiguracji wdrożenia. Konfiguracja wdrożenia jest charakterystyczne dla docelowej obliczeń, który będzie hostował usługę sieci web. Na przykład w przypadku wdrażania lokalnie należy określić port, na którym usługa akceptuje żądania.

Również może być konieczne utworzenie zasobów obliczeniowych. Na przykład jeśli to zrobisz, nie jest jeszcze w usłudze Azure Kubernetes Service skojarzony z obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrażania dla każdego obliczeniowych elementów docelowych:

| Obliczeniowego elementu docelowego | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalny | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Wystąpienie kontenera platformy Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Poniższe sekcje pokazują, jak utworzyć konfigurację wdrożenia, a następnie używania jej do wdrażania usługi sieci web.

### <a name="optional-profile-your-model"></a>Opcjonalnie: Profil modelu
Przed wdrożeniem modelu w postaci usługi, można profilować go do ustalenia optymalnej procesora CPU i wymagania dotyczące pamięci przy użyciu zestawu SDK lub interfejsu wiersza polecenia.  Model wyniki profilowania są emitowane jako `Run` obiektu. Pełne szczegóły [schematu modelu profilu można znaleźć w dokumentacji interfejsu API](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

Dowiedz się więcej o [jak profil modelu przy użyciu zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)

## <a name="deploy-to-target"></a>Wdrażanie na docelowych

### <a id="local"></a> Wdrożenie lokalne

Aby wdrożyć lokalnie, musisz mieć **zainstalowana platforma Docker** na komputerze lokalnym.

+ **Korzystanie z zestawu SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Przy użyciu interfejsu wiersza polecenia**

    Aby wdrożyć przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `mymodel:1` o nazwie i wersji zarejestrowany modelu:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Wpisy w `deploymentconfig.json` planu dokumentu do parametrów [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON i parametrów dla metody:

    | Jednostki JSON | Parametr metody | Opis |
    | ----- | ----- | ----- |
    | `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla wartości lokalnych, wartość musi być `local`. |
    | `port` | `port` | Port lokalny, na którym chcesz udostępnić punktu końcowego HTTP usługi. |

    Następujący kod JSON jest przykładowa Konfiguracja wdrożenia do użycia przy użyciu interfejsu wiersza polecenia:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a> Usługa Azure Container Instances (DEVTEST)

Użyj usługi Azure Container Instances dla wdrażając swoje modele jako usługi sieci web, jeśli jeden lub więcej z następujących warunków jest spełniony:
- Musisz szybko wdrażać i weryfikacja modelu.
- W przypadku testowania modelu, który jest w fazie projektowania. 

Aby wyświetlić przydziału i regionu dostępność usługi ACI, zobacz [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artykułu.

+ **Korzystanie z zestawu SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Przy użyciu interfejsu wiersza polecenia**

    Aby wdrożyć przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `mymodel:1` z nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` o nazwie, aby zapewnić tę usługę:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    Wpisy w `deploymentconfig.json` planu dokumentu do parametrów [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON i parametrów dla metody:

    | Jednostki JSON | Parametr metody | Opis |
    | ----- | ----- | ----- |
    | `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla usługi ACI, wartość musi być `ACI`. |
    | `containerResourceRequirements` | Nie dotyczy | Zawiera elementy konfiguracji dla procesora CPU i pamięci przydzielonej dla kontenera. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia dla tej usługi sieci web. Ustawienia domyślne, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci web. Domyślnie `0.5` |
    | `location` | `location` | Region platformy Azure do wdrożenia tej usługi internetowej, aby. Jeśli nie określono obszaru roboczego, lokalizacja będzie używana. Szczegółowe informacje na temat dostępnych regionów można znaleźć tutaj: [Regiony usługi ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | Czy należy włączyć uwierzytelnianie dla tej usługi internetowej. Wartość domyślna to False |
    | `sslEnabled` | `ssl_enabled` | Czy należy włączyć protokół SSL dla tej usługi internetowej. Wartość domyślna to False. |
    | `appInsightsEnabled` | `enable_app_insights` | Czy należy włączyć usługi Application Insights dla tej usługi internetowej. Wartość domyślna to False |
    | `sslCertificate` | `ssl_cert_pem_file` | Plik certyfikatu, wymagane, jeśli jest włączony protokół SSL |
    | `sslKey` | `ssl_key_pem_file` | Plik klucza, wymagane, jeśli jest włączony protokół SSL |
    | `cname` | `ssl_cname` | Rekord cname dla Jeśli jest włączony protokół SSL |
    | `dnsNameLabel` | `dns_name_label` | Etykieta nazwy dns dla punktu końcowego oceniania. Jeśli nie określono etykietę nazwy dns unikatowych zostanie wygenerowany dla punktu końcowego oceniania. |

    Następujący kod JSON jest przykładowa Konfiguracja wdrożenia do użycia przy użyciu interfejsu wiersza polecenia:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **Za pomocą programu VS Code**

  Aby [Wdróż swoje modele, za pomocą programu VS Code](how-to-vscode-tools.md#deploy-and-manage-models) nie jest potrzebny do utworzenia kontenera usługi ACI do testowania z wyprzedzeniem, ponieważ kontenerów usługi ACI są tworzone na bieżąco.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) i [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) klasy.

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST i produkcja)

Można użyć istniejącego klastra AKS lub utworzyć nowe konto, przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia lub witryny Azure portal.

<a id="deploy-aks"></a>

Jeśli masz już klaster AKS, dołączony, można wdrożyć do niego. Jeśli jeszcze nie utworzono, lub dołączone klaster AKS, wykonaj procedurę <a href="#create-attach-aks">Utwórz nowy klaster AKS</a>.

+ **Korzystanie z zestawu SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Przy użyciu interfejsu wiersza polecenia**

    Aby wdrożyć przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `myaks` o nazwie AKS obliczeniowego elementu docelowego. Zastąp `mymodel:1` z nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` o nazwie, aby zapewnić tę usługę:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Wpisy w `deploymentconfig.json` planu dokumentu do parametrów [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON i parametrów dla metody:

    | Jednostki JSON | Parametr metody | Opis |
    | ----- | ----- | ----- |
    | `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla usługi AKS, wartość musi być `aks`. |
    | `autoScaler` | Nie dotyczy | Zawiera elementy konfiguracji dotyczące automatycznego skalowania. Zobacz tabelę skalowania automatycznego. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Czy należy włączyć Skalowanie automatyczne usługi sieci web. Jeśli `numReplicas`  =  `0`, `True`; w przeciwnym razie `False`. |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimalna liczba kontenerów do użycia podczas automatycznego skalowania tej usługi sieci web. Domyślnie, `1`. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maksymalną liczbę kontenerów do użycia podczas automatycznego skalowania tej usługi sieci web. Domyślnie, `10`. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak często skalowania automatycznego próby skalowania tej usługi sieci web. Domyślnie, `1`. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Wykorzystanie docelowe (w procentach 100), skalowania automatycznego ma podejmować próbę zachowania tej usługi sieci web. Domyślnie, `70`. |
    | `dataCollection` | Nie dotyczy | Zawiera elementy konfiguracji do zbierania danych. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Czy należy włączyć zbieranie danych modelu dla usługi sieci web. Domyślnie, `False`. |
    | `authEnabled` | `auth_enabled` | Czy należy włączyć uwierzytelnianie usługi sieci web. Domyślnie, `True`. |
    | `containerResourceRequirements` | Nie dotyczy | Zawiera elementy konfiguracji dla procesora CPU i pamięci przydzielonej dla kontenera. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia dla tej usługi sieci web. Ustawienia domyślne, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci web. Domyślnie `0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Czy należy włączyć rejestrowanie usługi Application Insights dla usługi sieci web. Domyślnie, `False`. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | Wartość limitu czasu wymuszenia do oceniania wywołania usługi sieci web. Domyślnie, `60000`. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maksymalna liczba równoczesnych żądań na węzeł dla tej usługi sieci web. Domyślnie, `1`. |
    | `maxQueueWaitMs` | `max_request_wait_time` | Maksymalny czas żądania, pozostanie na te kolejki (w milisekundach) przed 503 zwracany jest błąd. Domyślnie, `500`. |
    | `numReplicas` | `num_replicas` | Liczba kontenerów można przydzielić dla tej usługi sieci web. Brak wartości domyślnej. Jeśli ten parametr nie jest ustawiona, skalowania automatycznego jest domyślnie włączona. |
    | `keys` | Nie dotyczy | Zawiera elementy konfiguracji dla kluczy. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | Klucz uwierzytelniania podstawowego dla tej usługi internetowej |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | Klucz uwierzytelniania pomocniczego na potrzeby tej usługi internetowej |
    | `gpuCores` | `gpu_cores` | Liczba rdzeni procesora GPU, aby przydzielić dla tej usługi internetowej. Domyślna wartość wynosi 1. |
    | `livenessProbeRequirements` | Nie dotyczy | Zawiera elementy konfiguracji dla wymagań sondy żywotności. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak często (w sekundach) do wykonywania sondy żywotności. Domyślnie to 10 sekund. Minimalna wartość to 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Liczba sekund po rozpoczęciu kontenera, zanim sondy żywotności są inicjowane. Wartość domyślna to 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Liczba sekund, po upływie których sondy żywotności upłynie limit czasu. Wartość domyślna to 2 sekundy. Minimalna wartość to 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Minimalna kolejnych sukcesów dla sondy żywotności zostały uznane za pomyślne po o nie powiodło się. Wartość domyślna to 1. Minimalna wartość to 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | Po uruchomieniu zasobnik sonda żywotności nie powiedzie się, Kubernetes podejmie próbę failureThreshold czasy przed rezygnacją. Wartość domyślna to 3. Minimalna wartość to 1. |
    | `namespace` | `namespace` | Przestrzeń nazw platformy Kubernetes, usługi sieci Web jest wdrażany w. Do 63 małe znaki alfanumeryczne ("" – "z", "0" — "9") i łącznik ("-") znaków. Pierwszy i ostatni znak nie może mieć łączników. |

    Następujący kod JSON jest przykładowa Konfiguracja wdrożenia do użycia przy użyciu interfejsu wiersza polecenia:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **Za pomocą programu VS Code**

  Możesz również [wdrażanie w usłudze AKS za pośrednictwem rozszerzenia programu VS Code](how-to-vscode-tools.md#deploy-and-manage-models), ale należy wcześniej skonfigurować klastry usługi AKS.

Dowiedz się więcej na temat wdrażania usługi AKS i skalowanie automatyczne w [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) odwołania.

#### Utwórz nowy klaster usługi AKS<a id="create-attach-aks"></a>
**Szacowany czas**: Około 20 minut.

Tworzenie i dołączanie klaster AKS jest jeden raz przetwarzania dla obszaru roboczego. Można ponownie użyć klastra dla wielu wdrożeń. Jeśli usuniesz, klaster lub grupę zasobów, która ją zawiera, należy utworzyć nowy klaster przy następnym zajdzie potrzeba wdrożenia. Może mieć wielu klastrów usługi AKS, dołączone do obszaru roboczego.

Jeśli chcesz utworzyć klaster usługi AKS do tworzenia, weryfikacji i testowania, musisz ustawić `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` przy użyciu [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Klaster, który został utworzony za pomocą tego ustawienia będzie mieć tylko jeden węzeł.

> [!IMPORTANT]
> Ustawienie `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` tworzy klaster usługi AKS, który nie jest przeznaczony do obsługi ruchu w środowisku produkcyjnym. Wnioskowanie o czas może być dłuższy niż w klastrze, który został utworzony w środowisku produkcyjnym. Odporność na uszkodzenia również nie jest gwarantowana w przypadku klastrów i testowania aplikacji.
>
> Zaleca się, że klastry utworzone na potrzeby tworzenia i testowania Użyj co najmniej dwa procesory wirtualne.

Poniższy przykład przedstawia sposób tworzenia nowego klastra Azure Kubernetes Service:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Aby uzyskać więcej informacji na temat tworzenia klastra usługi AKS w taki sposób, poza zestawem SDK usługi Azure Machine Learning zobacz następujące artykuły:
* [Tworzenie klastra AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Tworzenie klastra AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Aby uzyskać więcej informacji na temat `cluster_purpose` parametrów, zobacz [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) odwołania.

> [!IMPORTANT]
> Aby uzyskać [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), w przypadku wybrania wartości niestandardowe agent_count i vm_size, a następnie należy upewnić się, że agent_count pomnożona przez vm_size jest większa lub równa 12 procesorów wirtualnych. Na przykład jeśli używasz vm_size "Maszyna wirtualna Standard_D3_v2", która ma 4 procesory wirtualne, następnie należy wybrać agent_count 3 lub większą.
>
> Zestaw SDK usługi Azure Machine Learning nie zapewnia obsługi skalowanie klastra usługi AKS. Aby skalować węzły w klastrze, należy użyć interfejsu użytkownika dla klastra usługi AKS w witrynie Azure portal. Można zmienić tylko liczby węzłów, a nie rozmiar maszyny Wirtualnej klastra.

#### <a name="attach-an-existing-aks-cluster"></a>Dołączanie istniejącego klastra AKS
**Szacowany czas:** Mniej więcej 5 minut.

Jeśli masz już klaster AKS w subskrypcji platformy Azure i jest wersja 1.12. ##, służy do wdrażania obrazu systemu.

> [!WARNING]
> Podczas podłączania klaster AKS do obszaru roboczego, można zdefiniować sposób korzystania z klastra przez ustawienie `cluster_purpose` parametru.
>
> Jeśli nie ustawisz `cluster_purpose` parametru lub ustawienie `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, a następnie klaster musi mieć co najmniej 12 procesorów wirtualnych dostępne.
>
> Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, a następnie klastra musi mieć 12 procesorów wirtualnych. Jednak klastra, który jest skonfigurowany do tworzenia i testowania nie będzie odpowiednia dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania.

Poniższy kod przedstawia sposób dołączania istniejącej 1.12 AKS. ## klastra do swojego obszaru roboczego:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Aby uzyskać więcej informacji na temat `attack_configuration()`, zobacz [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) odwołania.

Aby uzyskać więcej informacji na temat `cluster_purpose` parametrów, zobacz [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) odwołania.

## <a name="consume-web-services"></a>Korzystaj z usług sieci Web

Każdej wdrożonej usługi sieci web udostępnia interfejs API REST, aby można było utworzyć aplikacje klienta w różnych językach programowania. Po włączeniu uwierzytelniania dla usługi, musisz podać klucz usługi jako token w nagłówku żądania.

### <a name="request-response-consumption"></a>Użycie odpowiedź na żądanie

Oto przykład sposobu wywoływania usługi w języka Python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji do używania usług sieci Web klienta](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Wnioskowanie usługi Batch
Cele obliczeniowego usługi Machine Learning platformy Azure są tworzone i zarządzane przez usługę Azure Machine Learning. Służy do prognozowania usługi batch z potoków uczenia maszynowego Azure.

Aby zapoznać się z przewodnikiem wnioskowania usługi batch przy użyciu obliczeniowego usługi Azure Machine Learning, przeczytaj [sposobu uruchamiania prognoz usługi Batch](how-to-run-batch-predictions.md) artykułu.

### <a id="iotedge"></a> Wnioskowanie usługi IoT Edge
Obsługa wdrażania na urządzeniach brzegowych jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrożenia usługi Azure Machine Learning jako moduł usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) artykułu.


## <a id="update"></a> Aktualizacja usług sieci web

Podczas tworzenia nowego modelu, należy ręcznie zaktualizować wszystkich usług, które chcesz użyć nowego modelu. Aby zaktualizować usługę sieci web, użyj `update` metody. Poniższy kod pokazuje, jak zaktualizować usługę sieci web, aby użyć nowego modelu:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="continuous-model-deployment"></a>Wdrożenie modelu ciągłego 

Umożliwia ciągłe wdrażanie modeli za pomocą rozszerzenia dla usługi Machine Learning [DevOps platformy Azure](https://azure.microsoft.com/services/devops/). Za pomocą rozszerzenia usługi Machine Learning dla metodyki DevOps platformy Azure, możesz wyzwolić potok wdrażania po zarejestrowaniu nowego modelu uczenia maszynowego w obszarze roboczym usługi Azure Machine Learning. 

1. Należy zasubskrybować [potoki Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), co sprawia, że ciągłą integrację i dostarczanie aplikacji na dowolnej platformie/any w chmurze możliwe. Potoki usługi Azure [różni się od potokach uczenia Maszynowego](concept-ml-pipelines.md#compare). 

1. [Utwórz projekt DevOps platformy Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Zainstaluj [rozszerzenie potoki usługi Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Użyj __obsługują połączenia__ skonfigurować jednostki połączenia usługi do swojego obszaru roboczego usługi Azure Machine Learning, aby dostęp do wszystkich artefaktów. Przejdź do obszaru Ustawienia projektu, kliknąć połączenia usługi i wybierz usługi Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Zdefiniuj AzureMLWorkspace jako __określić zakres poziomu__ i wypełniając kolejne parametry.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Następnie, aby stale wdrożyć model uczenia maszynowego przy użyciu potoków usługi Azure, w obszarze potoków wybierz __wersji__. Dodawanie nowych artefaktu, wybierz artefakt modelu uczenia maszynowego Azure i połączenia z usługą, który został utworzony w poprzednim kroku. Wybierz model i wersję, aby wyzwolić wdrożenie. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Włącz wyzwalacz modelu usługi artefaktu modelu. Dzięki włączeniu wyzwalacza, każdym razem, gdy określona wersja (tj.) Najnowsza wersja) tego modelu Zarejestruj się w obszarze roboczym, zostanie wywołany potok wydań DevOps platformy Azure. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Przykładowe projekty i przykłady, zapoznaj się z [repozytorium MLOps](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.
Aby usunąć zarejestrowanego modelu, użyj `model.delete()`.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), i [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Następne kroki
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrożenia](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj swoje modele usługi Azure Machine Learning z usługą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)

