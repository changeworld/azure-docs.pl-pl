---
title: Wdrażanie modeli do rozwiązania Kubernetes w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning w usłudze Azure Kubernetes Service. Model jest wdrażany jako usługę sieci web. Usługa Kubernetes jest dobra dla obciążeń produkcyjnych w dużej skali.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: d9328b293d38114d319d79e38b91b1b67e410d94
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581840"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Jak wdrażać modele z usługi Azure Machine Learning w usłudze Azure Kubernetes Service

Na potrzeby scenariuszy produkcyjnych w dużej skali możesz wdrożyć model do usługi Azure Kubernetes Service (AKS). Usługa Azure Machine Learning można użyć istniejącego klastra AKS lub utworzony podczas wdrażania nowego klastra. Model jest wdrożony na poproś jako usługę sieci web.

Wdrażanie w usłudze AKS zapewnia automatyczne skalowanie, rejestrowanie, zbierania danych modelu i krótszych czasów reakcji usługi sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).

- Usługi Azure Machine Learning service obszar roboczy, zawierający skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane katalogu lokalnego. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.

- Model uczenia maszynowego uczony. Jeśli nie masz, zobacz [szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.

## <a name="initialize-the-workspace"></a>Inicjowanie obszaru roboczego

Aby zainicjować obszaru roboczego, należy załadować `config.json` pliku, który zawiera informacje o obszarze roboczym.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Zarejestruj model

Aby zarejestrować istniejący model, określ ścieżkę modelu, opis i tagi.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Tworzenie obrazu platformy Docker

Usługa Azure Kubernetes Service korzysta z obrazów platformy Docker. Do utworzenia obrazu, wykonaj następujące kroki:

1. Aby skonfigurować obraz, należy utworzyć skrypt oceniania i plikiem środowiska. Aby uzyskać przykład tworzenia pliku skryptu i środowiska zobacz następujące sekcje przykładu klasyfikacji obrazów:

    * [Utwórz skrypt oceniania (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > Skrypt oceniania odbiera dane przesłane przez klientów i przekazuje je do modelu w celu oceniania. Dokumentowanie struktury danych, skrypt i model oczekujesz. Posiadanie tej dokumentacji wiele ułatwia podczas tworzenia klienta do korzystania z usługi sieci web.

    * [Utwórz plik środowiska (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   W poniższym przykładzie użyto tych plików, aby skonfigurować obraz:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Do utworzenia obrazu, należy użyć konfiguracji modelu i obrazów. Ta operacja może potrwać około 5 minut do ukończenia:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Tworzenie klastra AKS

Poniższy fragment kodu przedstawia sposób tworzenia klastra AKS. Ten proces trwa około 20 minut do ukończenia:

> [!IMPORTANT]
> Tworzenie klastra AKS jest czas procesu dla obszaru roboczego. Po utworzeniu można ponownie użyć klastra dla wielu wdrożeń. Jeśli możesz usunąć klaster lub grupę zasobów, która ją zawiera, następnie musi utworzysz nowy klaster przy następnym zajdzie potrzeba wdrożenia.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Dołącz istniejący klaster AKS (opcjonalnie)

Jeśli masz istniejący klaster usługi AKS w subskrypcji platformy Azure umożliwia jej wdrożenie Twojego obrazu. Poniższy fragment kodu pokazuje, jak dołączyć klaster z obszarem roboczym. 

> [!IMPORTANT]
> Tylko usługi AKS w wersji 1.11.3 jest obsługiwane.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>Wdrażanie usługi sieci web

Poniższy fragment kodu przedstawia sposób wdrażania obrazu klastra AKS:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Jeśli występują błędy podczas wdrażania, użyj `aks_service.get_logs()` Aby wyświetlić dzienniki usługi AKS. Zarejestrowane informacje może wskazywać przyczynę błędu.

## <a name="test-the-web-service"></a>Test usługi sieci web

Użyj `aks_service.run()` testowania tej usługi sieci web. Poniższy fragment kodu pokazuje, jak przekazać dane do usługi i wyświetlić prognozowania:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Czyszczenie

Aby usunąć usługę, obrazu i model, użyj poniższego fragmentu kodu:

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zużywania modelu uczenia Maszynowego wdrożyć jako usługę sieci web](how-to-consume-web-service.md).