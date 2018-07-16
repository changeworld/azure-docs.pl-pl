---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: f4600413e05950446db71f988c4c4302f0dcacb3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39063075"
---
## <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Aby nawiązać połączenie z interfejsami API usługi Azure Media Services, musisz użyć uwierzytelniania związanego z nazwą główną usługi Azure AD. Poniższe polecenie powoduje utworzenie aplikacji usługi Azure AD i dołączenie nazwy głównej usługi do konta. Skonfiguruj aplikację przy użyciu zwróconych wartości.

Przed uruchomieniem skryptu możesz zastąpić wartości `amsaccount` i `amsResourceGroup` nazwami użytymi podczas tworzenia tych zasobów. `amsaccount` to nazwa konta usługi Azure Media Services, do którego zostanie dołączona nazwa główna usługi.

Następujące polecenie zwraca dane wyjściowe `json`:

```azurecli-interactive
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

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
