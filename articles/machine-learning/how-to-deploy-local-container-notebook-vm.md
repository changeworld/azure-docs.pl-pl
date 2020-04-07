---
title: Jak wdrożyć modele do obliczania wystąpień
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele usługi Azure Machine Learning jako usługę sieci web przy użyciu wystąpień obliczeniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756577"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Wdrażanie modelu w wystąpieniach obliczeniowych usługi Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak używać usługi Azure Machine Learning do wdrażania modelu jako usługi sieci web w wystąpieniu obliczeniowym usługi Azure Machine Learning. Użyj instancji obliczeniowych, jeśli spełniony jest jeden z następujących warunków:

- Należy szybko wdrożyć i zweryfikować model.
- Testujesz model, który jest w fazie rozwoju.

> [!TIP]
> Wdrażanie modelu z notesu Jupyter w wystąpieniu obliczeniowym do usługi sieci web na tej samej maszynie wirtualnej jest _wdrożeniem lokalnym._ W takim przypadku "lokalny" komputer jest wystąpieniem obliczeniowym. Aby uzyskać więcej informacji na temat wdrożeń, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning z uruchomionym wystąpieniem obliczeniowym. Aby uzyskać więcej informacji, zobacz [Środowisko instalacji i obszar roboczy](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Wdrażanie w wystąpieniach obliczeniowych

Przykładowy notes, który pokazuje wdrożenia lokalne znajduje się w wystąpieniu obliczeniowym. Aby załadować notes i wdrożyć model jako usługę sieci web na maszynie wirtualnej, należy wykonać następujące kroki:

1. W [studiu usługi Azure Machine Learning](https://ml.azure.com)wybierz wystąpienia obliczeniowe usługi Azure Machine Learning.

1. Otwórz `samples-*` podkatalog, a `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`następnie otwórz . Po otwarciu uruchom notes.

    ![Zrzut ekranu przedstawiający uruchomiającą usługę lokalną w notesie](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Notes wyświetla adres URL i port, na których jest uruchomiona usługa. Na przykład `https://localhost:6789`. Można również uruchomić komórkę `print('Local service port: {}'.format(local_service.port))` zawierającą, aby wyświetlić port.

    ![Zrzut ekranu przedstawiający uruchomiony port usługi lokalnej](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Aby przetestować usługę z wystąpienia obliczeniowego, użyj `https://localhost:<local_service.port>` adresu URL. Aby przetestować z klienta zdalnego, pobierz publiczny adres URL usługi uruchomionej w wystąpieniu obliczeniowym. Publiczny adres URL można określić przy użyciu następującej formuły; 
    * Maszyna wirtualna `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`notebooka: . 
    * Oblicz wystąpienie: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Na przykład: 
    * Maszyna wirtualna notesu:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Oblicza wystąpienie:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testowanie usługi

Aby przesłać przykładowe dane do uruchomionej usługi, użyj następującego kodu. Zastąp `service_url` wartość adresu URL z poprzedniego kroku:

> [!NOTE]
> Podczas uwierzytelniania do wdrożenia w wystąpieniu obliczeniowym, uwierzytelnianie jest dokonywane przy użyciu usługi Azure Active Directory. Wywołanie `interactive_auth.get_authentication_header()` w przykładowym kodzie uwierzytelnia cię przy użyciu usługi AAD i zwraca nagłówek, który następnie może służyć do uwierzytelniania w usłudze w wystąpieniu obliczeniowym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning](how-to-setup-authentication.md#interactive-authentication).
>
> Podczas uwierzytelniania do wdrożenia w usłudze Azure Kubernetes lub instancje kontenera platformy Azure używana jest inna metoda uwierzytelniania. Aby uzyskać więcej informacji na ten temat, zobacz [Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu ml wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)