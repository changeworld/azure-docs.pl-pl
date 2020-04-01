---
title: Jak wdrożyć modele w usłudze Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele usługi Azure Machine Learning jako usługę sieci web przy użyciu usługi Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 792964f28ddb3fcb10932b8de9499a9c7027960f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475381"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie modelu w klastrze usługi Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak używać usługi Azure Machine Learning do wdrażania modelu jako usługi sieci web w usłudze Azure Kubernetes (AKS). Usługa Azure Kubernetes jest dobra do wdrożeń produkcyjnych na dużą skalę. Użyj usługi Azure Kubernetes, jeśli potrzebujesz co najmniej jednej z następujących możliwości:

- __Krótki czas reakcji__.
- __Automatyczne skalowanie__ wdrożonej usługi.
- __Opcje przyspieszania sprzętowego,__ takie jak gpu i programowalne w terenie tablice bramek (FPGA).

> [!IMPORTANT]
> Skalowanie klastra nie jest dostarczane za pośrednictwem zestawu SDK usługi Azure Machine Learning. Aby uzyskać więcej informacji na temat skalowania węzłów w klastrze AKS, zobacz [Skalowanie liczby węzłów w klastrze AKS](../aks/scale-cluster.md).

Podczas wdrażania w usłudze Azure Kubernetes można wdrożyć w klastrze AKS, który jest __połączony z obszarem roboczym__. Istnieją dwa sposoby łączenia klastra usługi AKS z obszarem roboczym:

