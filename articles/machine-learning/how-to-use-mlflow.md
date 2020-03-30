---
title: Śledzenie przepływów ML dla eksperymentów w ml
titleSuffix: Azure Machine Learning
description: Skonfiguruj przepływ MLflow za pomocą usługi Azure Machine Learning, aby rejestrować metryki i artefakty z modeli uczenia maszynowego utworzonych w klastrach Databricks, środowisku lokalnym lub środowisku maszyn wirtualnych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983702"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Śledzenie metryk modeli za pomocą mlflow i usługi Azure Machine Learning (wersja zapoznawcza)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak włączyć śledzenia MLflow identyfikator URI i rejestrowania interfejsu API, łącznie znany jako [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), aby połączyć eksperymenty MLflow i usługi Azure Machine Learning. W ten sposób można śledzić i rejestrować metryki eksperymentu i artefakty w [obszarze roboczym usługi Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Jeśli do eksperymentów jest już używane śledzenie przepływów ML, obszar roboczy udostępnia scentralizowaną, bezpieczną i skalowalną lokalizację do przechowywania metryk i modeli szkoleniowych.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) to biblioteka typu open source do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow jest składnikiem MLflow, który rejestruje i śledzi metryki przebiegu szkolenia i artefakty modelu, niezależnie od środowiska eksperymentu — lokalnie na komputerze, na zdalnym celu obliczeniowym, maszynie wirtualnej lub klastrze usługi Azure Databricks. 

Na poniższym diagramie przedstawiono, że za pomocą śledzenia przepływów ML, można śledzić metryki uruchamiania eksperymentu i artefakty modelu magazynu w obszarze roboczym usługi Azure Machine Learning.

