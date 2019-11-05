---
title: Jak wdrażać modele w wystąpieniach obliczeniowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrażać modele Azure Machine Learning jako usługę sieci Web przy użyciu wystąpień obliczeniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: bb187826250b3edc9ac3d9e36a243d75819a45b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496886"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Wdrażanie modelu w celu Azure Machine Learning wystąpień obliczeniowych

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Wystąpienia obliczeniowe są dostępne tylko dla obszarów roboczych z regionem **Północno-środkowe stany USA** lub **Południowe Zjednoczone Królestwo**.
>Jeśli obszar roboczy znajduje się w innym regionie, możesz w zamian utworzyć [maszynę wirtualną w notesie](concept-compute-instance.md#notebookvm) i korzystać z niej.  Model można wdrożyć w wystąpieniu obliczeniowym lub na maszynie wirtualnej notesu, wykonując kroki opisane w tym artykule.

Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model jako usługę sieci Web w wystąpieniu Azure Machine Learning COMPUTE. Użyj wystąpień obliczeniowych, jeśli spełniony jest jeden z następujących warunków:

- Należy szybko wdrożyć i zweryfikować model.
- Testujesz model, który jest w fazie projektowania.

> [!TIP]
> Wdrożenie modelu z Jupyter Notebook w wystąpieniu obliczeniowym w usłudze sieci Web na tej samej maszynie wirtualnej jest _wdrożeniem lokalnym_. W takim przypadku komputer "Local" jest wystąpieniem obliczeniowym. Aby uzyskać więcej informacji na temat wdrożeń, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy Azure Machine Learning z uruchomionym wystąpieniem obliczeniowym. Aby uzyskać więcej informacji, zobacz temat [Instalowanie środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Wdrażanie w wystąpieniach obliczeniowych

Przykładowy Notes, który pokazuje lokalne wdrożenia, jest uwzględniony w wystąpieniu obliczeniowym. Wykonaj następujące kroki, aby załadować Notes i wdrożyć model jako usługę sieci Web na maszynie wirtualnej:

1. W programie [Azure Machine Learning Studio](https://ml.azure.com)wybierz Azure Machine Learning wystąpienia obliczeniowe.

1. Otwórz podkatalog `samples-*`, a następnie otwórz `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Po otwarciu Uruchom Notes.

    ![Zrzut ekranu przedstawiający uruchomioną usługę lokalną w notesie](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Notes wyświetla adres URL i port, na którym uruchomiono usługę. Na przykład `https://localhost:6789`. Możesz również uruchomić komórkę zawierającą `print('Local service port: {}'.format(local_service.port))`, aby wyświetlić port.

    ![Zrzut ekranu z uruchomionym portem usługi lokalnej](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Aby przetestować usługę z wystąpienia obliczeniowego, użyj adresu URL `https://localhost:<local_service.port>`. Aby przetestować klienta zdalnego, uzyskaj publiczny adres URL usługi uruchomionej w wystąpieniu obliczeniowym. Publiczny adres URL można określić przy użyciu następującej formuły: 
    * Maszyna wirtualna Notes: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Wystąpienie obliczeniowe: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 
    
    Na przykład: 
    * Maszyna wirtualna Notes: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Wystąpienie obliczeniowe: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testowanie usługi

Aby przesłać przykładowe dane do uruchomionej usługi, użyj poniższego kodu. Zastąp wartość `service_url` adresem URL z poprzedniego kroku:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie Azure Machine Learning usług sieci Web przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)