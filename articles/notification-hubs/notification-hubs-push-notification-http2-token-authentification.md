---
title: Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać nowego uwierzytelniania tokenu dla aplikacji APNS.
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
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263816"
---
# <a name="token-based-http2-authentication-for-apns"></a>Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak używać nowego protokołu HTTP/2 usługi APNS z uwierzytelnianiem opartym na tokenie.

Najważniejsze korzyści wynikające z używania nowego protokołu obejmują:

* Generowanie tokenów jest stosunkowo proste (w porównaniu z certyfikatami)
* Koniec z datami wygaśnięcia – masz kontrolę nad tokenami uwierzytelniania i ich cofnięciem
* Ładowność może teraz mieć do 4 KB
* Synchroniczne sprzężenie zwrotne
* Jesteś na najnowszym protokole Apple - certyfikaty nadal używają protokołu binarnego, który jest oznaczony do wycofania

Za pomocą tego nowego mechanizmu można wykonać w dwóch etapach:

* Uzyskaj niezbędne informacje z portalu konta dewelopera Apple.
* Skonfiguruj centrum powiadomień za pomocą nowych informacji.

Centra powiadomień są teraz skonfigurowane do używania nowego systemu uwierzytelniania z systemem APNS.

Należy zauważyć, że w przypadku migracji z przy użyciu poświadczeń certyfikatu dla usługi APNS właściwości tokenu zastępują certyfikat w naszym systemie, ale aplikacja nadal będzie bezproblemowo otrzymywać powiadomienia.

## <a name="obtaining-authentication-information-from-apple"></a>Uzyskiwanie informacji uwierzytelniających od firmy Apple

Aby włączyć uwierzytelnianie oparte na tokenie, potrzebujesz następujących właściwości z konta dewelopera Apple:

### <a name="key-identifier"></a>Identyfikator klucza

Identyfikator klucza można uzyskać ze strony **Klucze** w obszarze **Certyfikaty, identyfikatory & profile**na koncie dewelopera Apple:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identyfikator aplikacji i nazwa aplikacji

Nazwa i identyfikator aplikacji są również dostępne na stronie **Certyfikaty, identyfikatory & profile** na koncie dewelopera:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurowanie za pomocą narzędzia .NET SDK lub portalu Azure

Centrum można skonfigurować tak, aby używało uwierzytelniania opartego na tokenach przy użyciu naszego [najnowszego sdk klienta](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)lub w witrynie Azure portal. Aby włączyć uwierzytelnianie oparte na tokenie w portalu, zaloguj się do witryny Azure Portal i przejdź do panelu Ustawienia > usługi **Apple (APNS)** centrum powiadomień. Wybierz **token** z **właściwości Tryb uwierzytelniania,** aby zaktualizować koncentrator o wszystkie odpowiednie właściwości tokenu.

![Konfigurowanie tokenu](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Wprowadź właściwości pobrane z konta dewelopera Apple.
* Wybierz tryb aplikacji **(Produkcja** lub **Piaskownica).**
* Kliknij przycisk **Zapisz,** aby zaktualizować poświadczenia usługi APNS.

Poświadczenia oparte na tokenie składają się z następujących pól:

* **Identyfikator klucza:** identyfikator klucza prywatnego wygenerowanego w portalu dewelopera Firmy Apple; na przykład `2USFGKSKLT`.
* **Identyfikator zespołu:** nazywany również "Prefiksem" lub "Prefiksem aplikacji". Jest to identyfikator organizacji w portalu Apple Developer; na przykład `S4V3D7CHJR`.
* **Identyfikator pakietu:** nazywany również "Identyfikatorem aplikacji". Jest to identyfikator pakietu dla aplikacji; na przykład `com.microsoft.nhubsample2019`. Pamiętaj, że w wielu aplikacjach można użyć jednego klucza. Ta wartość jest `apns-topic` mapowana do nagłówka HTTP podczas wysyłania powiadomienia i jest używana do kierowania na określoną aplikację.
* **Token:** Nazywany również "kluczem" lub "kluczem prywatnym". Jest to uzyskiwane z pliku p8 wygenerowanego w portalu Apple Developer. Klucz musi mieć włączoną usługę APNS (która jest zaznaczona w portalu Apple Developer podczas generowania klucza). Wartość musi mieć nagłówek/stopkę PEM usunięte z niego po podaniu go do NH Portal/API.
* **Punkt końcowy:** Jest to przełącznik w bloku portalu centrum powiadomień i pole ciągu w interfejsie API. Prawidłowe `https://api.push.apple.com` wartości `https://api.sandbox.push.apple.com`są lub . Centra powiadomień używa tej wartości dla środowiska produkcyjnego lub piaskownicy do wysyłania powiadomień. Musi to `aps-environment` odpowiadać uprawnieniu w aplikacji, w przeciwnym razie tokeny urządzenia APNS generowane nie są zgodne ze środowiskiem, a powiadomienia nie mogą być wysyłane.

Oto przykład kodu ilustrujący prawidłowe użycie:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie centrum powiadomień platformy Azure w witrynie Azure Portal](create-notification-hub-portal.md)
* [Konfigurowanie centrum powiadomień w witrynie Azure portal](create-notification-hub-portal.md)
