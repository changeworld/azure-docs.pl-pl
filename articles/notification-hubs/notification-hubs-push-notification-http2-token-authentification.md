---
title: Uwierzytelnianie oparte na tokenach (HTTP/2) dla usług APNS na platformie Azure Notification Hubs | Microsoft Docs
description: W tym temacie wyjaśniono, jak wykorzystać nowe uwierzytelnianie tokenu dla usługi APNS
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213126"
---
# <a name="token-based-http2-authentication-for-apns"></a>Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS

## <a name="overview"></a>Przegląd

W tym artykule szczegółowo przedstawiono sposób użycia nowego protokołu HTTP/2 APNS z uwierzytelnianiem opartym na tokenach.

Najważniejsze zalety korzystania z nowego protokołu obejmują:

* Generowanie tokenów jest stosunkowo nieodpłatne (w porównaniu z certyfikatami)
* Nie ma więcej dat wygaśnięcia — masz kontrolę nad tokenami uwierzytelniania i ich odwołaniem.
* Ładunki mogą teraz mieć do 4 KB
* Synchroniczna opinia
* Korzystasz z najnowszego protokołu firmy Apple — certyfikaty nadal używają protokołu binarnego, który jest oznaczony jako przestarzały

Za pomocą tego nowego mechanizmu można wykonać dwa kroki w ciągu kilku minut:

1. Uzyskaj niezbędne informacje z portalu konta dewelopera firmy Apple
2. Skonfiguruj centrum powiadomień przy użyciu nowych informacji

Notification Hubs jest teraz ustawiona do korzystania z nowego systemu uwierzytelniania przy użyciu usługi APNS.

Należy pamiętać, że w przypadku migracji z używania poświadczeń certyfikatu dla usługi APNS:

* właściwości tokenu zastępują certyfikat w naszym systemie.
* Jednak aplikacja nadal będzie odbierać powiadomienia bezproblemowo.

## <a name="obtaining-authentication-information-from-apple"></a>Uzyskiwanie informacji o uwierzytelnianiu od firmy Apple

Aby włączyć uwierzytelnianie oparte na tokenach, potrzebne są następujące właściwości konta dewelopera firmy Apple:

### <a name="key-identifier"></a>Identyfikator klucza

Identyfikator klucza można uzyskać na stronie "klucze" na koncie dewelopera firmy Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identyfikator aplikacji & Nazwa aplikacji

Nazwa aplikacji jest dostępna za pośrednictwem strony identyfikatorów aplikacji na koncie dewelopera.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Identyfikator aplikacji jest dostępny za pośrednictwem strony szczegółów członkostwa na koncie dewelopera.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Token uwierzytelniania

Token uwierzytelniania można pobrać po wygenerowaniu tokenu dla aplikacji. Aby uzyskać szczegółowe informacje na temat sposobu generowania tego tokenu, zapoznaj się z [dokumentacją dla deweloperów firmy Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurowanie centrum powiadomień w celu korzystania z uwierzytelniania opartego na tokenach

### <a name="configure-via-the-azure-portal"></a>Skonfiguruj za pomocą Azure Portal

Aby włączyć uwierzytelnianie oparte na tokenach w portalu, zaloguj się do Azure Portal i przejdź do centrum powiadomień > usługi powiadomień > panelu APNS.

Istnieje nowa właściwość — *tryb uwierzytelniania*. Wybranie opcji token umożliwia zaktualizowanie centrum przy użyciu wszystkich odpowiednich właściwości tokenu.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Wprowadź właściwości pobrane z konta dewelopera firmy Apple
* Wybierz tryb aplikacji (w środowisku produkcyjnym lub piaskownicy)
* Kliknij przycisk **Zapisz** , aby zaktualizować poświadczenia usługi APNs

### <a name="configure-via-management-api-rest"></a>Konfigurowanie za pomocą interfejsu API zarządzania (REST)

Do aktualizowania centrum powiadomień w celu korzystania z uwierzytelniania opartego na tokenach można użyć naszych [interfejsów API zarządzania](https://msdn.microsoft.com/library/azure/dn495827.aspx) .
W zależności od tego, czy konfigurowana aplikacja jest piaskownicą lub aplikacją produkcyjną (określoną na koncie dewelopera firmy Apple), użyj jednego z odpowiednich punktów końcowych:

* Punkt końcowy piaskownicy:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Punkt końcowy produkcji:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Uwierzytelnianie oparte na tokenach wymaga wersji interfejsu API: **2017-04 lub nowszy**.

Oto przykład żądania PUT służącego do aktualizowania centrum przy użyciu uwierzytelniania opartego na tokenach:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Konfigurowanie za pomocą zestawu .NET SDK

Można skonfigurować koncentrator tak, aby korzystał z uwierzytelniania opartego na tokenach przy użyciu [najnowszego zestawu SDK klienta](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Oto przykładowy kod ilustrujący poprawne użycie:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Przywracanie do korzystania z uwierzytelniania opartego na certyfikatach

Możesz cofnąć w dowolnym momencie, aby użyć uwierzytelniania opartego na certyfikatach przy użyciu dowolnej metody poprzedzającej i przekazywać certyfikat zamiast właściwości tokenu. Ta akcja spowoduje zastąpienie wcześniej przechowywanych poświadczeń.
