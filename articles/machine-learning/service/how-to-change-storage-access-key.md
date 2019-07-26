---
title: Zmień klucze dostępu do konta magazynu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zmienić klucze dostępu dla konta usługi Azure Storage używanego w obszarze roboczym. Usługa Azure Machine Learning używa konta usługi Azure Storage do przechowywania danych i modeli. W przypadku ponownego wygenerowania klucza dostępu dla konta magazynu należy zaktualizować usługę Azure Machine Learning, aby korzystała z nowych kluczy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 0721542811709e9b938fea3f31bc2a0a28ecdc74
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358774"
---
# <a name="regenerate-storage-account-access-keys"></a>Ponowne generowanie kluczy dostępu do konta magazynu

Dowiedz się, jak zmienić klucze dostępu dla kont usługi Azure Storage używanych przez usługę Azure Machine Learning. Azure Machine Learning mogą używać kont magazynu do przechowywania danych lub modeli szkoleniowych.

Ze względów bezpieczeństwa może zajść potrzeba zmiany kluczy dostępu dla konta usługi Azure Storage. Po ponownym wygenerowaniu klucza dostępu Azure Machine Learning należy zaktualizować, aby użyć nowego klucza. Azure Machine Learning może korzystać z konta magazynu zarówno dla magazynu modelowego, jak i magazynu danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](setup-create-workspace.md) .

* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozszerzenie interfejsu wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co należy zaktualizować

Konta magazynu mogą być używane przez obszar roboczy usługi Azure Machine Learning (przechowywanie dzienników, modeli, migawek itp.) oraz magazynu danych. Proces aktualizowania obszaru roboczego to pojedyncze polecenie interfejsu wiersza polecenia platformy Azure, które można wykonać po zaktualizowaniu klucza magazynu. Proces aktualizowania magazynów danych jest większy i wymaga odnajdywania elementów, które są obecnie używane w ramach konta magazynu, a następnie ich ponownego rejestrowania.

> [!IMPORTANT]
> Zaktualizuj obszar roboczy przy użyciu interfejsu wiersza polecenia platformy Azure i magazynów danych przy użyciu języka Python w tym samym czasie. Aktualizowanie tylko jednej lub drugiej nie jest wystarczające i może spowodować błędy do momentu zaktualizowania obu tych wartości.

Aby odnaleźć konta magazynu, które są używane przez magazyny danych, użyj następującego kodu:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ten kod wyszukuje wszystkie zarejestrowane magazyny danych korzystające z usługi Azure Storage i wyświetla następujące informacje:

* Nazwa magazynu danych: Nazwa magazynu danych, w ramach którego zarejestrowano konto magazynu.
* Nazwa konta magazynu: Nazwa konta usługi Azure Storage.
* Wbudowane Kontener na koncie magazynu, który jest używany przez tę rejestrację.

Jeśli istnieje wpis dla konta magazynu, w którym planujesz ponownie wygenerować klucze dostępu, Zapisz nazwę magazynu danych, nazwę konta magazynu i nazwę kontenera.

## <a name="update-the-access-key"></a>Aktualizowanie klucza dostępu

Aby zaktualizować usługę Azure Machine Learning w celu używania nowego klucza, wykonaj następujące czynności:

> [!IMPORTANT]
> Wykonaj wszystkie kroki, zaktualizuj oba obszary robocze przy użyciu interfejsu wiersza polecenia i magazynów danych przy użyciu języka Python. Aktualizacja tylko jednego lub drugiego może spowodować błędy do momentu zaktualizowania obu tych metod.

1. Wygeneruj ponownie klucz. Aby uzyskać informacje na temat ponownego generowania klucza dostępu, zobacz artykuł [Zarządzanie kontem magazynu](/azure/storage/common/storage-account-manage#access-keys) . Zapisz nowy klucz.

1. Aby zaktualizować obszar roboczy pod kątem używania nowego klucza, wykonaj następujące czynności:

    1. Aby zalogować się do subskrypcji platformy Azure, która zawiera obszar roboczy, za pomocą następującego polecenia platformy Azure:

        ```azurecli-interactive
        az login
        ```

    1. Aby zainstalować rozszerzenie Azure Machine Learning, użyj następującego polecenia:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Aby zaktualizować obszar roboczy pod kątem używania nowego klucza, użyj następującego polecenia. Zastąp `myworkspace` ciąg nazwą obszaru roboczego Azure Machine Learning i Zamień `myresourcegroup` na nazwę grupy zasobów platformy Azure, która zawiera obszar roboczy.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        To polecenie automatycznie synchronizuje nowe klucze dla konta usługi Azure Storage używanego w obszarze roboczym.

1. Aby ponownie zarejestrować magazyny danych korzystające z konta magazynu, użyj wartości z sekcji [co należy zaktualizować](#whattoupdate) , a klucz z kroku 1 z następującym kodem:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Ponieważ `overwrite=True` został określony, ten kod zastępuje istniejącą rejestrację i aktualizuje ją w celu użycia nowego klucza.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rejestrowania magazynów danych, [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) Zobacz odwołanie do klasy.
