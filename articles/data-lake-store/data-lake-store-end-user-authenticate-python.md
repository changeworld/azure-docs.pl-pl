---
title: 'Uwierzytelnianie użytkowników końcowych: Python za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Active Directory za pomocą języka Python
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
ms.openlocfilehash: 8b72604d7e736230911d0a0987b88d372be4ddf3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881283"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Ten artykuł zawiera informacje o sposobie używania zestawu SDK języka Python w celu uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1. Uwierzytelnianie użytkowników końcowych może zostać dalej podzielony na dwie kategorie:

* Uwierzytelnianie użytkowników końcowych bez usługi Multi-Factor authentication
* Uwierzytelnianie użytkowników końcowych przy użyciu uwierzytelniania wieloskładnikowego

Obie te opcje są omówione w tym artykule. Do usługi do uwierzytelniania przy użyciu Data Lake Storage Gen1 przy użyciu języka Python, zobacz [uwierzytelniania Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Natywnego"**. Zostały wykonane kroki opisane w [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. W ulubionego środowiska IDE, Utwórz nową aplikację języka Python, na przykład **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych przy użyciu uwierzytelniania wieloskładnikowego

### <a name="for-account-management"></a>Do zarządzania kontami

Poniższy fragment kodu umożliwia uwierzytelnianie za pomocą usługi Azure AD dla operacji zarządzania kontem w konta Data Lake Storage Gen1. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. W artości poniżej istniejącej usługi Azure AD **natywnych** aplikacji.

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

### <a name="for-filesystem-operations"></a>Dla operacji systemu plików

Służy do uwierzytelniania w usłudze Azure AD dla operacji systemu plików na koncie Data Lake Storage Gen1. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. W artości poniżej istniejącej usługi Azure AD **natywnych** aplikacji.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych bez usługi Multi-Factor authentication

Ta opcja jest przestarzała. Aby uzyskać więcej informacji, zobacz [uwierzytelniania platformy Azure przy użyciu zestawu SDK języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 za pomocą uwierzytelniania użytkowników końcowych przy użyciu języka Python. Możesz teraz przejrzeć następujące artykuły, które mówić o tym, jak używać języka Python do pracy z usługi Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md)

