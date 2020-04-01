---
title: Jak wdrożyć modele w wystąpieniach kontenerów platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele usługi Azure Machine Learning jako usługę sieci web przy użyciu wystąpień kontenerów platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475459"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Wdrażanie modelu w wystąpieniach kontenera platformy Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak używać usługi Azure Machine Learning do wdrażania modelu jako usługi sieci web w wystąpieniach kontenerów platformy Azure (ACI). Użyj wystąpienia kontenera platformy Azure, jeśli spełniony jest jeden z następujących warunków:

- Należy szybko wdrożyć i zweryfikować model. Nie trzeba tworzyć kontenerów ACI z wyprzedzeniem. Są one tworzone jako część procesu wdrażania.
- Testujesz model, który jest w fazie rozwoju. 

Aby uzyskać informacje na temat przydziału i dostępności regionu dla usługi ACI, zobacz [przydziały i dostępność regionów dla wystąpień kontenerów platformy Azure.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

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

## <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI

Aby wdrożyć model w wystąpieniach kontenera platformy Azure, utwórz __konfigurację wdrożenia__ opisującą potrzebne zasoby obliczeniowe. Na przykład liczba rdzeni i pamięci. Potrzebna jest również __konfiguracja wnioskowania,__ która opisuje środowisko potrzebne do obsługi modelu i usługi sieci web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [Usługa AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Usługa sieci Web.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć następującego polecenia. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz odwołanie do [modelu az ml deploy.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>Korzystanie z programu VS Code

Zobacz [wdrażanie modeli za pomocą programu VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nie trzeba tworzyć kontenera ACI, aby przetestować z wyprzedzeniem. Kontenery ACI są tworzone w razie potrzeby.

## <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu ml wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)
