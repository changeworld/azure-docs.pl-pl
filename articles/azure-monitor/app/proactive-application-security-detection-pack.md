---
title: Pakiet wykrywania zabezpieczeń w usłudze Azure Application Insights
description: Monitoruj aplikację za pomocą usługi Azure Application Insights i inteligentnego wykrywania potencjalnych problemów z zabezpieczeniami.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669730"
---
# <a name="application-security-detection-pack-preview"></a>Pakiet wykrywania zabezpieczeń aplikacji (wersja zapoznawcza)

Usługa Application Insights automatycznie analizuje dane telemetryczne generowane przez aplikację i wykrywa potencjalne problemy z zabezpieczeniami. Ta funkcja umożliwia identyfikowanie potencjalnych problemów z zabezpieczeniami i obsługę ich przez naprawienie aplikacji lub podjęcie niezbędnych środków bezpieczeństwa.

Ta funkcja nie wymaga specjalnej konfiguracji, poza [skonfigurowaniem aplikacji do wysyłania danych telemetrycznych](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy otrzymam tego typu powiadomienia o wykrywaniu z inteligentnego wykrywania?
Istnieją trzy typy problemów z zabezpieczeniami, które są wykrywane:
1. Niezabezpieczony dostęp do adresu URL: adres URL w aplikacji jest dostępny zarówno za pośrednictwem protokołu HTTP, jak i HTTPS. Zazwyczaj adres URL, który akceptuje żądania HTTPS, nie powinien akceptować żądań HTTP. Może to wskazywać na błąd lub problem z zabezpieczeniami w aplikacji.
2. Formularz niezabezpieczony: formularz (lub inne żądanie "POST") w aplikacji używa protokołu HTTP zamiast protokołu HTTPS. Za pomocą protokołu HTTP może naruszyć dane użytkownika, który jest wysyłany przez formularz.
3. Podejrzana aktywność użytkowników: aplikacja jest dostępna z wielu krajów/regionów przez tego samego użytkownika w przybliżeniu w tym samym czasie. Na przykład ten sam użytkownik uzyskał dostęp do aplikacji z Hiszpanii i Stanów Zjednoczonych w ciągu tej samej godziny. To wykrycie wskazuje potencjalnie złośliwą próbę dostępu do aplikacji.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Czy moja aplikacja na pewno ma problem z zabezpieczeniami?
Nie, powiadomienie nie oznacza, że aplikacja na pewno ma problem z zabezpieczeniami. Wykrycie dowolnego z powyższych scenariuszy może w wielu przypadkach wskazywać na problem z zabezpieczeniami. Jednak wykrywanie może mieć naturalne uzasadnienie biznesowe i może być ignorowane.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Jak naprawić wykrywanie "niezabezpieczony dostęp do adresów URL"?
1. **Segregowanie.** Powiadomienie zawiera liczbę użytkowników, którzy uzyskują dostęp do niezabezpieczonych adresów URL, oraz adres URL, na który najbardziej miał wpływ niezabezpieczony dostęp. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Jaki procent użytkowników uzyskał dostęp do niezabezpieczonych adresów URL? Ile adresów URL zostało dotkniętych? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Wykrywanie zawiera listę niezabezpieczonych żądań oraz listy adresów URL i użytkowników, których dotyczy problem, aby pomóc w dalszym diagnozowaniu problemu.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Jak naprawić wykrywanie "niezabezpieczony formularz"?
1. **Segregowanie.** Powiadomienie zawiera liczbę niezabezpieczonych formularzy i liczbę użytkowników, których dane zostały potencjalnie naruszone. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Która forma była zaangażowana w największą liczbę niezabezpieczonych transmisji i jaka jest dystrybucja niezabezpieczonych transmisji w czasie? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Wykrywanie zawiera listę niezabezpieczonych formularzy i podział liczby niezabezpieczonych transmisji dla każdego formularza, aby pomóc w dalszym diagnozowaniu problemu.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Jak naprawić wykrywanie "Podejrzana aktywność użytkownika"?
1. **Segregowanie.** Powiadomienie zawiera liczbę różnych użytkowników, którzy wykazali podejrzane zachowanie. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Z jakich krajów/regionów pochodzą podejrzane żądania? Który użytkownik był najbardziej podejrzany? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Wykrywanie zawiera listę podejrzanych użytkowników i listę krajów/regionów dla każdego użytkownika, aby pomóc w dalszym diagnozowaniu problemu.
