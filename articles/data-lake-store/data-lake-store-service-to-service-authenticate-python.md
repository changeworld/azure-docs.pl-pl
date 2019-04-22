---
title: 'Service-to-service authentication: Python za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnąć service to service uwierzytelnianie za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory przy użyciu języka Python
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
ms.openlocfilehash: 84b7fac10374c1c8f23d17ad775d522b4cb261e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877758"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Uwierzytelnianie Service to service za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Ten artykuł zawiera informacje o sposobie używania zestawu SDK języka Python w celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1. Aby uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Zostały wykonane kroki opisane w [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z Data Lake Storage Gen1 przy użyciu języka Python, musisz zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store` Moduł, który obejmuje operacje zarządzania kontem Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołania do modułu usługi Azure Data Lake magazynu Gen1 Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Moduł, który obejmuje operacje systemu plików Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [dokumentację modułu systemu plików azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
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

Za pomocą tego fragmentu kodu do uwierzytelniania za pomocą usługi Azure AD, dla operacji zarządzania kontem w Data Lake Storage Gen1, takie jak tworzenie konta Data Lake Storage Gen1, konto usuwania Gen1 Data Lake Storage itp. Poniższy fragment kodu służy do uwierzytelniania aplikacji w sposób nieinterakcyjny za pomocą klucza tajnego klienta dla aplikacji / jednostki usługi z istniejącej usługi Azure AD "Aplikacja sieci Web" aplikacji.

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

Użyj poniższy fragment kodu do uwierzytelniania za pomocą usługi Azure AD dla operacji systemu plików w Data Lake Storage Gen1, takie jak tworzenie folderów, przekazywanie pliku itp. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji w sposób nieinterakcyjny za pomocą klucza tajnego klienta aplikacji / nazwy głównej usługi. Użyj tej metody wraz z istniejącą aplikacją internetową usługi Azure AD.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

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
W tym artykule przedstawiono sposób uwierzytelniania w usłudze Data Lake Storage Gen1 za pomocą usługi do uwierzytelniania przy użyciu języka Python. Możesz teraz przejrzeć następujące artykuły, które mówić o tym, jak używać języka Python do pracy z Data Lake Storage Gen1.

* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md)


