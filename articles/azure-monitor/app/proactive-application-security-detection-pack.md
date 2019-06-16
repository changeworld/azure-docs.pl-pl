---
title: Inteligentne wykrywanie - Pack wykrywania zabezpieczeń za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji za pomocą usługi Azure Application Insights dla potencjalnych problemów z zabezpieczeniami.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 90d58d1b22e893e922aa0f3770198fc95f539419
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572886"
---
# <a name="application-security-detection-pack-preview"></a>Pakiet wykrywania zabezpieczeń aplikacji (wersja zapoznawcza)

Usługa Application Insights analizuje dane telemetryczne wygenerowane przez aplikację i automatycznie wykrywa potencjalne problemy z zabezpieczeniami. Ta funkcja pozwala zidentyfikować potencjalne problemy z zabezpieczeniami i je obsłużyć, napraw aplikację, lub przez pobranie środki bezpieczeństwa niezbędne.

Ta funkcja wymaga nie specjalne ustawienia innego niż [konfigurowania aplikacji w celu wysyłania danych telemetrycznych](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Gdy otrzyma ten typ powiadomienia wykrywania inteligentnego
Istnieją trzy rodzaje problemów z zabezpieczeniami, które są wykrywane:
1. Do niezabezpieczonych adresów URL: adres URL w aplikacji jest uzyskiwany za pośrednictwem protokołów HTTP i HTTPS. Zazwyczaj adres URL, który akceptuje żądania HTTPS nie powinna obsługiwać żądań HTTP. Może to oznaczać błąd lub zabezpieczeń problem w aplikacji.
2. Formularz niezabezpieczone: formularza (lub inne żądanie "POST") w aplikacji korzysta z protokołu HTTP zamiast HTTPS. Przy użyciu protokołu HTTP mogą wpłynąć na dane użytkownika, które są wysyłane przez formularz.
3. Podejrzanej działalności użytkownika: aplikacja jest uzyskiwany z wielu krajów/regionów przez tego samego użytkownika w tym samym czasie. Na przykład tego samego użytkownika uzyskać dostępu do aplikacji z Hiszpanii i Stanach Zjednoczonych w ciągu jednej godziny. Wykrywanie wskazuje próba potencjalnie złośliwego dostępu do aplikacji.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Moja aplikacja zdecydowanie ma problem z zabezpieczeniami?
Nie, powiadomienia nie oznacza, że Twoja aplikacja ma zdecydowanie problem z zabezpieczeniami. Wykrywanie żadnej z powyższych scenariuszy może w wielu przypadkach wskazywać problem z zabezpieczeniami. Jednak wykrycie może mieć uzasadnienie biznesowe naturalnych i można zignorować.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Jak naprawić wykrywania "Do niezabezpieczonych adresów URL"?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę użytkowników, którzy dostępne niezabezpieczone adresy URL i adresu URL, który został większość wpływ niezabezpieczone dostępu. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Jaki procent użytkowników, dostęp do niezabezpieczone adresy URL? Jak wiele adresów URL miało to wpływ? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera listę żądań niebezpieczne i listę adresów URL i użytkowników, które miały wpływ, aby pomóc w dalszym diagnozowaniu problemu.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Jak naprawić wykrywania "Niezabezpieczone formularza"?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę niezabezpieczone formularzy i liczbę użytkowników, których dane potencjalnie zostało naruszone. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Formularz brał udział w największej liczby niezabezpieczone transmisji i jaki jest podział niezabezpieczone transmisji wraz z upływem czasu? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera listę niezabezpieczone formularzy i podział liczby niezabezpieczone transmisji dla każdego formularza, aby pomóc w dalszym diagnozowaniu problemu.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Jak naprawić wykrywania "podejrzanej działalności użytkownika"?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę różnych użytkowników, którzy uwidocznione podejrzanego zachowania. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Z jakich krajach/regionach podejrzane żądania pochodzą? Użytkownika, który był najbardziej podejrzanych? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera listę podejrzanych użytkowników i listę kraje/regiony dla każdego użytkownika, aby pomóc w dalszym diagnozowaniu problemu.
