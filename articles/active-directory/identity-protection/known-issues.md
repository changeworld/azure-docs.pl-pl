---
title: Często zadawane pytania i znane problemy związane z ochrony tożsamości (odświeżane) w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Często zadawane pytania i znane problemy związane z ochrony tożsamości (odświeżane) w usłudze Azure Active Directory.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd894b214366302af4651b5944e250086be14beb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183840"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Często zadawane pytania i znane problemy związane z ochrony tożsamości (odświeżane) w usłudze Azure Active Directory


## <a name="dismiss-user-risk"></a>Odrzuć ryzyko związane z użytkownikiem

**Odrzuć ryzyka związanego z użytkownikiem** w modelu klasycznym Identity Protection ustawia aktora w historii ryzyka użytkownika w Identity Protection (odświeżane) do **usługi Azure AD**.


**Odrzuć ryzyka związanego z użytkownikiem** w Identity Protection (odświeżane) ustawia aktora w historii ryzyka użytkownika w Identity Protection (odświeżane) do **\<nazwę administratora przy użyciu hiperłącze wskazujące blok tego użytkownika\>**.


## <a name="risky-users-report"></a>Raport ryzykownych użytkowników

Zapytanie na **username** rozróżnia wielkość liter podczas kwerendy dotyczące **nazwa** pola są niezależne od przypadku.

Przełączanie **Pokaż daty jako** ukrywa **Ostatnia aktualizacja ryzyka** kolumny. Kliknij kolumnę ich **kolumn** w górnej części bloku ryzykownych użytkowników.

**Odrzuć wszystkie zdarzenia** w modelu klasycznym Identity Protection umożliwia ustawienie stanu zdarzenia o podwyższonym ryzyku **zamknięte (rozwiązane)**.

Jeśli spróbujesz uzyskać dostęp do raportu ryzykownych użytkowników, klikając **raportu ryzykownych użytkowników** w rekordzie logowania w raport dotyczący ryzykownych logowań, czasem może pokazywać **coś poszło źle. Ponów próbę**. Aby rozwiązać ten problem, kliknij przycisk **Zastosuj** lub **resetowania** w górnej części ekranu i ryzykownych użytkowników zostaną wyświetlone dane.


## <a name="risky-sign-ins-report"></a>Raport dotyczący ryzykownych logowań

**Rozwiąż** o ryzyko dotyczące zdarzeń ustawia stan **użytkowników przekazywane MFA oparte na zasady na podstawie ryzyka**.

**Resetuj** w **ryzykownych logowań** raportu nie czyści wartość **typ zdarzenia ryzyka**.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Dlaczego nie można ustawić własną poziomów ryzyka dla każdego zdarzenia o podwyższonym ryzyku

Poziomy ryzyka, które w ochronie tożsamości opartych na dokładność wykrywania i zarządzany przez naszych uczenia maszynowego nadzorowanym. Aby dostosować, co użytkownicy środowiska są prezentowane, administrator może uwzględnianie/wykluczanie określonych użytkowników/grup ryzyka związanego z użytkownikiem i zasady ryzyka logowania.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Dlaczego lokalizacja logowania nie pasuje gdzie naprawdę logowania użytkownika z?

Mapowanie geolokalizacja IP jest wyzwaniem w całej branży. Jeśli uważasz, że lokalizacja wymienione w raporcie operacji logowania jest niezgodny rzeczywistej lokalizacji, skontaktuj się z pomocy technicznej. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak działają mechanizmy przesyłania opinii w Identity Protection?

**Upewnij się, naruszenia zabezpieczeń** (na klawiaturze logowania) — informuje o usługi Azure AD Identity Protection logowania nie było wykonywane przez właściciela tożsamości i wskazuje naruszenia zabezpieczeń.

- Po otrzymaniu tej opinii, możemy przenieść stan ryzyka logowania i użytkownika, aby **została potwierdzona, naruszenia zabezpieczeń** i poziomu ryzyka **wysokiej**.

- Ponadto firma Microsoft zapewnia informacje do naszej usługi machine learning systemów przyszłe ulepszenia w ocenie ryzyka.

    > [!NOTE]
    > Użytkownik już jest korygowana, nie należy klikać przycisku **Potwierdź naruszone** ponieważ przenosi stan ryzyka logowania i użytkownika, aby **została potwierdzona, naruszenia zabezpieczeń** i poziomu ryzyka **wysokiej**.

**Upewnij się, bezpieczne** (na klawiaturze logowania) — informuje usługa Azure AD Identity Protection, że logowanie zostało wykonane przez właściciela tożsamości i nie wskazuje na naruszenia zabezpieczeń.

- Po otrzymaniu tej opinii, możemy przenieść logowania (nie użytkownika) o podwyższonym ryzyku stan **safe została potwierdzona** i poziom ryzyka do **-**.

- Ponadto firma Microsoft zapewnia informacje do naszej usługi machine learning systemów przyszłe ulepszenia w ocenie ryzyka.

    > [!NOTE]
    > Jeśli uważasz, że użytkownik nie zostanie naruszony, należy użyć **odrzucić ryzyka związanego z użytkownikiem** na poziomie użytkownika zamiast **safe została potwierdzona** na poziomie rejestrowania. A **odrzucić ryzyka związanego z użytkownikiem** użytkownika zostanie zamknięty poziom ryzyka związanego z użytkownikiem i wszystkie upłynął ryzykownych logowań i zdarzeń o podwyższonym ryzyku.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Dlaczego widzę ocenę ryzyka użytkownika z niskim (lub nowszej), nawet jeśli żadne ryzykowne logowania ani zdarzeń o podwyższonym ryzyku są wyświetlane w Identity Protection?

Podany użytkownik ryzyka jest z natury i nie wygasa, użytkownik może mieć ryzyka związanego z użytkownikiem, niski lub powyżej, nawet jeśli nie są najnowsze ryzykowne logowania lub zdarzenia o podwyższonym ryzyku są wyświetlane na ochronę tożsamości. Może się to zdarzyć, jeśli tylko złośliwych działań na koncie użytkownika miała miejsce poza przedział czasu, dla którego przechowujemy dane dotyczące ryzykownych logowań i zdarzeń o podwyższonym ryzyku. Firma Microsoft nie wygasają ryzyka związanego z użytkownikiem, ponieważ nieupoważnione osoby być znane, aby pozostać w środowisku klientów ponad 140 dni za tożsamości ze złamanymi zabezpieczeniami rozwijanie ataku. Klientów można przejrzeć osi czasu ryzyka użytkownika, aby zrozumieć, dlaczego jest użytkownikiem na ryzyko, przechodząc do: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Dlaczego Zaloguj ma "sign in (agregacji)" oceny ryzyka wysoki w przypadku wykrycia skojarzonych z nim zagrożenia na poziomie niskim lub średnim?

Wysokiego ryzyka agregacji może opierać się na inne funkcje logowania lub fakt, że więcej niż jeden wykrywania uruchamiane do tego logowania. I odwrotnie, logowania mogą mieć ryzyka logowania (agregacji) ze średnim nawet, jeśli są wykrywania skojarzony identyfikator logowania o wysokim ryzyku. 
