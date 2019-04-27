---
title: Łączenie usługi Azure Media Services v3 API — Python
description: Dowiedz się, jak nawiązać połączenie usługi Media Services v3 z interfejsem API za pomocą języka Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733100"
---
# <a name="connect-to-media-services-v3-api---python"></a>Łączenie z Media Services v3 API — Python

W tym artykule pokazano, jak połączyć się z zestawu SDK usługi Azure Media Services w wersji 3 języka Python przy użyciu znaku nazwy głównej usługi, w metodzie.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobrać środowisko Python [python.org](https://www.python.org/downloads/)
- Upewnij się ustawić `PATH` zmiennej środowiskowej
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Należy pamiętać, nazwę grupy zasobów i nazwę konta usługi Media Services.
- Postępuj zgodnie z instrukcjami w [dostęp do interfejsów API](access-api-cli-how-to.md) tematu. Zapisz identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, które są potrzebne w kolejnym kroku.

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z usługi Azure Media Services przy użyciu języka Python, musisz zainstalować moduły.

* `azure-mgmt-resource` Moduł, który obejmuje moduły platformy Azure dla usługi Active Directory.
* `azure-mgmt-media` Moduł, który zawiera jednostki usługi Media Services.

Otwórz narzędzie wiersza polecenia i użyj następujących poleceń, aby zainstalować moduły.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Nawiązywać połączenia z klientem języka Python

1. Utwórz plik o `.py` rozszerzenia
1. Otwórz plik w ulubionym edytorze
1. Dodaj kod, znajdujący się w pliku. Kod importuje wymagane moduły i tworzy obiekt poświadczeń usługi Active Directory, czego potrzebujesz do łączenia z usługą Media Services.

      Ustaw wartości zmiennych uzyskana z [dostęp do interfejsów API](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Uruchom plik

## <a name="next-steps"></a>Kolejne kroki

- Użyj [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Przejrzyj [dokumentację dla języka Python](https://aka.ms/ams-v3-python-ref) usługi Media Services.
