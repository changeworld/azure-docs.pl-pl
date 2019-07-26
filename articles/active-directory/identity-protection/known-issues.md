---
title: Często zadawane pytania i znane problemy z usługą Identity Protection (odświeżone) w Azure Active Directory | Microsoft Docs
description: Często zadawane pytania i znane problemy z usługą Identity Protection (odświeżone) w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d5aa50aec98b3944aed92b9da49182f0608f34c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333886"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Często zadawane pytania i znane problemy z usługą Identity Protection (odświeżone) w Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Odrzuć znane problemy dotyczące ryzyka związanego z użytkownikiem

**Odrzucanie ryzyka użytkownika** w ramach klasycznej ochrony tożsamości ustawia aktora w historii ryzyka użytkownika w ramach usługi Identity Protection (odświeżony) w usłudze **Azure AD**.

**Odrzuć ryzyko związane z użytkownikiem** w usłudze Identity Protection (odświeżane) ustawia aktora w historii ryzyka użytkownika w usłudze Identity Protection (odświeżenie) do  **\<nazwy administratora z hiperłączem\>wskazującym na blok użytkownika**.

Istnieje obecnie znany problem powodujący opóźnienia w przepływie nieprzerwanego użytkownika. Jeśli masz "zasady ryzyka dla użytkowników", te zasady przestaną być stosowane do odrzuconych użytkowników w ciągu kilku minut od kliknięcia pozycji "Odrzuć ryzyko użytkownika". Istnieją jednak znane opóźnienia w przypadku, gdy środowisko użytkownika odświeża "stan ryzyka" odrzuconych użytkowników. Aby obejść ten element, należy odświeżyć stronę na poziomie przeglądarki, aby zobaczyć najnowszego stanu zagrożenia użytkownika.

## <a name="risky-users-report-known-issues"></a>Ryzykowni użytkownicy zgłaszają znane problemy

W zapytaniach w polu Nazwa **użytkownika** jest rozróżniana wielkość liter, podczas gdy zapytania w polu **Nazwa** to Case-niezależny od.

Przełączenie **Pokaż daty jako** Ukrycie kolumny **Ostatnia aktualizacja ryzyka** . Aby odczytać kolumnę, kliknij **kolumny** w górnej części bloku ryzykownych użytkowników.

**Odrzuć wszystkie zdarzenia** z klasycznej ochrony tożsamości ustawia stan zdarzeń ryzyka na **zamknięte (rozwiązane)** .

## <a name="risky-sign-ins-report-known-issues"></a>Ryzykowne logowania zgłaszają problemy

**Rozwiązanie** dotyczące ryzyka powoduje ustawienie stanu dla **użytkowników, którzy przechodzą zgodnie z zasadami opartymi na ryzyku**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Dlaczego nie mogę ustawić własnych poziomów ryzyka dla każdego zdarzenia związanego z ryzykiem?

Poziomy ryzyka związane z ochroną tożsamości są oparte na dokładności wykrywania i obsługiwane przez naszą nadzorowany Uczenie maszynowe. Aby dostosować środowisko, które są prezentowane użytkownikom, administrator może uwzględnić/wykluczyć niektórych użytkowników/grupy z zasad ryzyka użytkownika i logowania.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Dlaczego lokalizacja logowania jest niezgodna z miejscem, w którym użytkownik rzeczywiście się zalogował?

Mapowanie geolokalizacji IP jest wyzwaniem obejmującym cały branżę. Jeśli uważasz, że lokalizacja wymieniona w raporcie logowania nie jest zgodna z rzeczywistą lokalizacją, skontaktuj się z pomocą techniczną. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak działają mechanizmy przesyłania opinii w ramach ochrony tożsamości?

**Potwierdź naruszenie zabezpieczeń** (w przypadku logowania) — informuje, Azure AD Identity Protection, że logowanie nie zostało wykonane przez właściciela tożsamości i wskazuje na naruszenie zabezpieczeń.

- Po otrzymaniu opinii o stanie zalogować się i zagrożeniu użytkownika w celu potwierdzenia **bezpieczeństwa** i **wysokiego**poziomu ryzyka.

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka.

    > [!NOTE]
    > Jeśli użytkownik jest już korygowany, nie klikaj przycisku **Potwierdź złamane zabezpieczenia** , ponieważ przeniesie stan zagrożenia logowania i użytkownika w celu  potwierdzenia, że poziom zagrożenia został naruszony i podwyższonego poziomu ryzyka na **wysoki**.

**Potwierdź bezpieczną** (w przypadku logowania) — informuje Azure AD Identity Protection, że logowanie zostało wykonane przez właściciela tożsamości i nie wskazuje na naruszenie zabezpieczeń.

- Po otrzymaniu tej opinii przeniesiemy stan ryzyka logowania (nie użytkownika), aby **potwierdzić bezpieczny** i poziom ryzyka do **-** .

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka.

    > [!NOTE]
    > Jeśli uważasz, że użytkownik nie zostanie naruszony, użyj opcji **Odrzuć ryzyko** dla użytkownika na poziomie użytkownika, a nie za pomocą **potwierdzonego bezpiecznego** na poziomie logowania. **Ryzyko odrzucania użytkowników** na poziomie użytkownika powoduje zamknięcie ryzyka użytkownika i wszystkich wcześniejszych zagrożeń związanych z logowaniem i ryzykiem.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Dlaczego widzę użytkownika o niskim (lub wyższym) wyniku ryzyka, nawet jeśli nie są wyświetlane ryzykowne logowania ani zdarzenia ryzyka w usłudze Identity Protection?

Ze względu na to, że ryzyko związane z użytkownikiem ma charakter zbiorczy i nie wygasa, użytkownik może mieć ryzyko związane z ryzykiem lub wyższym, nawet jeśli nie ma żadnych ostatnich ryzykownych logowań ani zdarzeń ryzyka wyświetlanych w usłudze Identity Protection. Może się tak zdarzyć, jeśli jedyne złośliwe działanie użytkownika miało miejsce poza przedziałem czasu, w którym przechowywane są szczegóły ryzykownych logowań i zdarzeń o podwyższonym ryzyku. Nie wygasa to ryzyka dla użytkowników, ponieważ wiadomo, że złe osoby, które pozostały w środowisku klienta przez 140 dni przed przystąpieniem do naruszenia tożsamości. Klienci mogą przeglądać oś czasu ryzyka użytkownika, aby zrozumieć, dlaczego użytkownik jest narażony na ryzyko:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Dlaczego logowanie ma wynik "ryzyko związane z logowaniem (zagregowany)" o wysokim stopniu, gdy powiązane z nim środki wykrywania mają niski lub średniego ryzyka?

Wysoki zagregowany wskaźnik ryzyka może opierać się na innych funkcjach logowania lub w przypadku, gdy wygenerowane jest więcej niż jedno wykrycie dla tego logowania. Z drugiej strony Logowanie może mieć ryzyko związane z logowaniem (zagregowany), nawet jeśli wykrycie skojarzone z logowaniem ma wysokie ryzyko. 
