---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
## <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Aby nawiązać połączenie z interfejsami API usługi Azure Media Services, musisz użyć uwierzytelniania związanego z nazwą główną usługi Azure AD. Poniższe polecenie powoduje utworzenie aplikacji usługi Azure AD i dołączenie nazwy głównej usługi do konta. Zwróconych wartości użyjesz do skonfigurowania aplikacji .NET — jak pokazano to w następnym kroku.

Przed uruchomieniem skryptu możesz zastąpić wartości `amsaccount` i `amsResourceGroup` nazwami użytymi podczas tworzenia tych zasobów. `amsaccount` to nazwa konta usługi Azure Media Services, do którego zostanie dołączona nazwa główna usługi. <br/>W poniższym poleceniu użyta jest opcja `xml` powodująca zwrócenie kodu XML, który można wkleić do pliku app.config. W przypadku pominięcia opcji `xml` odpowiedź zostanie zapisana w formacie `json`.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

Polecenie to spowoduje wyświetlenie odpowiedzi podobnej do poniższej:

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>Konfigurowanie przykładowej aplikacji

Aby uruchomić aplikację i uzyskać dostęp do interfejsów API usługi Media Services, musisz określić prawidłowe wartości dostępu w pliku App.config. 

1. Otwórz program Visual Studio.
2. Przejdź do sklonowanego rozwiązania.
3. W Eksploratorze rozwiązań rozwiń projekt *EncodeAndStreamFiles*.
4. Ustaw ten projekt jako startowy.
5. Otwórz plik App.config.
6. Zastąp wartości appSettings wartościami uzyskanymi w poprzednim kroku.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Naciśnij klawisze CTRL+SHIFT+B, aby skompilować rozwiązanie.
