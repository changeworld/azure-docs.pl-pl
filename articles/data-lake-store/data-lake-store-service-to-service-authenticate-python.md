---
title: 'Do usługi uwierzytelniania: Python z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie usługi do usługi z usługi Data Lake Store za pomocą usługi Azure Active Directory przy użyciu języka Python
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: f2ebd9228b9fa7861527dab20277ceb09f61b544
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626236"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Aby usługa uwierzytelniania za pomocą Data Lake Store z użyciem języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

W tym artykule informacje o sposobie używania zestawu SDK Python celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Store. Dla użytkownika końcowego uwierzytelniania za pomocą usługi Data Lake Store za pomocą języka Python, zobacz [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store za pomocą języka Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Należy wykonać czynności opisane w [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

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
    import logging, getpass, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Uwierzytelnianie między usługami z kluczem tajnym klienta w celu zarządzania kontami

Użyj ta Wstawka kodu uwierzytelniania za pomocą usługi Azure AD dla operacji zarządzania kontem w usłudze Data Lake przechowywania, takich jak tworzenie konta usługi Data Lake Store, usuwanie konta usługi Data Lake Store itp. Poniższy fragment kodu może służyć do uwierzytelniania aplikacji nieinteraktywnie, przy użyciu klucza tajnego klienta dla aplikacji / usługi podmiotu zabezpieczeń istniejącej usługi Azure AD "Aplikacja sieci Web" aplikacji.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Uwierzytelnianie między usługami z wpisem tajnym klienta dla operacji systemu plików

Użyj następującego fragmentu kodu do uwierzytelniania w usłudze Azure AD w celu wykonywania operacji w usłudze Data Lake Store, takich jak tworzenie folderów, przekazywanie plików itp. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji w sposób nieinterakcyjny za pomocą klucza tajnego klienta aplikacji / nazwy głównej usługi. Użyj tej metody wraz z istniejącą aplikacją sieci Web usługi Azure AD.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Store za pomocą usługi do uwierzytelniania przy użyciu języka Python. Teraz można przeglądać następujące artykuły, które porozmawiać na temat sposobu korzystania z języka Python do pracy z usługą Azure Data Lake Store.

* [Operacji zarządzania kontem w usłudze Data Lake Store za pomocą języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych w usłudze Data Lake Store za pomocą języka Python](data-lake-store-data-operations-python.md)


