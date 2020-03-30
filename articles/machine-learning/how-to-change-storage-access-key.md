---
title: Zmienianie kluczy dostępu do konta magazynu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zmienić klucze dostępu dla konta usługi Azure Storage używanego przez obszar roboczy. Usługa Azure Machine Learning używa konta usługi Azure Storage do przechowywania danych i modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296945"
---
# <a name="regenerate-storage-account-access-keys"></a>Ponowne generowanie kluczy dostępu do konta magazynu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak zmienić klucze dostępu dla kont usługi Azure Storage używanych przez usługę Azure Machine Learning. Usługa Azure Machine Learning może używać kont magazynu do przechowywania danych lub uczonych modeli.

Ze względów bezpieczeństwa może być konieczna zmiana kluczy dostępu dla konta usługi Azure Storage. Podczas ponownego generowania klucza dostępu usługa Azure Machine Learning musi zostać zaktualizowana w celu użycia nowego klucza. Usługa Azure Machine Learning może używać konta magazynu zarówno dla magazynu modelu, jak i jako magazynu danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego.](how-to-manage-workspace.md)

* Zestaw [SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Fragmenty kodu w tym dokumencie zostały przetestowane w wersji 1.0.83 sdk języka Python.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co należy zaktualizować

Konta magazynu mogą być używane przez obszar roboczy usługi Azure Machine Learning (przechowywanie dzienników, modeli, migawek itp.) i jako magazyn danych. Proces aktualizacji obszaru roboczego jest pojedynczym poleceniem interfejsu wiersza polecenia platformy Azure i można go uruchomić po zaktualizowaniu klucza magazynu. Proces aktualizowania magazynów danych jest bardziej zaangażowany i wymaga odnajdowania, które magazyny danych są obecnie przy użyciu konta magazynu, a następnie ich ponownej rejestracji.

> [!IMPORTANT]
> Zaktualizuj obszar roboczy przy użyciu interfejsu wiersza polecenia platformy Azure i magazynów danych przy użyciu języka Python w tym samym czasie. Aktualizowanie tylko jednego lub drugiego nie jest wystarczające i może powodować błędy, dopóki oba nie zostaną zaktualizowane.

Aby odnajdować konta magazynu używane przez magazyny danych, użyj następującego kodu:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ten kod wyszukuje wszystkie zarejestrowane magazyny danych korzystające z usługi Azure Storage i wyświetla następujące informacje:

* Nazwa magazynu danych: nazwa magazynu danych, w których jest zarejestrowane konto magazynu.
* Nazwa konta magazynu: nazwa konta usługi Azure Storage.
* Kontener: kontener na koncie magazynu, który jest używany przez tę rejestrację.

Wskazuje również, czy magazyn danych jest dla usługi Azure Blob lub udziału plików platformy Azure, ponieważ istnieją różne metody ponownego rejestrowania każdego typu magazynu danych.

Jeśli istnieje wpis dla konta magazynu, dla którego planujesz ponowne generowanie kluczy dostępu, zapisz nazwę magazynu danych, nazwę konta magazynu i nazwę kontenera.

## <a name="update-the-access-key"></a>Aktualizowanie klucza dostępu

Aby zaktualizować usługę Azure Machine Learning w celu użycia nowego klucza, należy wykonać następujące kroki:

> [!IMPORTANT]
> Wykonaj wszystkie kroki, aktualizując zarówno obszar roboczy przy użyciu interfejsu wiersza polecenia, jak i magazynów danych przy użyciu języka Python. Aktualizowanie tylko jednego lub drugiego może powodować błędy, dopóki oba nie zostaną zaktualizowane.

1. Ponownie wygeneruj klucz. Aby uzyskać informacje dotyczące ponownego generowania klucza dostępu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). Zapisz nowy klucz.

1. Aby zaktualizować obszar roboczy w celu użycia nowego klucza, należy wykonać następujące czynności:

    1. Aby zalogować się do subskrypcji platformy Azure, która zawiera obszar roboczy przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Aby zaktualizować obszar roboczy w celu użycia nowego klucza, użyj następującego polecenia. Zamień `myworkspace` nazwę obszaru roboczego usługi `myresourcegroup` Azure Machine Learning i zastąp nazwą grupy zasobów platformy Azure zawierającej obszar roboczy.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        To polecenie automatycznie synchronizuje nowe klucze dla konta magazynu platformy Azure używanego przez obszar roboczy.

1. Aby ponownie zarejestrować magazyny danych korzystające z konta magazynu, należy użyć wartości z sekcji [Co należy zaktualizować](#whattoupdate) i klucza z kroku 1 z następującym kodem:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Ponieważ `overwrite=True` jest określony, ten kod zastępuje istniejącą rejestrację i aktualizuje go do korzystania z nowego klucza.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) rejestrowania magazynów danych, zobacz odwołanie do klasy.
