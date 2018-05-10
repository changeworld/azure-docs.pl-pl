---
title: 'Uwierzytelnianie użytkownika końcowego: Python z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie uzyskania uwierzytelniania użytkowników końcowych z Data Lake Store za pomocą usługi Azure Active Directory z języka Python
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7f582683245505fe1ff161e4461dd34690a1ab0a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Store za pomocą języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

W tym artykule informacje o sposobie używania zestawu SDK Python w celu uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Store. Uwierzytelnianie użytkowników końcowych dodatkowo może zostać podzielony na dwie kategorie:

* Uwierzytelnianie użytkownika bez usługi Multi-Factor authentication
* Uwierzytelnianie użytkownika końcowego przy użyciu uwierzytelniania wieloskładnikowego

W tym artykule omówiono obie te opcje. Do uwierzytelnienia do usługi Data Lake Store z użyciem języka Python, zobacz [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Native"**. Należy wykonać czynności opisane w [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby móc pracować z usługą Data Lake Store przy użyciu języka Python, musisz zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* Moduł `azure-mgmt-datalake-store`, który obejmuje operacje zarządzania kontem usługi Azure Data Lake Store. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu do zarządzania dla usługi Azure Data Lake Store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Moduł `azure-datalake-store`, który obejmuje operacje systemu plików usługi Azure Data Lake Store. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu systemu plików dla usługi Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Użyj następujących poleceń, aby zainstalować moduły.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W środowisku IDE wybór, Utwórz nową aplikację języka Python, na przykład **mysample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Uwierzytelnianie użytkownika końcowego przy użyciu uwierzytelniania wieloskładnikowego

### <a name="for-account-management"></a>Do zarządzania kontami

Użyj następującego fragmentu kodu do uwierzytelniania za pomocą usługi Azure AD dla operacji zarządzania konta na konto usługi Data Lake Store. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj wartości poniższych istniejącej usługi Azure AD **natywnego** aplikacji.

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

Służy do uwierzytelniania za pomocą usługi Azure AD w celu wykonywania operacji na koncie usługi Data Lake Store. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj wartości poniższych istniejącej usługi Azure AD **natywnego** aplikacji.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Uwierzytelnianie użytkownika bez usługi Multi-Factor authentication

Ta opcja jest przestarzała. Aby uzyskać więcej informacji, zobacz [uwierzytelniania Azure przy użyciu zestawu SDK Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania użytkowników końcowych do uwierzytelniania w usłudze Azure Data Lake Store za pomocą języka Python. Teraz można przeglądać następujące artykuły, które porozmawiać na temat sposobu korzystania z języka Python do pracy z usługą Azure Data Lake Store.

* [Operacji zarządzania kontem w usłudze Data Lake Store za pomocą języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych w usłudze Data Lake Store za pomocą języka Python](data-lake-store-data-operations-python.md)

