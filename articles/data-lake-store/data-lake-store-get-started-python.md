---
title: 'Python: Operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z operacjami zarządzania kontami usługi Azure Data Lake Storage Gen1.
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
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76290618"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [INTERFEJS API ODPOCZYNKU](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Dowiedz się, jak używać zestawu SDK języka Python dla usługi Azure Data Lake Storage Gen1 do wykonywania podstawowych operacji zarządzania kontami, takich jak tworzenie konta Data Lake Storage Gen1, lista kont Data Lake Storage Gen1 itp. Aby uzyskać instrukcje dotyczące wykonywania operacji systemu plików w systemie danych Lake Storage Gen1 przy użyciu języka Python, zobacz [Operacje systemu plików w pamięci magazynu danych Lake Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Grupa zasobów platformy Azure**. Aby uzyskać instrukcje, zobacz [Tworzenie grupy zasobów platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z magazynem danych Data Lake Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* Moduł, `azure-mgmt-datalake-store` który obejmuje operacje zarządzania kontem usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołanie do modułu zarządzania usługi Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* Moduł, `azure-datalake-store` który obejmuje operacje systemu plików Usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołanie do modułu systemu plików azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

## <a name="authentication"></a>Uwierzytelnianie

W tej sekcji omówione zostaną różne sposoby uwierzytelniania w usłudze Azure AD. Dostępne opcje:

* Aby uzyskać uwierzytelnianie użytkowników końcowych dla aplikacji, zobacz [Uwierzytelnianie użytkowników końcowych za pomocą usługi Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Tworzenie konta klienta i usługi Data Lake Storage Gen1

Poniższy fragment kodu najpierw tworzy klienta konta Usługi Data Lake Storage Gen1. Używa obiektu klienta do utworzenia konta Usługi Data Lake Storage Gen1. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Wyświetlanie listy kont Gen1 magazynu usługi Data Lake

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Następne kroki
* [Operacje systemu plików w pamięci magazynu danych Lake Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zobacz też

* [odwołanie do języka Python (Filesystem) azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikacje Open Source Big Data zgodne z usługą Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
