---
title: Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak korzystać z nowych tokenów uwierzytelniania dla usługi APNS
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872374"
---
# <a name="token-based-http2-authentication-for-apns"></a>Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS

## <a name="overview"></a>Omówienie

W tym artykule opisano, jak nowy protokół HTTP/2 usługi APNS za pomocą uwierzytelniania na podstawie tokenu.

Najważniejsze zalety stosowania nowy protokół obejmują:

* Generowanie tokenu jest stosunkowo trudności związanych ze skomplikowanymi bezpłatna (w porównaniu do certyfikatów)
* Nie więcej daty wygaśnięcia — Ty masz kontrolę tokenów uwierzytelniania i ich odwołań
* Ładunki można teraz maksymalnie 4 KB
* Synchroniczne opinii
* Możesz teraz protokół najnowsze firmy Apple — certyfikaty nadal używać protokołu binarny, który jest oznaczony do wycofania z użycia

Za pomocą tego nowego mechanizmu może odbywać się w dwóch krokach za kilka minut:

1. Uzyskać niezbędne informacje w portalu konta usługi dla deweloperów firmy Apple
2. Konfigurowanie Centrum powiadomień przy użyciu nowych informacji

Usługa Notification Hubs jest teraz gotowi do nowego systemu uwierzytelniania za pomocą usługi APNS.

Należy pamiętać, że w przypadku migracji z przy użyciu poświadczeń certyfikatu dla usługi APNS:

* właściwości tokenu zastąpić certyfikat w naszym systemie
* Jednak aplikacja będzie nadal otrzymywał powiadomienia bezproblemowo.

## <a name="obtaining-authentication-information-from-apple"></a>Uzyskiwanie informacji uwierzytelniania od firmy Apple

Aby włączyć uwierzytelnianie oparte na tokenie, potrzebne są następujące właściwości ze swojego konta dewelopera firmy Apple:

### <a name="key-identifier"></a>Identyfikator klucza

Identyfikator klucza można uzyskać ze strony "Klucze" w ramach Twojego konta dewelopera firmy Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identyfikator aplikacji i nazwy aplikacji

Nazwa aplikacji jest dostępna za pośrednictwem strony identyfikatory aplikacji w ramach konta dewelopera.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Identyfikator aplikacji jest dostępna za pośrednictwem strony szczegółów w członkostwie konta dewelopera.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Token uwierzytelniania

Po wygenerowaniu tokenu dla aplikacji, można pobrać tokenu uwierzytelniania. Aby uzyskać szczegółowe informacje na temat generowania tego tokenu, zapoznaj się [dokumentacji dla deweloperów firmy Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurowanie Centrum powiadomień do korzystania z uwierzytelniania opartego na tokenach

### <a name="configure-via-the-azure-portal"></a>Konfigurowanie w witrynie Azure portal

Aby włączyć uwierzytelniania przy użyciu tokenu w portalu, zaloguj się do witryny Azure portal i przejdź do Centrum powiadomień > Usługi powiadomień > panel usługi APNS.

Istnieje nowa właściwość — *tryb uwierzytelniania*. Wybieranie tokenu umożliwia zaktualizowanie Centrum przy użyciu odpowiedniego tokenu właściwości.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Wprowadź właściwości, które są pobierane z konta dewelopera firmy Apple
* Wybierz swoje tryb aplikacji (w środowisku produkcyjnym lub piaskownicy)
* Kliknij przycisk **Zapisz** przycisk, aby zaktualizować swoje poświadczenia usługi APNS

### <a name="configure-via-management-api-rest"></a>Skonfiguruj za pomocą interfejsu API (REST) do zarządzania

Możesz użyć naszych [interfejsów API zarządzania](https://msdn.microsoft.com/library/azure/dn495827.aspx) można zaktualizować Twoje Centrum powiadomień, aby używać uwierzytelniania opartego na tokenach.
W zależności od tego, czy aplikacja, którą konfigurujesz jest to piaskownica lub produkcja aplikacja (określony w ramach Twojego konta dewelopera firmy Apple) należy użyć jednego z odpowiednich punktów końcowych:

* Punkt końcowy piaskownicy: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produkcyjny punkt końcowy: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Uwierzytelnianie oparte na tokenie wymaga wersję interfejsu API: **2017-04 lub nowszego**.

Oto przykład żądania PUT, aby zaktualizować Centrum przy użyciu uwierzytelniania opartego na tokenach:

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

### <a name="configure-via-the-net-sdk"></a>Skonfiguruj za pomocą zestawu .NET SDK

Można skonfigurować Centrum do użycia przy użyciu tokenu uwierzytelniania przy użyciu naszego [najnowszy zestaw SDK klienta](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Poniżej przedstawiono przykładowy kod pokazujący poprawne użycie:

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

## <a name="reverting-to-using-certificate-based-authentication"></a>Powracanie do korzystania z uwierzytelniania opartego na certyfikatach

Możesz przywrócić w dowolnym momencie przy użyciu uwierzytelniania opartego na certyfikatach, używając dowolnej metody poprzedniego i przekazywanie certyfikatu zamiast właściwości tokenu. Ta akcja zastępuje wcześniej zapisane poświadczenia.
