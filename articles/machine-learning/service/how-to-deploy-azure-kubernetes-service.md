---
title: Jak wdrażać modele w usłudze Azure Kubernetes Service
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć modele usług Azure Machine Learning jako usługę sieci Web przy użyciu usługi Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: deb6482c0419a5872ccf86f0014adbecc7be6c9d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694387"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie modelu w klastrze usługi Azure Kubernetes Service

Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model jako usługę sieci Web w usłudze Azure Kubernetes Service (AKS). Usługa Azure Kubernetes Service jest dobra w przypadku dużych wdrożeń produkcyjnych. Użyj usługi Azure Kubernetes Service, jeśli potrzebujesz co najmniej jednej z następujących możliwości:

- __Krótki czas odpowiedzi__.
- __Skalowanie__ automatyczne wdrożonej usługi.
- Opcje przyspieszania sprzętowego, takie jak macierze oparte na procesorach GPU i polach (FPGA).

> [!IMPORTANT]
> Skalowanie Cluter nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby uzyskać więcej informacji na temat skalowania węzłów w klastrze AKS, zobacz [skalowanie liczby węzłów w KLASTRZE AKS](../../aks/scale-cluster.md).

Podczas wdrażania w usłudze Azure Kubernetes należy wdrożyć klaster AKS, który jest __połączony z obszarem roboczym__. Istnieją dwa sposoby łączenia klastra AKS z obszarem roboczym:

* Utwórz klaster AKS przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia Machine Learning lub Azure Portal. Ten proces automatycznie łączy klaster z obszarem roboczym.
* Dołącz istniejący klaster AKS do obszaru roboczego usługi Azure Machine Learning. Klaster może być dołączany przy użyciu zestawu SDK usługi Azure Machine Learning, Machine Learning interfejsu wiersza polecenia lub Azure Portal.

> [!IMPORTANT]
> Proces tworzenia lub załączników to zadanie jednorazowe. Gdy klaster AKS jest połączony z obszarem roboczym, można go użyć do wdrożeń. Możesz odłączyć lub usunąć klaster AKS, jeśli nie jest już potrzebny. Po detatched lub usunięciu nie będzie już można wdrażać w klastrze.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](setup-create-workspace.md).

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zapoznaj [się z tematem jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://aka.ms/aml-sdk)lub [rozszerzenia Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- W fragmentach kodu w języku __Python__ w tym artykule założono, że ustawiono następujące zmienne:

    * `ws`-Ustaw na obszar roboczy.
    * `model`-Ustaw na zarejestrowany model.
    * `inference_config`-Ustaw na konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- W fragmentach __interfejsu wiersza polecenia__ w tym artykule przyjęto założenie `inferenceconfig.json` , że dokument został utworzony. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Tworzenie nowego klastra AKS

**Szacowany czas**: Około 20 minut.

Tworzenie i dołączanie klastra AKS jest jednym procesem czasu dla Twojego obszaru roboczego. Można ponownie użyć klastra dla wielu wdrożeń. W przypadku usunięcia klastra lub grupy zasobów, która zawiera tę usługę, należy utworzyć nowy klaster przy następnym wdrożeniu. Do obszaru roboczego można dołączyć wiele klastrów AKS.

Jeśli chcesz utworzyć klaster AKS __na potrzeby tworzenia__, __sprawdzania poprawności__i __testowania__ zamiast produkcji, możesz określić __cel klastra__ dla __testu deweloperskiego__.

W poniższych przykładach pokazano, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK i interfejsu wiersza polecenia:

**Korzystanie z zestawu SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> W [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)przypadku wybrania wartości niestandardowych dla agent_count i vm_size, należy upewnić się, że agent_count pomnożone przez vm_size jest większe lub równe 12 wirtualnym procesorom CPU. Na przykład jeśli używasz vm_size "Standard_D3_v2", który ma 4 procesory wirtualne, należy wybrać agent_count z 3 lub większą.
>
> Zestaw SDK Azure Machine Learning nie zapewnia obsługi skalowania klastra AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika dla klastra AKS w Azure Portal. Można zmienić tylko liczbę węzłów, a nie rozmiar maszyn wirtualnych klastra.

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Korzystanie z interfejsu wiersza polecenia**

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create pyta](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Dołącz istniejący klaster AKS

**Szacowany czas:** Około 5 minut.

Jeśli masz już klaster AKS w ramach subskrypcji platformy Azure i jest to wersja 1.12. # #, możesz użyć jej do wdrożenia obrazu.

> [!TIP]
> Istniejący klaster AKS może znajdować się w regionie platformy Azure niż obszar roboczy usługi Azure Machine Learning.

> [!WARNING]
> Podczas dołączania klastra AKS do obszaru roboczego można określić, jak będzie używany klaster przez ustawienie `cluster_purpose` parametru.
>
> Jeśli nie ustawisz `cluster_purpose` parametru lub zestawu `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, klaster musi mieć co najmniej 12 dostępnych wirtualnych procesorów CPU.
>
> Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, klaster nie musi mieć 12 wirtualnych procesorów CPU. Jednak klaster skonfigurowany do tworzenia i testowania nie będzie odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania.

Aby uzyskać więcej informacji na temat tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu, zobacz następujące artykuły:

* [Tworzenie klastra AKS (interfejs wiersza polecenia)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Tworzenie klastra AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

W poniższych przykładach pokazano, jak dołączyć istniejący klaster AKS 1.12. # # do obszaru roboczego:

**Korzystanie z zestawu SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Korzystanie z interfejsu wiersza polecenia**

Aby dołączyć istniejący klaster przy użyciu interfejsu wiersza polecenia, należy uzyskać identyfikator zasobu istniejącego klastra. Aby uzyskać tę wartość, użyj następującego polecenia. Zamień `myexistingcluster` na nazwę klastra AKS. Zamień `myresourcegroup` na grupę zasobów zawierającą klaster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

To polecenie zwraca wartość podobną do następującego tekstu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Aby dołączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zamień `aksresourceid` na wartość zwracaną przez poprzednie polecenie. Zamień `myresourcegroup` na grupę zasobów, która zawiera obszar roboczy. Zamień `myworkspace` na nazwę obszaru roboczego.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) Reference.

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model w usłudze Azure Kubernetes Service, Utwórz __konfigurację wdrożenia__ opisującą wymaganą wartość zasobów obliczeniowych. Na przykład liczba rdzeni i pamięć. Potrzebna jest również __Konfiguracja wnioskowania__opisująca środowisko wymagane do hostowania modelu i usługi sieci Web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Usługa WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zamień `myaks` na nazwę elementu docelowego obliczeń AKS. Zastąp `mymodel:1` wartość nazwą i wersją zarejestrowanego modelu. Zamień `myservice` na nazwę, która ma zostać przydana do tej usługi:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference. 

## <a name="using-vs-code"></a>Używanie VS Code

Aby uzyskać informacje na temat korzystania z VS Code, zobacz [wdrażanie do AKS za pomocą rozszerzenia vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Wdrożenie za pomocą VS Code wymaga, aby klaster AKS został utworzony lub dołączony do obszaru roboczego z wyprzedzeniem.

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
