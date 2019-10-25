---
title: Ważność powiadomień wypychanych w aplikacjach mobilnych przy użyciu Visual Studio App Center i platformy Azure Notification Hubs
description: Poznaj usługi, takie jak App Center, które umożliwiają zaangażowanie użytkowników aplikacji mobilnych.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795915"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Zaangażuj użytkowników aplikacji, wysyłając powiadomienia wypychane 

Niezależnie od tego, czy tworzysz aplikacje dla klientów, czy dla przedsiębiorstw, chcesz aktywnie korzystać z aplikacji. Często zdarza się, że chcesz udostępnić użytkownikom najświeższe wiadomości, aktualizacje gier, atrakcyjne oferty, aktualizację produktu lub wszelkie istotne informacje. Aby zwiększyć zaangażowanie z użytkownikami i zapewnić ich powrót do swojej aplikacji, musisz mieć możliwość komunikowania się z użytkownikami w czasie rzeczywistym.

Powiadomienia wypychane zapewniają szybki i wydajny sposób komunikowania się z użytkownikami aplikacji. Można skontaktować się z użytkownikami w odpowiednim czasie i może powiadomić o żądanych informacjach, zazwyczaj w podręcznym lub oknie dialogowym na urządzeniu przenośnym, niezależnie od ich działania.

## <a name="value-of-push-notifications"></a>Wartość powiadomień wypychanych
Powiadomienia wypychane zawierają wartości zarówno dla użytkowników końcowych, jak i dla firmy.

Firmy mogą:
- **Komunikuj się bezpośrednio z użytkownikami** , wysyłając im komunikaty na obsługiwanej platformie w odpowiednim czasie.
- **Zwiększ zaangażowanie użytkowników** , wysyłając do użytkowników aktualizacje i przypomnienia w czasie rzeczywistym, zachęcając do regularnego angażowania się w aplikację.
- **Zachowaj użytkowników** i ponownie Zaangażuj się z nieaktywnymi użytkownikami, którzy pobrali aplikację, ale nie używają jej do ponownego uaktywnienia.
- **Zwiększ szybkość konwersji** , analizując zachowanie użytkowników końcowych, segmentację użytkowników końcowych i wykorzystując kampanie na podstawie mobilnych powiadomień wypychanych.
- **Promuj produkty i usługi** przez wysyłanie komunikatów wypychanych i okresowych aktualizacji do użytkowników.
- **Kierowanie użytkowników** przez wysyłanie spersonalizowanych komunikatów wypychanych.

Dla użytkowników aplikacji powiadomienia wypychane:
- **Podaj wartość i informacje** w czasie rzeczywistym.
- **Przypomnij użytkownikom** , aby korzystali z aplikacji.

Użyj następujących usług, aby włączyć powiadomienia wypychane w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
Usługa [wypychania App Center](/appcenter/push/) umożliwia kontaktowanie się z użytkownikami przez wysyłanie do nich komunikatów skierowanych do użytkowników systemów iOS, Android i Windows bez konieczności zarządzania procesem wysyłania powiadomień do urządzeń za pomocą usług powiadomień wypychanych (PNS). W oparciu o Notification Hubs platformy Azure usługa ta eliminuje złożone funkcje związane z wypychaniem powiadomień ręcznie, dostarczając zaawansowany pulpit nawigacyjny.

**Najważniejsze funkcje**
- **Wysyłanie powiadomień wypychanych do urządzeń przenośnych** na różnych platformach.
- Używaj powiadomień do wysyłania danych do aplikacji, wyświetlania komunikatu dla użytkownika lub wyzwalania akcji przez aplikację.
- Cele powiadomień: 
    - Wysyłaj komunikaty emisji do **wszystkich zarejestrowanych urządzeń**.
    - Wysyłaj powiadomienia do **odbiorców** utworzonych na podstawie informacji o urządzeniu i właściwości niestandardowych.
    - Wysyłaj powiadomienia do **określonych użytkowników**.
    - Wysyłaj powiadomienia do **określonych urządzeń**.
- **Bogate dane telemetryczne** dotyczące wypychania, urządzenia, błędu są dostępne w portalu App Center.
- **Obsługa platform** — iOS, Android, MacOS, Xamarin, system reaguje na natywny, Unity, Cordova.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do App Center wypychania](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) zapewnić łatwy w użyciu i skalowany aparat wypychania, który umożliwia wysyłanie powiadomień do dowolnej platformy i z dowolnego zaplecza (w chmurze lub lokalnie).

**Najważniejsze funkcje**
- **Wysyłać powiadomienia wypychane** na dowolną platformę z dowolnego zaplecza, w chmurze lub lokalnie.
- **Szybka emisja** wypychana do milionów urządzeń przenośnych przy użyciu jednego wywołania interfejsu API.
- Dostosowywanie powiadomień wypychanych według klienta, języka i lokalizacji.
- Dynamiczne Definiowanie i powiadamianie **segmentów klientów**.
- **Natychmiastowe skalowanie** do milionów urządzeń przenośnych.
- **Obsługa platform** — iOS, Android, Windows, Kindle, Baidu.
        
**Wołują**
- [Azure Portal](https://portal.azure.com) 
- [Wprowadzenie do usługi Azure Notification Hubs](/azure/notification-hubs/)   
- [Przewodniki Szybki start](/azure/notification-hubs/create-notification-hub-portal)
- [Przykłady](/azure/notification-hubs/samples)