* Utwórz klaster AKS przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia uczenia maszynowego lub [studia usługi Azure Machine Learning.](https://ml.azure.com) Ten proces automatycznie łączy klaster z obszarem roboczym.
* Dołącz istniejący klaster AKS do obszaru roboczego usługi Azure Machine Learning. Klaster można dołączyć przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia uczenia maszynowego lub studia usługi Azure Machine Learning.

> [!IMPORTANT]
> Proces tworzenia lub przywiązywania jest zadaniem jednorazowym. Po podłączeniu klastra AKS do obszaru roboczego można go używać do wdrożeń. Można odłączyć lub usunąć klaster AKS, jeśli nie jest już potrzebny. Po odłączeniu lub usunięciu nie będzie już można wdrożyć w klastrze.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Uczenie maszynowe,](reference-azure-machine-learning-cli.md) [zestaw SDK języka Python usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub rozszerzenie kodu usługi Azure Machine Learning Visual [Studio](tutorial-setup-vscode-extension.md).

- Fragmenty kodu __języka Python__ w tym artykule przyjęto założenie, że są ustawione następujące zmienne:

    * `ws`- Ustaw swój obszar roboczy.
    * `model`- Ustaw zarejestrowany model.
    * `inference_config`- Ustaw konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

- Fragmenty interfejsu __wiersza polecenia__ w tym artykule `inferenceconfig.json` przyjęto założenie, że utworzono dokument. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Tworzenie nowego klastra usługi AKS

**Szacowany czas**: Około 20 minut.

Tworzenie lub dołączanie klastra AKS jest procesem jednorazowym dla obszaru roboczego. Można ponownie użyć tego klastra dla wielu wdrożeń. Jeśli usuniesz klaster lub grupę zasobów, która go zawiera, należy utworzyć nowy klaster przy następnym uruchomieniu. Do obszaru roboczego może być dołączonych wiele klastrów AKS.

> [!TIP]
> Jeśli chcesz zabezpieczyć klaster AKS przy użyciu sieci wirtualnej platformy Azure, należy najpierw utworzyć sieć wirtualną. Aby uzyskać więcej informacji, zobacz [Bezpieczne eksperymentowanie i wnioskowanie za pomocą usługi Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Jeśli chcesz utworzyć klaster AKS do __tworzenia,__ __sprawdzania poprawności__i __testowania__ zamiast produkcji, możesz określić __cel klastra__ do __testowania deweloperskiego.__

> [!WARNING]
> Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, utworzony klaster nie nadaje się do ruchu na poziomie produkcji i może wydłużyć czas wnioskowania. Klastry deweloperów/testowych również nie gwarantują odporności na uszkodzenia. Zalecamy co najmniej 2 procesory wirtualne dla klastrów deweloperskich/testowych.

Poniższe przykłady pokazują, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK i interfejsu wiersza polecenia:

**Używanie zestawu SDK**

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
> Dla [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), jeśli wybierzesz `vm_size`wartości `cluster_purpose` niestandardowe dla `agent_count` i , `agent_count` i `vm_size` nie `DEV_TEST`jest , to musisz upewnić się, że pomnożone przez jest większa lub równa 12 wirtualnych procesorów. Na przykład jeśli używasz `vm_size` "Standard_D3_v2", który ma 4 wirtualne procesory, `agent_count` a następnie należy wybrać z 3 lub więcej.
>
> Zestaw SDK usługi Azure Machine Learning nie zapewnia obsługi skalowania klastra AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika dla klastra AKS w studio usługi Azure Machine Learning. Można zmienić tylko liczbę węzłów, a nie rozmiar maszyny Wirtualnej klastra.

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [Plik ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [Plik ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Korzystanie z interfejsu wiersza polecenia**

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Dołączanie istniejącego klastra AKS

**Szacowanie czasu:** Około 5 minut.

Jeśli masz już klaster AKS w subskrypcji platformy Azure i jest w wersji 1.17 lub niższej, można go użyć do wdrożenia obrazu.

> [!TIP]
> Istniejący klaster AKS może znajdować się w regionie platformy Azure innym niż obszar roboczy usługi Azure Machine Learning.
>
> Jeśli chcesz zabezpieczyć klaster AKS przy użyciu sieci wirtualnej platformy Azure, należy najpierw utworzyć sieć wirtualną. Aby uzyskać więcej informacji, zobacz [Bezpieczne eksperymentowanie i wnioskowanie za pomocą usługi Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Podczas dołączania klastra AKS do obszaru roboczego można zdefiniować sposób `cluster_purpose` używania klastra przez ustawienie parametru.

Jeśli parametr nie `cluster_purpose` zostanie ustawiony `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`lub ustawiony, klaster musi mieć co najmniej 12 procesorów wirtualnych.

Jeśli ustawisz `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, klaster nie musi mieć 12 procesorów wirtualnych. Zalecamy co najmniej 2 wirtualne procesory do tworzenia/testowania. Jednak klaster, który jest skonfigurowany do tworzenia/testowania nie nadaje się do ruchu na poziomie produkcji i może zwiększyć czas wnioskowania. Klastry deweloperów/testowych również nie gwarantują odporności na uszkodzenia.

> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego klastra AKS z obszaru roboczego. Na przykład dołączanie jednego klastra usługi AKS do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzedniego istniejącego załącznika.Com.
>
> Aby ponownie dołączyć klaster AKS, na przykład w celu zmiany protokołu TLS lub innego ustawienia konfiguracji klastra, należy najpierw usunąć istniejący załącznik za pomocą [pliku AksCompute.detach().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)

Aby uzyskać więcej informacji na temat tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia lub portalu platformy Azure, zobacz następujące artykuły:

* [Tworzenie klastra AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Tworzenie klastra AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Poniższe przykłady pokazują, jak dołączyć istniejący klaster usługi AKS do obszaru roboczego:

**Używanie zestawu SDK**

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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Korzystanie z interfejsu wiersza polecenia**

Aby dołączyć istniejący klaster przy użyciu interfejsu wiersza polecenia, należy uzyskać identyfikator zasobu istniejącego klastra. Aby uzyskać tę wartość, należy użyć następującego polecenia. Zamień `myexistingcluster` na nazwę klastra AKS. Zamień `myresourcegroup` grupę zasobów zawierającą klaster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

To polecenie zwraca wartość podobną do następującego tekstu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Aby dołączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zamień `aksresourceid` na wartość zwróconą przez poprzednie polecenie. Zamień `myresourcegroup` ją na grupę zasobów zawierającą obszar roboczy. Zamień `myworkspace` na nazwę obszaru roboczego.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model w usłudze Azure Kubernetes, należy utworzyć __konfigurację wdrażania__ opisującą potrzebne zasoby obliczeniowe. Na przykład liczba rdzeni i pamięci. Potrzebna jest również __konfiguracja wnioskowania,__ która opisuje środowisko potrzebne do obsługi modelu i usługi sieci web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

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

* [AksCompute (AksCompute)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [Usługa AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Usługa sieci Web.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć następującego polecenia. Zamień `myaks` na nazwę obiektu docelowego obliczeń AKS. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz odwołanie do [modelu az ml deploy.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Korzystanie z programu VS Code

Aby uzyskać informacje na temat korzystania z programu VS Code, zobacz [wdrażanie w UKS za pośrednictwem rozszerzenia kodu VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Wdrażanie za pośrednictwem programu VS Code wymaga klastra AKS, który ma zostać utworzony lub dołączony do obszaru roboczego z wyprzedzeniem.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Wdrażanie modeli w udręki AKS przy użyciu kontrolowanego wdrażania (wersja zapoznawcza)
Analizuj i promuj wersje modeli w sposób kontrolowany przy użyciu punktów końcowych. Wdrożenie do 6 wersji za jednym punktem końcowym i skonfigurować % ruchu oceniania do każdej wdrożonej wersji. Można włączyć wgląd w aplikacje, aby wyświetlić metryki operacyjne punktów końcowych i wdrożonych wersji.

### <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Gdy będziesz gotowy do wdrożenia modeli, utwórz punkt końcowy oceniania i wdrożyć pierwszą wersję. Poniższy krok pokazuje, jak wdrożyć i utworzyć punkt końcowy przy użyciu SDK. Pierwsze wdrożenie zostanie zdefiniowane jako wersja domyślna, co oznacza, że nieokreślony percentyl ruchu we wszystkich wersjach przejdzie do wersji domyślnej.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualizowanie i dodawanie wersji do punktu końcowego

Dodaj inną wersję do punktu końcowego i skonfiguruj percentyl ruchu oceniania przechodzący do wersji. Istnieją dwa typy wersji, kontroli i wersji leczenia. Może istnieć wiele wersji leczenia, aby pomóc porównać z pojedynczą wersją kontrolną.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Zaktualizuj istniejące wersje lub usuń je w punkcie końcowym. Można zmienić domyślny typ wersji, typ formantu i percentyl ruchu.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

Podczas wdrażania w usłudze Azure Kubernetes uwierzytelnianie __jest__ domyślnie włączone. Można również włączyć uwierzytelnianie __oparte na tokenie.__ Uwierzytelnianie oparte na tokenie wymaga od klientów użycia konta usługi Azure Active Directory do żądania tokenu uwierzytelniania, który jest używany do żądania do wdrożonej usługi.

Aby __wyłączyć__ uwierzytelnianie, należy ustawić `auth_enabled=False` parametr podczas tworzenia konfiguracji wdrożenia. Poniższy przykład wyłącza uwierzytelnianie przy użyciu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Aby uzyskać informacje na temat uwierzytelniania z aplikacji klienckiej, zobacz [Korzystanie z modelu usługi Azure Machine Learning wdrożone jako usługa sieci web.](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Uwierzytelnianie za pomocą kluczy

Jeśli uwierzytelnianie za pomocą klucza `get_keys` jest włączone, można użyć tej metody do pobrania podstawowego i pomocniczego klucza uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli chcesz zregenerować klucz, użyj[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Uwierzytelnianie za pomocą tokenów

Aby włączyć uwierzytelnianie `token_auth_enabled=True` tokenu, należy ustawić parametr podczas tworzenia lub aktualizowania wdrożenia. Poniższy przykład umożliwia uwierzytelnianie tokenu przy użyciu sdk:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Jeśli uwierzytelnianie tokenu jest włączone, można użyć `get_token` metody do pobrania tokenu JWT i czasu wygaśnięcia tego tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Musisz zażądać nowego tokenu po czasie `refresh_by` tokenu.
>
> Firma Microsoft zdecydowanie zaleca utworzenie obszaru roboczego usługi Azure Machine Learning w tym samym regionie co klaster usługi Azure Kubernetes. Aby uwierzytelnić się za pomocą tokenu, usługa sieci web nawędnie do regionu, w którym jest tworzony obszar roboczy usługi Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. To skutecznie powoduje, że uwierzytelnianie oparte na tokenie jest niedostępne, dopóki region obszaru roboczego jest ponownie dostępny. Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej trwa pobieranie tokenu.

## <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu ml wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)
