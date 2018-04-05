---
title: 'Python: operacje zarządzania kontem w usłudze Azure Data Lake Store | Microsoft Docs'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z operacjami zarządzania kontem usługi Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5f060dfd061af07f220576f17110afccd01fdae5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Operacje zarządzania kontem w usłudze Azure Data Lake Store za pomocą języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Dowiedz się, jak używać zestawu SDK języka Python dla usługi Azure Data Lake Store do wykonywania podstawowych operacji zarządzania kontem, takich jak tworzenie konta usługi Data Lake Store, generowanie listy kont usługi Data Lake Store itp. Aby uzyskać instrukcje na temat sposobu wykonywania operacji systemu plików w usłudze Data Lake Store przy użyciu języka Python, zobacz [Operacje systemu plików w usłudze Data Lake Store za pomocą języka Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Grupa zasobów platformy Azure**. Aby uzyskać instrukcje, zobacz [Tworzenie grupy zasobów platformy Azure](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby móc pracować z usługą Data Lake Store przy użyciu języka Python, musisz zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* Moduł `azure-mgmt-datalake-store`, który obejmuje operacje zarządzania kontem usługi Azure Data Lake Store. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu do zarządzania dla usługi Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/sample_azure-mgmt-datalake-store.html).
* Moduł `azure-datalake-store`, który obejmuje operacje systemu plików usługi Azure Data Lake Store. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu systemu plików dla usługi Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
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

* Aby dowiedzieć się więcej o uwierzytelnianiu użytkowników końcowych w aplikacji, zobacz [End-user authentication with Data Lake Store using Python (Uwierzytelnianie użytkowników końcowych w usłudze Data Lake Store za pomocą języka Python)](data-lake-store-end-user-authenticate-python.md).
* Aby dowiedzieć się więcej o uwierzytelnianiu „usługa-usługa” w aplikacji, zobacz [Service-to-service authentication with Data Lake Store using Python (Uwierzytelnianie „usługa-usługa” w usłudze Data Lake Store za pomocą języka Python)](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-store-account"></a>Tworzenie klienta i konta usługi Data Lake Store

Poniższy fragment kodu najpierw tworzy klienta konta usługi Data Lake Store. Używa on obiektu klienta do utworzenia konta usługi Data Lake Store. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Wyświetlanie listy kont usługi Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Usuwanie konta usługi Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Następne kroki
* [Operacje systemu plików w usłudze Data Lake Store za pomocą języka Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zobacz też
* [Azure Data Lake Store Python (Account management) Reference](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html) (Dokumentacja języka Python dla usługi Azure Data Lake Store [zarządzanie kontem])
* [Azure Data Lake Store Python (Filesystem) Reference](http://azure-datalake-store.readthedocs.io/en/latest) (Dokumentacja języka Python dla usługi Azure Data Lake Store [system plików])
* [Open Source Big Data applications compatible with Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) (Aplikacje danych big data typu open source zgodne z usługą Azure Data Lake Store)