![mlflow z diagramem azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informacje zawarte w tym dokumencie są przeznaczone przede wszystkim dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowanym monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub ukończone wdrożenia modelu, zobacz [Monitorowanie usługi Azure Machine Learning.](monitor-azure-machine-learning.md)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porównanie klientów mlflow i usługi Azure Machine Learning

 Poniższa tabela zawiera podsumowanie różnych klientów, którzy mogą korzystać z usługi Azure Machine Learning i ich odpowiednich funkcji.

 MLflow Tracking oferuje funkcje rejestrowania metryk i przechowywania artefaktów, które są dostępne tylko w inny sposób za pośrednictwem [sdk języka Python usługi Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | Śledzenie przepływu&nbsp;mlflow <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Studio uczenia maszynowego Azure|
|---|---|---|---|---|
| Zarządzanie obszarem roboczym |   | ✓ | ✓ | ✓ |
| Korzystanie ze magazynów danych  |   | ✓ | ✓ | |
| Log y metryki      | ✓ | ✓ |   | |
| Przekazywanie artefaktów | ✓ | ✓ |   | |
| Wyświetlanie metryk     | ✓ | ✓ | ✓ | ✓ |
| Zarządzanie obliczeniami   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) na komputerze lokalnym Zestaw SDK zapewnia łączność dla przepływu obiektów ML, aby uzyskać dostęp do obszaru roboczego.
* [Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Śledzenie przebiegów lokalnych

Śledzenie przepływów ml z usługą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z lokalnych uruchomień w obszarze roboczym usługi Azure Machine Learning.

Zainstaluj `azureml-mlflow` pakiet, aby używać śledzenia przepływów ML z usługą Azure Machine Learning w eksperymentach uruchamianych lokalnie w notesie lub edytorze kodu Jupyter.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Obszar nazw azureml.contrib zmienia się często, ponieważ pracujemy nad ulepszeniem usługi. W związku z tym wszystko w tej przestrzeni nazw należy uznać za wersję zapoznawczą i nie jest w pełni obsługiwane przez firmę Microsoft.

Zaimportuj `mlflow` i [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasy, aby uzyskać dostęp do śledzenia identyfikatora URI MLflow i skonfigurować obszar roboczy.

W poniższym kodzie `get_mlflow_tracking_uri()` metoda przypisuje unikatowy adres identyfikatora `ws`URI `set_tracking_uri()` śledzenia śledzenia do obszaru roboczego i wskazuje identyfikator URI śledzenia przepływu MLflow na ten adres.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identyfikator URI śledzenia jest ważny do godziny lub mniej. Jeśli ponownie uruchom skrypt po pewnym czasie bezczynnością, użyj get_mlflow_tracking_uri interfejsu API, aby uzyskać nowy identyfikator URI.

Ustaw nazwę eksperymentu MLflow z `set_experiment()` programem . `start_run()` Następnie `log_metric()` użyj, aby aktywować interfejs API rejestrowania przepływów MLflow i rozpocząć rejestrowanie metryk przebiegu szkolenia.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Śledzenie zdalnych przebiegów

Śledzenie przepływów ML za pomocą usługi Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z zdalnego przebiegów w obszarze roboczym usługi Azure Machine Learning.

Zdalne przebiegi umożliwiają uczenie modeli na bardziej zaawansowanych obliczeniach, takich jak maszyny wirtualne z obsługą procesora GPU lub klastry obliczeniowe uczenia maszynowego. Zobacz [Konfigurowanie celów obliczeniowych do szkolenia modelu,](how-to-set-up-training-targets.md) aby dowiedzieć się więcej o różnych opcjach obliczeniowych.

Skonfiguruj środowisko obliczeniowe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i szkoleniowe z klasą. Dołącz `mlflow` `azureml-mlflow` pakiety i pip [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) w sekcji środowiska. Następnie [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) konstruuj za pomocą zdalnego obliczeń jako celu obliczeniowego.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

W skrypcie `mlflow` szkolenia zaimportuj, aby użyć interfejsów API rejestrowania przepływów MLflow i rozpocznij rejestrowanie metryk uruchamiania.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

W przypadku tej konfiguracji przebiegu `Experiment.submit('train.py')` obliczeń i szkoleń użyj tej metody, aby przesłać przebieg. Ta metoda automatycznie ustawia identyfikator URI śledzenia przepływu MLflow i kieruje rejestrowanie z przepływu do obszaru roboczego.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Śledzenie przebiegów usługi Azure Databricks

Śledzenie przepływów ML z usługą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z uruchomień usługi Azure Databricks w obszarze roboczym usługi Azure Machine Learning.

Aby uruchomić eksperymenty mlflow z usługą Azure Databricks, należy najpierw utworzyć [obszar roboczy i klaster usługi Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) W klastrze należy zainstalować bibliotekę *azureml-mlflow* z PyPi, aby upewnić się, że klasowy klasowy ma dostęp do niezbędnych funkcji i klas.

W tym miejscu zaimportuj notes eksperymentu, dołącz go do klastra usługi Azure Databricks i uruchom eksperyment. 

### <a name="install-libraries"></a>Instalowanie bibliotek

Aby zainstalować biblioteki w klastrze, przejdź do karty **Biblioteki** i kliknij pozycję **Zainstaluj**

 ![mlflow z diagramem azure machine learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

W polu **Pakiet** wpisz azureml-mlflow, a następnie kliknij przycisk Zainstaluj. Powtórz ten krok, aby zainstalować inne dodatkowe pakiety w klastrze dla eksperymentu.

 ![mlflow z diagramem azure machine learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Konfigurowanie notesu i obszaru roboczego

Po skonfigurowaniu klastra zaimportuj notes eksperymentu, otwórz go i dołącz do niego klaster.

Poniższy kod powinien znajdować się w notesie eksperymentu. Ten kod pobiera szczegółowe informacje o subskrypcji platformy Azure, aby utworzyć wystąpienia obszaru roboczego. Ten kod zakłada, że masz istniejącą grupę zasobów i obszar roboczy usługi Azure Machine Learning, w przeciwnym razie można [je utworzyć.](how-to-manage-workspace.md) 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Łączenie obszarów roboczych usługi Azure Databricks i usługi Azure Machine Learning

W [witrynie Azure portal](https://ms.portal.azure.com)można połączyć obszar roboczy usługi Azure Databricks (ADB) z nowym lub istniejącym obszarem roboczym usługi Azure Machine Learning. Aby to zrobić, przejdź do obszaru roboczego bazy danych ADB i wybierz przycisk **Łącze usługi Azure Machine Learning** w prawym dolnym rogu. Łączenie obszarów roboczych umożliwia śledzenie danych eksperymentu w obszarze roboczym usługi Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Łączenie śledzenia przepływów MLflow z obszarem roboczym

Po utworzeniu wystąpienia obszaru roboczego ustaw identyfikator URI śledzenia przepływu MLflow. W ten sposób należy połączyć śledzenie przepływów ML do obszaru roboczego usługi Azure Machine Learning. Po połączeniu wszystkie eksperymenty wylądują w zarządzanej usłudze śledzenia usługi Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Bezpośrednio ustaw śledzenie przepływu ML w notesie

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

W skrypcie szkolenia zaimportuj mlflow, aby użyć interfejsów API rejestrowania przepływów MLflow i rozpocznij rejestrowanie metryk uruchamiania. Poniższy przykład rejestruje metrykę utraty epoki. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatyzacja śledzenia przepływu przepływu ML

Zamiast ręcznie ustawiać identyfikator URI śledzenia w każdej kolejnej sesji notesu eksperymentu w klastrach, zrób to automatycznie za pomocą tego [skryptu Init klastra śledzenia usługi Azure Machine Learning.](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

Po poprawnej konfiguracji można wyświetlić dane śledzenia przepływu MLflow w interfejsie API REST usługi Azure Machine Learning i wszystkich klientów oraz w usłudze Azure Databricks za pośrednictwem interfejsu użytkownika MLflow lub przy użyciu klienta MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Wyświetlanie metryk i artefaktów w obszarze roboczym

Metryki i artefakty z rejestrowania przepływów ML są przechowywane w obszarze roboczym. Aby wyświetlić je w dowolnym momencie, przejdź do obszaru roboczego i znajdź eksperyment według nazwy w obszarze roboczym w [studiu usługi Azure Machine Learning.](https://ml.azure.com)  Lub uruchom poniższy kod. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Przykładowe notesy

[MLflow z notesów usługi Azure ML](https://aka.ms/azureml-mlflow-examples) zademonstrować i rozwinąć pojęcia przedstawione w tym artykule.

## <a name="next-steps"></a>Następne kroki
* [Zarządzaj modelami](concept-model-management-and-deployment.md).
* Monitoruj modele produkcyjne pod kątem [dryfu danych](how-to-monitor-data-drift.md).
