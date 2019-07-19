---
title: Korzystanie z MLflow z usługą Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Rejestruj metryki i artefakty oraz Wdrażaj modele w środowisku produkcyjnym za pomocą MLflow z usługą Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2030365cd78480c25e224edfea9e395aafa6661c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227917"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-service-preview"></a>Śledzenie metryk i wdrażanie modeli za pomocą usług MLflow i Azure Machine Learning (wersja zapoznawcza)

W tym artykule pokazano, jak włączyć śledzenie identyfikatorów URI MLflow i API rejestrowania, wspólnie znane jako [śledzenie MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), za pomocą usługi Azure Machine Learning. Dzięki temu można:

+ Śledź i Rejestruj metryki eksperymentu i artefakty w [obszarze roboczym usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Jeśli już używasz śledzenia MLflow do eksperymentów, obszar roboczy zapewnia scentralizowaną, bezpieczną i skalowalną lokalizację do przechowywania metryk i modeli szkoleniowych.

+ Wdróż eksperymenty MLflow jako usługę sieci Web Azure Machine Learning. Wdrażając jako usługę sieci Web, można zastosować funkcje monitorowania Azure Machine Learning i wykrywania dryfowania danych do modeli produkcyjnych. 

[MLflow](https://www.mlflow.org) to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow jest składnikiem MLflow, które rejestruje i śledzi przebiegi szkoleniowe i artefakty modelu, niezależnie od środowiska eksperymentu — lokalnie, na maszynie wirtualnej, zdalnego klastra obliczeniowego, nawet na Azure Databricks.

![mlflow z diagramem usługi Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porównanie klientów MLflow i Azure Machine Learning

 Poniższa tabela zawiera podsumowanie różnych klientów, którzy mogą korzystać z usługi Azure Machine Learning i ich funkcji.

 Śledzenie MLflow oferuje funkcje rejestrowania metryk i magazynu artefaktów, które są dostępne tylko w innym przypadku za pośrednictwem [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | MLflow śledzenie & | Azure Machine Learning <br> Zestaw SDK dla języka Python |  Azure Machine Learning <br> Interfejs wiersza polecenia | Azure Portal|
|-|-|-|-|-|-|
| Zarządzanie obszarem roboczym |   | ✓ | ✓ | ✓ |
| Korzystanie z magazynów danych  |   | ✓ | ✓ | |
| Metryki dzienników      | ✓ | ✓ |   | |
| Przekaż artefakty | ✓ | ✓ |   | |
| Wyświetlanie metryk     | ✓ | ✓ | ✓ | ✓ |
| Zarządzanie obliczeniami   |   | ✓ | ✓ | ✓ |
| Wdrażanie modeli    | ✓ | ✓ | ✓ | ✓ |
|Monitorowanie wydajności modelu||✓|  |   |
| Wykrywanie dryfu danych |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Zainstaluj Azure Machine Learning Python SDK na komputerze lokalnym i utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md#sdk). Zestaw SDK zapewnia łączność z usługą MLflow w celu uzyskania dostępu do obszaru roboczego.

## <a name="track-experiment-runs"></a>Śledź uruchomienia eksperymentów

Śledzenie MLflow za pomocą usługi Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z lokalnego i zdalnego uruchomienia w obszarze roboczym Azure Machine Learning.

### <a name="local-runs"></a>Lokalne uruchomienia

Zainstaluj pakiet `azureml-contrib-run` , aby używać śledzenia MLflow z Azure Machine Learningami na eksperymentach lokalnie uruchamianych w Jupyter notebook lub edytorze kodu.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Przestrzeń nazw Azure. contrib często zmienia się, ponieważ usprawniamy działanie usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

Zaimportuj [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasy i,abyuzyskaćdostępdośledzeniaidentyfikatorówURIMLflowiskonfigurowaćobszarroboczy.`mlflow`

W poniższym kodzie `get_mlflow_tracking_uri()` Metoda przypisuje unikatowy adres URI śledzenia do obszaru roboczego, `ws`a `set_tracking_uri()` następnie wskazuje na ten adres identyfikator URI śledzenia MLflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identyfikator URI śledzenia jest prawidłowy do godziny lub mniej. Jeśli ponownie uruchomisz skrypt po pewnym czasie bezczynności, użyj interfejsu API get_mlflow_tracking_uri, aby uzyskać nowy identyfikator URI.

Ustaw nazwę eksperymentu MLflow z `set_experiment()` i zacznij korzystać z szkolenia w `start_run()`systemie. Następnie użyj `log_metric()` , aby aktywować interfejs API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu szkoleniowego.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

### <a name="remote-runs"></a>Zdalne uruchomienia

Zdalne uruchomienia umożliwiają uczenie modeli przy większej liczbie zaawansowanych obliczeń, takich jak maszyny wirtualne obsługujące procesor GPU lub klastry środowisko obliczeniowe usługi Machine Learning. Zapoznaj się z tematem [Konfigurowanie elementów docelowych obliczeń dla szkolenia modeli](how-to-set-up-training-targets.md) , aby poznać różne opcje obliczeń.

Skonfiguruj środowisko uruchomieniowe obliczeniowe i szkoleniowe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) za pomocą klasy. Dołącz `mlflow` i `azure-contrib-run` [PIPpakiety`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) w sekcji środowiska. Następnie Utwórz [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) z użyciem obliczeń zdalnych jako element docelowy obliczeń.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

W skrypcie szkoleniowym zaimportuj `mlflow` , aby korzystać z interfejsów API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

W przypadku konfiguracji przebiegu obliczeń i szkoleń Użyj `Experiment.submit('train.py')` metody do przesyłania przebiegu. Spowoduje to automatyczne ustawienie identyfikatora URI śledzenia MLflow i skierowanie rejestrowania z MLflow do obszaru roboczego.

```Python
run = exp.submit(src)
```

### <a name="mlflow-with-azure-databricks-runs"></a>MLflow z uruchomionymi Azure Databricks

Aby uruchamiać eksperymenty Mlflow z Azure Databricks, musisz najpierw utworzyć [Azure Databricks obszar roboczy i klaster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Upewnij się, że w klastrze została zainstalowana Biblioteka usługi *Azure mlflow* z poziomu programu PyPi, aby zapewnić, że klaster ma dostęp do wymaganych funkcji i klas.

#### <a name="install-libraries"></a>Zainstaluj biblioteki

Aby zainstalować biblioteki w klastrze, przejdź do karty **biblioteki** , a następnie kliknij pozycję **Instaluj nowe** .

 ![mlflow z diagramem usługi Azure Machine Learning](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

W polu **pakiet** wpisz polecenie Azure-mlflow, a następnie kliknij przycisk Instaluj. Powtórz ten krok w razie potrzeby, aby zainstalować inne dodatkowe pakiety w klastrze dla eksperymentu.

 ![mlflow z diagramem usługi Azure Machine Learning](media/how-to-use-mlflow/install-libraries.png)

#### <a name="notebook-and-workspace-set-up"></a>Konfiguracja notesu i obszaru roboczego

Po skonfigurowaniu klastra zaimportuj Notes eksperymentu, otwórz go i Dołącz do niego klaster.

Poniższy kod powinien znajdować się w notesie eksperymentu. Spowoduje to pobranie szczegółów subskrypcji platformy Azure w celu utworzenia wystąpienia Twojego obszaru roboczego. Przyjęto założenie, że masz istniejącą grupę zasobów i obszar roboczy Azure Machine Learning, w przeciwnym razie można [je utworzyć](setup-create-workspace.md#portal). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

```
#### <a name="set-mlflow-tracking-uri"></a>Ustaw identyfikator URI śledzenia MLflow
Po utworzeniu wystąpienia obszaru roboczego ustaw identyfikator URI śledzenia MLflow. Dzięki temu można połączyć śledzenie MLflow z obszarem roboczym Azure Machine Learning. Po wykonaniu tych badań wszystkie eksperymenty będą gruntować w usłudze Managed Azure Machine Learning śledzenie.

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

W skrypcie szkoleniowym zaimportuj mlflow do używania interfejsów API rejestrowania MLflow i Rozpocznij rejestrowanie metryk uruchamiania. Poniższy przykład rejestruje metrykę utraty epoki. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Wyświetlanie metryk i artefaktów w obszarze roboczym

Metryki i artefakty z rejestrowania MLflow są przechowywane w Twoim obszarze roboczym. Aby wyświetlić je w dowolnym momencie, przejdź do obszaru roboczego i Znajdź eksperyment według nazwy na [Azure Portal](https://portal.azure.com) lub przez uruchomienie poniższego kodu. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Wdrażanie modeli MLflow jako usługi sieci Web

Wdrożenie eksperymentów MLflow jako usługi sieci Web Azure Machine Learning pozwala korzystać z funkcji zarządzania modelami Azure Machine Learning i wykrywania dryfowania danych oraz stosować je do modeli produkcyjnych.

![mlflow z diagramem usługi Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Rejestruj model
Przed wdrożeniem należy upewnić się, że model jest zapisany, aby można było odwołać się do niego i lokalizacji ścieżki dla wdrożenia. W skrypcie szkoleniowym powinien istnieć kod podobny do następującego: [mlflow. skryptu sklearn. log _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , który zapisuje model do określonego katalogu danych wyjściowych. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Uwzględnij `conda_env` parametr, aby przekazać reprezentację słownika zależności i środowiska, w których powinien być uruchamiany ten model.

### <a name="retrieve-model-from-previous-run"></a>Pobierz model z poprzedniego przebiegu

Aby można było pobrać żądany przebieg, wymagany jest identyfikator przebiegu i ścieżka w historii przebiegów, gdzie model został zapisany. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Tworzenie obrazu platformy Docker

`mlflow.azureml.build_image()` Funkcja kompiluje obraz platformy Docker z zapisanego modelu w sposób oparty na architekturze. Automatycznie tworzy kod otoki inferencing specyficzny dla platformy i określa zależności pakietów. Określ ścieżkę modelu, obszar roboczy, identyfikator przebiegu i inne parametry.

W poniższym kodzie tworzymy obraz platformy Docker przy użyciu przebiegu *:/< Run. id >/model* jako ścieżkę model_uri dla eksperymentu Scikit.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Tworzenie obrazu platformy Docker może potrwać kilka minut. 

### <a name="deploy-the-docker-image"></a>Wdrażanie obrazu platformy Docker 

Po utworzeniu obrazu Użyj zestawu SDK Azure Machine Learning, aby wdrożyć obraz jako usługę sieci Web.

Najpierw określ konfigurację wdrożenia. Usługa Azure Container Instance (ACI) to odpowiedni wybór dla szybkiego wdrożenia w ramach tworzenia i testowania, natomiast w przypadku skalowalnych wdrożeń produkcyjnych usługi Kubernetes (AKS) są odpowiednie.

#### <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI

Skonfiguruj konfigurację wdrożenia przy użyciu metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Możesz również dodać tagi i opisy, aby pomóc w śledzeniu usługi sieci Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Następnie wdróż obraz przy Azure Machine Learning użyciu metody [deploy_from_image () zestawu SDK ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) . 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć aplikację w usłudze AKS, należy utworzyć klaster AKS i przenieść go na obraz platformy Docker, który ma zostać wdrożony. W tym przykładzie przeniesiemy wcześniej utworzony obraz z naszego wdrożenia ACI.

Aby pobrać obraz z poprzedniego wdrożenia ACI, korzystamy z klasy [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) . 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Utwórz AKS obliczeń może upłynąć 20-25 minut, aby utworzyć nowy klaster

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Skonfiguruj konfigurację wdrożenia przy użyciu metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Możesz również dodać tagi i opisy, aby pomóc w śledzeniu usługi sieci Web.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Następnie wdróż obraz przy Azure Machine Learning użyciu metody [deploy_from_image () zestawu SDK ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) . 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

Wdrożenie usługi może potrwać kilka minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz używania zarejestrowanych metryk i artefaktów w obszarze roboczym, możliwość ich usunięcia osobno jest obecnie niedostępna. Zamiast tego należy usunąć grupę zasobów zawierającą konto magazynu i obszar roboczy, dzięki czemu nie zostaną naliczone żadne opłaty:

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.

   ![Usuwanie w witrynie Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. Wybierz utworzoną grupę zasobów z listy.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.


## <a name="example-notebooks"></a>Przykład notesów

[MLflow z usługami Azure ml notesy](https://aka.ms/azureml-mlflow-examples) pokazują i rozszerzają w oparciu o koncepcje przedstawione w tym artykule.

## <a name="next-steps"></a>Następne kroki
* [Zarządzaj modelami](concept-model-management-and-deployment.md).
* Monitoruj modele produkcyjne pod kątem [dryfowania danych](how-to-monitor-data-drift.md).
