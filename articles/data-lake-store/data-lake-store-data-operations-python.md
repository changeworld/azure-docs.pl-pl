---
title: 'Python: operacje systemu plików w usłudze Azure Data Lake Store | Microsoft Docs'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z systemem plików usługi Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 6e92eed5e4083c9b8063318b2a9139e20f53de1a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-python"></a>Operacje systemu plików w usłudze Azure Data Lake Store przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

W tym artykule dowiesz się, jak używać zestawu SDK języka Python do wykonywania operacji systemu plików w usłudze Azure Data Lake Store. Aby uzyskać instrukcje na temat sposobu wykonywania operacji zarządzania kontem w usłudze Data Lake Store przy użyciu języka Python, zobacz [Account management operations on Data Lake Store using Python (Operacje zarządzania kontem w usłudze Data Lake Store przy użyciu języka Python)](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md).

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

2. Dodaj następujące wiersze, aby zaimportować wymagane moduły

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

## <a name="create-filesystem-client"></a>Tworzenie klienta systemu plików

Poniższy fragment kodu najpierw tworzy klienta konta usługi Data Lake Store. Używa on obiektu klienta do utworzenia konta usługi Data Lake Store. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Tworzenie katalogu

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Przekazywanie pliku


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Pobieranie pliku

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Usuwanie katalogu

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Następne kroki
* [Operacje zarządzania kontem w usłudze Data Lake Store przy użyciu języka Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Zobacz też
* [Azure Data Lake Store Python (Account management) Reference](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html) (Dokumentacja języka Python dla usługi Azure Data Lake Store [zarządzanie kontem])
* [Azure Data Lake Store Python (Filesystem) Reference](http://azure-datalake-store.readthedocs.io/en/latest) (Dokumentacja języka Python dla usługi Azure Data Lake Store [system plików])
* [Open Source Big Data applications compatible with Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) (Aplikacje danych big data typu open source zgodne z usługą Azure Data Lake Store)
