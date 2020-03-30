---
title: 'Uwierzytelnianie użytkowników końcowych: Python z usługą Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak osiągnąć uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory w języku Python
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c69f6c1f587285c5c52280c4c49008764d5b20d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265599"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

W tym artykule dowiesz się, jak używać zestawu SDK języka Python do uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1. Uwierzytelnianie użytkowników końcowych można dalej podzielić na dwie kategorie:

* Uwierzytelnianie użytkowników końcowych bez uwierzytelniania wieloskładnikowego
* Uwierzytelnianie użytkowników końcowych z uwierzytelnianiem wieloskładnikowym

Obie te opcje zostały omówione w tym artykule. Aby uzyskać uwierzytelnianie między usługami przy użyciu usługi Lake Storage Gen1 przy użyciu języka Python, zobacz [Uwierzytelnianie usługi dla usługi za pomocą usługi Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz "natywną" aplikację usługi Azure Active Directory**. Kroki w uwierzytelnianiu użytkowników końcowych za [pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. W wybranym przez użytkownika języku IDE utwórz nową aplikację Języka Python, na przykład **mysample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych z uwierzytelnianiem wieloskładnikowym

### <a name="for-account-management"></a>Do zarządzania kontem

Poniższy fragment kodu służy do uwierzytelniania przy użyciu usługi Azure AD dla operacji zarządzania kontami na koncie Usługi Data Lake Storage Gen1. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj poniższe wartości dla istniejącej aplikacji **natywnej** usługi Azure AD.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Do operacji w systemie plików

Służy do uwierzytelniania przy użyciu usługi Azure AD dla operacji systemu plików na koncie Gen1 magazynu usługi Data Lake. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj poniższe wartości dla istniejącej aplikacji **natywnej** usługi Azure AD.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych bez uwierzytelniania wieloskładnikowego

Jest to przestarzałe. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie platformy Azure przy użyciu zestawu SDK języka Python](/azure/python/python-sdk-azure-authenticate).
   
## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak używać uwierzytelniania użytkowników końcowych do uwierzytelniania przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu języka Python. Teraz możesz przeglądać następujące artykuły, które mówią o tym, jak używać języka Python do pracy z usługą Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontami w umiaru Danych Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md)
* [Operacje danych w pamięci masowej danych Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md)

