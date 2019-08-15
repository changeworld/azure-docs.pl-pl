---
title: Jak wdrażać modele na maszynach wirtualnych notesu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć modele usług Azure Machine Learning jako usługę sieci Web przy użyciu maszyn wirtualnych notesu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: d6b26dfe1eb8ea65dd7c751a148c599123b0f6db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947762"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Wdrażanie modelu na maszynach wirtualnych notesu

Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model jako usługę sieci Web na maszynie wirtualnej notesu. Użyj maszyn wirtualnych z notesem, jeśli spełniony jest jeden z następujących warunków:

- Musisz szybko wdrażać i weryfikacja modelu.
- W przypadku testowania modelu, który jest w fazie projektowania.

> [!TIP]
> Wdrożenie modelu na podstawie Jupyter Notebook na maszynie wirtualnej w notesie do usługi sieci Web na tej samej maszynie wirtualnej jest _wdrażaniem lokalnym_. W takim przypadku komputer "lokalny" jest maszyną wirtualną notesu. Więcej informacji o wdrożeniach znajduje się w temacie [Deploying Models with Azure Machine Learning Service](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning z uruchomioną maszyną wirtualną notesu. Aby uzyskać więcej informacji, zobacz temat [Instalowanie środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Wdrażanie na maszynach wirtualnych notesu

Przykładowy Notes demonstrujący lokalne wdrożenia znajduje się na maszynie wirtualnej notesu. Wykonaj następujące kroki, aby załadować Notes i wdrożyć model jako usługę sieci Web na maszynie wirtualnej:

1. Na [Azure Portal](https://portal.azure.com)wybierz maszyny wirtualne Azure Machine Learning notesu.

1. Otwórz podkatalog, a następnie otwórz `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. `samples-*` Po otwarciu Uruchom Notes.

    ![Zrzut ekranu przedstawiający uruchomioną usługę lokalną w notesie](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Notes wyświetla adres URL i port, na którym uruchomiono usługę. Na przykład `https://localhost:6789`. Możesz również uruchomić komórkę zawierającą `print('Local service port: {}'.format(local_service.port))` , aby wyświetlić port.

    ![Zrzut ekranu z uruchomionym portem usługi lokalnej](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Aby przetestować usługę z poziomu maszyny wirtualnej notesu, użyj `https://localhost:<local_service.port>` adresu URL. Aby przetestować klienta zdalnego, uzyskaj publiczny adres URL usługi działającej na maszynie wirtualnej notesu publiczny adres URL można określić przy użyciu następującej formuły: `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Na przykład `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

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
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)