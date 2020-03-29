---
title: Łączenie się z interfejsem API usługi Azure Media Services w wersji 3 — Python
description: W tym artykule pokazano, jak połączyć się z interfejsem API usługi Media Services w wersji 3 za pomocą języka Python.
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
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888465"
---
# <a name="connect-to-media-services-v3-api---python"></a>Łączenie się z interfejsem API usługi Media Services w wersji 3 — Python

W tym artykule pokazano, jak połączyć się z zestawem SDK języka Python usługi Azure Media Services w wersji 3 przy użyciu metody logowania jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz Pythona z [python.org](https://www.python.org/downloads/)
- Upewnij się, `PATH` że ustawiono zmienną środowiskową
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services.
- Wykonaj kroki opisane w temacie [Interfejsy API programu Access.](access-api-cli-how-to.md) Zarejestruj identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, który jest potrzebny w późniejszym kroku.

> [!IMPORTANT]
> Przejrzyj [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z usługą Azure Media Services przy użyciu języka Python, należy zainstalować te moduły.

* Moduł, `azure-mgmt-resource` który zawiera moduły platformy Azure dla usługi Active Directory.
* Moduł, `azure-mgmt-media` który obejmuje encje usługi Media Services.

Otwórz narzędzie wiersza polecenia i użyj następujących poleceń, aby zainstalować moduły.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Łączenie się z klientem języka Python

1. Tworzenie pliku z `.py` rozszerzeniem
1. Otwieranie pliku w ulubionym edytorze
1. Dodaj kod, który następuje do pliku. Kod importuje wymagane moduły i tworzy obiekt poświadczeń usługi Active Directory, który jest potrzebny do nawiązania połączenia z usługą Media Services.

      Ustawianie wartości zmiennych na wartości dodane z [interfejsów API programu Access](access-api-cli-how-to.md)

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

1. Uruchamianie pliku

## <a name="next-steps"></a>Następne kroki

- Użyj [pliku Python SDK](https://aka.ms/ams-v3-python-sdk).
- Przejrzyj [dokumentację dla języka Python](https://aka.ms/ams-v3-python-ref) usługi Media Services.
