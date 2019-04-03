---
title: 'Python: Operacje zarządzania kontem w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z operacji zarządzania kontem usługi Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b6ef5a5c12bb766fb7106d5c7a8189c4b92980d2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880206"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operacje zarządzania kontem w usłudze Azure Data Lake magazynu Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Dowiedz się, jak używać zestawu SDK języka Python dla usługi Azure Data Lake Storage Gen1 do wykonywania podstawowych operacji zarządzania, takie jak tworzenie konta Data Lake Storage Gen1, Data Lake Storage Gen1 wyświetlanie listy kont itd konta. Aby uzyskać instrukcje na temat sposobu wykonywania operacji systemu plików na Data Lake Storage Gen1 przy użyciu języka Python, zobacz [operacje systemu plików w Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Grupa zasobów platformy Azure**. Aby uzyskać instrukcje, zobacz [Tworzenie grupy zasobów platformy Azure](../azure-resource-manager/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z Data Lake Storage Gen1 przy użyciu języka Python, musisz zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store` Moduł, który obejmuje operacje zarządzania kontem usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołania do modułu usługi Azure Data Lake magazynu Gen1 Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Moduł, który obejmuje operacje systemu plików usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu systemu plików azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Użyj następujących poleceń, aby zainstalować moduły.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="authentication"></a>Authentication

W tej sekcji omówione zostaną różne sposoby uwierzytelniania w usłudze Azure AD. Dostępne opcje:

* Do uwierzytelniania użytkownika końcowego dla aplikacji, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).
* Do uwierzytelniania service to service dla aplikacji, zobacz [uwierzytelniania Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Tworzenie klienta i Data Lake Storage Gen1 konta

Poniższy fragment kodu najpierw tworzy klienta konta Data Lake Storage Gen1. Tworzenie konta Data Lake Storage Gen1 używa obiektu klienta. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Wyświetlanie listy kont Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Kolejne kroki
* [Operacje systemu plików w Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zobacz także

* [Dokumentacja języka Python (Filesystem) Azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest)
* [Otwórz źródło danych big Data aplikacji zgodnych z usługi Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
