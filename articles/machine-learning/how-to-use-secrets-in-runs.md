---
title: Używanie sekretów w biegach szkoleniowych
titleSuffix: Azure Machine Learning
description: Przekazywanie wpisów tajnych do szkoleń przebiegów w bezpieczny sposób przy użyciu workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942272"
---
# <a name="use-secrets-in-training-runs"></a>Używanie sekretów w biegach szkoleniowych
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak bezpiecznie używać wpisów tajnych w szkoleniach. Informacje uwierzytelniania, takie jak nazwa użytkownika i hasło są wpisami tajnymi. Na przykład jeśli łączysz się z zewnętrzną bazą danych w celu wykonywania zapytań o dane szkoleniowe, należy przekazać nazwę użytkownika i hasło do kontekstu uruchamiania zdalnego. Kodowanie takich wartości do skryptów szkoleniowych w cleartext jest niebezpieczne, ponieważ może ujawnić klucz tajny. 

Zamiast tego obszar roboczy usługi Azure Machine Learning ma skojarzony zasób o nazwie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Ta usługa Key Vault służy do przekazywania wpisów tajnych do zdalnych uruchomień bezpiecznie za pośrednictwem zestawu interfejsów API w zestawie Azure Machine Learning Python SDK.

Podstawowy przepływ przy użyciu wpisów tajnych jest:
 1. Na komputerze lokalnym zaloguj się na platformie Azure i połącz się z obszarem roboczym.
 2. Na komputerze lokalnym ustaw klucz tajny w programie Workspace Key Vault.
 3. Prześlij zdalny przebieg.
 4. W ramach zdalnego uruchomienia pobierz klucz tajny z usługi Key Vault i użyj jej.

## <a name="set-secrets"></a>Ustawianie wpisów tajnych

W usłudze Azure Machine Learning klasa [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) zawiera metody ustawiania wpisów tajnych. W lokalnej sesji języka Python najpierw uzyskaj odwołanie do magazynu kluczy obszaru roboczego, [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) a następnie użyj tej metody, aby ustawić klucz tajny według nazwy i wartości. Metoda __set_secret__ aktualizuje wartość tajną, jeśli nazwa już istnieje.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Nie umieszczaj tajnej wartości w kodzie Języka Python, ponieważ przechowywanie jej w pliku jako cleartext jest niebezpieczne. Zamiast tego uzyskać wartość tajną ze zmiennej środowiskowej, na przykład Azure DevOps kompilacji klucza tajnego lub z interaktywnych danych wejściowych użytkownika.

Można wyświetlić listę [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) tajnych nazw przy użyciu metody i istnieje również wersja wsadowa,[set_secrets(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) który pozwala ustawić wiele wpisów tajnych naraz.

## <a name="get-secrets"></a>Zdobądź sekrety

W kodzie lokalnym można[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) użyć metody, aby uzyskać wartość tajnego według nazwy.

W przypadku przebiegów [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) przesłanych do , użyj [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metody z klasą. [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) Ponieważ przesłane uruchomienie jest świadomy jego obszaru roboczego, ta metoda skróty wystąpienia obszaru roboczego i zwraca wartość tajnego bezpośrednio.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Należy uważać, aby nie ujawnić tajnej wartości, zapisując lub drukując ją.

Istnieje również wersja wsadowa, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) do uzyskiwania dostępu do wielu wpisów tajnych naraz.

## <a name="next-steps"></a>Następne kroki

 * [Wyświetlanie przykładowego notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Dowiedz się więcej o zabezpieczeniach przedsiębiorstwa dzięki usłudze Azure Machine Learning](concept-enterprise-security.md)
