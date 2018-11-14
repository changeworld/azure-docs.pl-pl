---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616643"
---
## <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Aby nawiązać połączenie z interfejsami API usługi Azure Media Services, musisz użyć uwierzytelniania związanego z nazwą główną usługi Azure AD. Poniższe polecenie powoduje utworzenie aplikacji usługi Azure AD i dołączenie nazwy głównej usługi do konta. Skonfiguruj aplikację przy użyciu zwróconych wartości.

Przed uruchomieniem skryptu możesz zastąpić wartości `amsaccount` i `amsResourceGroup` nazwami użytymi podczas tworzenia tych zasobów. `amsaccount` to nazwa konta usługi Azure Media Services, do którego zostanie dołączona nazwa główna usługi.

Następujące polecenie zwraca dane wyjściowe `json`:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

To polecenie powoduje wyświetlenie odpowiedzi podobnej do poniższej:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Jeśli w odpowiedzi chcesz uzyskać ciąg `xml`, użyj następującego polecenia:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
