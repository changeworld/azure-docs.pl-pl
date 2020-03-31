---
title: Znaczenie powiadomień wypychanych w aplikacjach mobilnych z Centrum aplikacji Visual Studio i centrum powiadomień platformy Azure
description: Dowiedz się więcej o usługach, takich jak Visual Studio App Center, których można używać do kontaktów z użytkownikami aplikacji mobilnych.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235330"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Nawiąż kontakt z użytkownikami aplikacji, wysyłając powiadomienia wypychane

Niezależnie od tego, czy budujesz aplikacje konsumenckie, czy dla przedsiębiorstw, chcesz, aby użytkownicy aktywnie używali twojej aplikacji. Często zdarza się, że użytkownik chce udostępniać użytkownikom najświeższe wiadomości, aktualizacje gier, ekscytujące oferty, aktualizacje produktów lub inne istotne informacje. Aby zwiększyć zaangażowanie użytkowników i zachęcić ich do powrotu do aplikacji, potrzebujesz sposobu komunikowania się z użytkownikami w czasie rzeczywistym.

Powiadomienia wypychane zapewniają szybki i wydajny sposób komunikowania się z użytkownikami aplikacji. Możesz skontaktować się z użytkownikami we właściwym czasie i powiadomić ich o żądanych informacjach, zwykle w wyskakującym elemencie lub oknie dialogowym na urządzeniu przenośnym, niezależnie od tego, co robią.

## <a name="value-of-push-notifications"></a>Wartość powiadomień wypychanych
Powiadomienia wypychane zapewniają wartość dla użytkowników i firm.

Firmy mogą:
- Komunikuj się bezpośrednio z użytkownikami, wysyłając wiadomości na obsługiwanych platformach we właściwym czasie.
- Zwiększ zaangażowanie użytkowników, wysyłając użytkownikom aktualizacje i przypomnienia w czasie rzeczywistym, zachęcając ich do regularnego angażowania się w aplikację.
- Zachowaj użytkowników i ponownie zaangażuj się z nieaktywnymi użytkownikami, którzy pobrali aplikację, ale nie używają jej do ponownego aktywowania.
- Zwiększ współczynniki konwersji, analizując zachowanie użytkowników, segmentując użytkowników i wykorzystując kampanie oparte na mobilnych powiadomieniach push.
- Promuj produkty i usługi, wysyłając wiadomości push i terminowe aktualizacje do użytkowników.
- Kieruj reklamy do użytkowników, wysyłając spersonalizowane wiadomości push.

W przypadku użytkowników aplikacji powiadomienia wypychane:
- Podaj wartość i informacje w czasie rzeczywistym.
- Przypomnij użytkownikom o korzystaniu z aplikacji.

Skorzystaj z następujących usług, aby włączyć powiadomienia wypychane w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Dzięki [funkcji App Center Push](/appcenter/push/)możesz wysyłać ukierunkowane wiadomości do użytkowników systemów iOS, Android i Windows bez konieczności zarządzania procesem wysyłania powiadomień do urządzeń za pomocą usług powiadomień wypychanych (PNS). Ta usługa oparta na usługach Azure Notification Hubs eliminuje złożoność związaną z ręcznym wypychaniem powiadomień, udostępniając zaawansowany pulpit nawigacyjny.

**Najważniejsze funkcje**
- Wysyłaj powiadomienia wypychane do urządzeń mobilnych na różnych platformach.
- Powiadomienia służy do wysyłania danych do aplikacji, wyświetlania wiadomości do użytkownika lub wyzwalania akcji przez aplikację.
- Użyj celów powiadomień, aby: 
    - Emituj wiadomości do wszystkich zarejestrowanych urządzeń.
    - Wysyłaj powiadomienia do odbiorców na podstawie informacji o urządzeniu i właściwości niestandardowych.
    - Wysyłaj powiadomienia do określonych użytkowników.
    - Wysyłaj powiadomienia do określonych urządzeń.
- Skorzystaj z danych telemetrycznych na wypychaniach, urządzeniach i błędach dostępnych w portalu Centrum aplikacji.
- Uzyskaj obsługę platformy dla systemów iOS, Android, macOS, Xamarin, React Native, Unity i Cordova.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do aplikacji App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Centra powiadomień](/azure/notification-hubs/notification-hubs-push-notification-overview) zapewniają łatwy w użyciu i skalowany w poziomie silnik wypychania. Można go używać do wysyłania powiadomień do dowolnej platformy i z dowolnego zaplecza w chmurze lub lokalnie.

**Najważniejsze funkcje**
- Wysyłaj powiadomienia wypychań do dowolnej platformy z dowolnego zaplecza, w chmurze lub lokalnie.
- Szybkie wypychanie transmisji do milionów urządzeń mobilnych za pomocą jednego połączenia interfejsu API.
- Dostosuj powiadomienia wypychane według klienta, języka i lokalizacji.
- Dynamicznie definiuj i powiadamiaj segmenty klientów.
- Skaluj się natychmiast do milionów urządzeń mobilnych.
- Uzyskaj obsługę platformy dla systemów iOS, Android, Windows, Kindle i Baidu.
        
**Odwołania**
- [Portal Azure](https://portal.azure.com) 
- [Wprowadzenie do usługi Azure Notification Hubs](/azure/notification-hubs/)
- [Szybki start](/azure/notification-hubs/create-notification-hub-portal)
- [Próbki](/azure/notification-hubs/samples)
