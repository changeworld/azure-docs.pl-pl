---
title: Uwierzytelniania opartego na tokenie (HTTP/2) dla usługi APNS w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak korzystać z nowego tokenu uwierzytelniania dla usługi APNS
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: ca86130e9c184576fc44119190d6224a363c6561
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="token-based-http2-authentication-for-apns"></a>Uwierzytelniania opartego na tokenie (HTTP/2) dla usługi APNS
## <a name="overview"></a>Przegląd
Ten artykuł zawiera szczegóły dotyczące sposobu zastosowania nowego protokołu APNS HTTP/2 z uwierzytelniania przy użyciu tokenu.

Najważniejsze zalety stosowania nowy protokół obejmują:
-   Generowania tokenów jest stosunkowo proste wolnego (w porównaniu do certyfikatów)
-   Nie więcej daty wygaśnięcia — są w formancie tokenów uwierzytelniania i ich odwołań
-   Ładunki można teraz maksymalnie 4 KB
- Synchroniczne opinii
-   Możesz teraz najnowsze protokołu firmy Apple — certyfikaty nadal używać protokołu binarny, który jest oznaczony do amortyzacja

Za pomocą tego nowego mechanizmu może odbywać się w dwóch krokach za kilka minut:
1.  Uzyskać niezbędne informacje z portalu Apple Developer konta
2.  Konfigurowanie Centrum powiadomień przy użyciu nowych informacji

Centra powiadomień jest teraz wszystko gotowe do użycia w nowym systemie uwierzytelniania przy użyciu usługi APNS. 

Należy pamiętać, że po migracji z przy użyciu poświadczeń certyfikatu dla usługi APNS:
- token właściwości zastąpić certyfikat w naszym systemie
- Jednak aplikacja będzie nadal otrzymywał powiadomienia bezproblemowo.

## <a name="obtaining-authentication-information-from-apple"></a>Uzyskiwanie informacji o uwierzytelnianiu od firmy Apple
Aby włączyć uwierzytelnianie tokenów, potrzebne są następujące właściwości z konta dewelopera firmy Apple:
### <a name="key-identifier"></a>Identyfikator klucza
Identyfikator klucza można uzyskać ze strony "Klucze" na koncie deweloperów firmy Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identyfikator aplikacji i nazwy aplikacji
Nazwa aplikacji jest dostępna za pośrednictwem strony identyfikatorów aplikacji w ramach konta dewelopera. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Identyfikator aplikacji jest dostępna za pośrednictwem strony szczegółów członkostwa w konta dewelopera.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Token uwierzytelniania
Po wygenerowaniu tokenu dla aplikacji można pobrać tokenu uwierzytelniania. Aby uzyskać więcej informacji na temat generowania tokenu, zapoznaj się [dokumentacja dla deweloperów firmy Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurowanie Centrum powiadomień do uwierzytelniania opartego na tokenach
### <a name="configure-via-the-azure-portal"></a>Konfigurowanie portalu Azure
Aby włączyć token uwierzytelniania przy użyciu w portalu, należy zalogować się do portalu Azure i przejdź do Centrum powiadomień > Usługi powiadomień > APNS panel. 

Brak nową właściwość — *tryb uwierzytelniania*. Wybieranie Token umożliwia zaktualizowanie koncentrator z odpowiednimi tokenu właściwości.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Wprowadź właściwości, które są pobierane z konta dewelopera firmy Apple 
- Wybierz tryb aplikacji (środowisko produkcyjne lub piaskownicy) 
- Kliknij przycisk Zapisz, aby zaktualizować swoje poświadczenia usługi APNS. 

### <a name="configure-via-management-api-rest"></a>Konfigurowanie za pomocą interfejsu API (REST) do zarządzania

Można użyć naszych [API zarządzania](https://msdn.microsoft.com/library/azure/dn495827.aspx) Aby zaktualizować Centrum powiadomień do uwierzytelniania opartego na tokenie.
W zależności od tego, czy w przypadku konfigurowania aplikacji jest aplikacja piaskownicy lub produkcji (określone w danych konta dewelopera Apple) użyj jednej z odpowiednich punktów końcowych:

- Punkt końcowy piaskownicy: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- Punkt końcowy produkcyjnym: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Uwierzytelnianie na podstawie tokenu wymaga wersji interfejsu API: **2017 04 lub nowszym**.
> 
> 

Oto przykład żądanie PUT, aby zaktualizować Centrum przy użyciu uwierzytelniania opartego na tokenie:


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
        

### <a name="configure-via-the-net-sdk"></a>Konfigurowanie za pomocą zestawu .NET SDK
Można skonfigurować do używania uwierzytelniania na podstawie tokenu przy użyciu Centrum naszych [najnowszego klienta SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Oto przykładowy kod, pokazujący poprawne użycie:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Powrót do przy użyciu uwierzytelniania opartego na certyfikatach
Możesz przywrócić w dowolnym momencie przy użyciu uwierzytelniania opartego na certyfikatach przy użyciu dowolnej metody poprzedniego i przekazywanie certyfikatu zamiast tokenu właściwości. Ta akcja spowoduje zastąpienie wcześniej zapisane poświadczenia.
