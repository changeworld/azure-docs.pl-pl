---
title: Zmiana kluczy dostępu do konta magazynu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zmienić klucze dostępu dla konta usługi Azure Storage używanego przez obszar roboczy. Usługa Azure Machine Learning korzysta z konta usługi Azure Storage do przechowywania danych i modeli. Podczas ponownego generowania klucza dostępu dla konta magazynu, należy zaktualizować usługi Azure Machine Learning w celu używania nowych kluczy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543842"
---
# <a name="regenerate-storage-account-access-keys"></a>Ponowne generowanie kluczy dostępu do konta magazynu

Dowiedz się, jak zmienić klucze dostępu dla konta usługi Azure Storage używane przez usługę Azure Machine Learning. Usługa Azure Machine Learning można użyć konta magazynu do przechowywania danych lub przeszkolone modele.

Ze względów bezpieczeństwa może być konieczna zmiana klucze dostępu dla konta usługi Azure Storage. Podczas ponownego generowania klucza dostępu, należy zaktualizować usługi Azure Machine Learning, aby używać nowego klucza. Usługa Azure Machine Learning może być używany na koncie magazynu dla obu modelu magazynu oraz Magazyn danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy](setup-create-workspace.md) artykułu.

* [Usługi Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Czego potrzebuje do zaktualizowania

Konta magazynu może służyć przez obszar roboczy usługi Azure Machine Learning (przechowywania dzienników, modele, migawki, itp.) i jako magazyn danych. Proces, aby zaktualizować obszar roboczy jest pojedyncze polecenie wiersza polecenia platformy Azure i zostać uruchomione po zaktualizowaniu klucza magazynu. Proces aktualizowania magazynów danych jest bardziej skomplikowane i wymaga odnajdywania, jakich magazynów danych obecnie używasz konta magazynu, a następnie ponowne zarejestrowanie ich.

> [!IMPORTANT]
> Aktualizowanie obszaru roboczego przy użyciu wiersza polecenia platformy Azure i magazynów danych przy użyciu języka Python, w tym samym czasie. Aktualizowanie tylko jedna lub druga nie jest wystarczające i może powodować błędy, dopóki oba są aktualizowane.

Aby odnaleźć kont magazynu, które są używane przez usługi magazynów danych, użyj następującego kodu:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Ten kod wyszukuje zarejestrowane magazynów danych, używanego przez usługi Azure Storage i zawiera następujące informacje:

* Nazwa magazynu danych: Nazwa magazynu danych, którego zarejestrowane jest konto magazynu.
* Nazwa konta magazynu: Nazwa konta usługi Azure Storage.
* Kontener: Kontener na koncie magazynu używanego przez tę rejestrację.

Jeśli istnieje wpis dla konta magazynu, w którym planujesz ponownie wygenerować klucze dostępu, należy zapisać nazwę magazynu danych, nazwę konta magazynu i nazwa kontenera.

## <a name="update-the-access-key"></a>Aktualizuj klucz dostępu

Aby zaktualizować usługę Azure Machine Learning do używania nowego klucza, wykonaj następujące czynności:

> [!IMPORTANT]
> Wykonaj wszystkie czynności, aktualizowania, zarówno obszaru roboczego przy użyciu interfejsu wiersza polecenia i magazynów danych przy użyciu języka Python. Aktualizowanie tylko jedna lub druga może powodować błędy, dopóki oba są aktualizowane.

1. Wygeneruj ponownie klucz. Aby uzyskać informacji na temat ponownego generowania klucza dostępu, zobacz [Zarządzanie kontem magazynu](/azure/storage/common/storage-account-manage#access-keys) artykułu. Zapisz nowy klucz.

1. Aby zaktualizować obszar roboczy w celu używania nowego klucza, wykonaj następujące kroki:

    1. Aby zalogować się do subskrypcji platformy Azure, która zawiera obszar roboczy za pomocą następującego polecenia wiersza polecenia platformy Azure:

        ```azurecli-interactive
        az login
        ```

    1. Aby zainstalować rozszerzenia usługi Azure Machine Learning, użyj następującego polecenia:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Aby zaktualizować obszar roboczy w celu używania nowego klucza, użyj następującego polecenia. Zastąp `myworkspace` przy użyciu nazwy obszaru roboczego usługi Azure Machine Learning i Zastąp `myresourcegroup` nazwą grupy zasobów platformy Azure, która zawiera obszar roboczy.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        To polecenie automatycznie synchronizuje nowych kluczy dla konta usługi Azure storage używanego przez obszar roboczy.

1. Aby ponownie zarejestrować magazyny danych, które używają konta magazynu, należy użyć wartości z [co musi zostać zaktualizowany](#whattoupdate) sekcji i klucza z kroku 1, używając następującego kodu:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Ponieważ `overwrite=True` jest określony, ten kod zastępuje istniejące rejestracji i aktualizuje go do używania nowego klucza.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rejestrowania magazynów danych, zobacz [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasy odniesienia.
