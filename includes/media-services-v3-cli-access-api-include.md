---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309079"
---
## <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Aby nawiązać połączenie z interfejsami API usługi Azure Media Services, musisz użyć uwierzytelniania związanego z nazwą główną usługi Azure AD. Poniższe polecenie powoduje utworzenie aplikacji usługi Azure AD i dołączenie nazwy głównej usługi do konta. Skonfiguruj aplikację przy użyciu zwróconych wartości.

Przed uruchomieniem skryptu możesz zastąpić wartości `amsaccount` i `amsResourceGroup` nazwami użytymi podczas tworzenia tych zasobów. `amsaccount` to nazwa konta usługi Azure Media Services, do którego zostanie dołączona nazwa główna usługi.

Następujące polecenie zwraca dane wyjściowe `json`:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

To polecenie powoduje wyświetlenie odpowiedzi podobnej do poniższej:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Jeśli w odpowiedzi chcesz uzyskać ciąg `xml`, użyj następującego polecenia:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
