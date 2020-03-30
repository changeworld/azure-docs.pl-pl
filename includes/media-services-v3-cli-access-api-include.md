---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461235"
---
### <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Aby nawiązać połączenie z interfejsami API usługi Azure Media Services, musisz użyć uwierzytelniania związanego z nazwą główną usługi Azure AD. Poniższe polecenie powoduje utworzenie aplikacji usługi Azure AD i dołączenie nazwy głównej usługi do konta. Skonfiguruj aplikację przy użyciu zwróconych wartości.

Przed uruchomieniem skryptu należy `amsaccount` `amsResourceGroup` zastąpić nazwy wybrane podczas tworzenia tych zasobów i z nazwami wybranymi. `amsaccount` to nazwa konta usługi Azure Media Services, do którego zostanie dołączona nazwa główna usługi.

Jeśli masz dostęp do wielu subskrypcji, najpierw ustaw aktywną subskrypcję subskrypcji, w której utworzono konto usługi Media Services.

```azurecli
az account set --subscription subscriptionId
```

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
