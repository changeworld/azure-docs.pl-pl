---
title: Jak skonfigurować zasady ryzyka logowania w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady ryzyka logowania usługi Azure AD Identity Protection.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 7350cbd3e8aed6098f24d0edaa5807d241890287
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581475"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka logowania

Usługa Azure Active Directory wykrywa [typy zdarzeń ryzyka](../reports-monitoring/concept-risk-events.md#risk-event-types) w czasie rzeczywistym, jak i offline. Każdego zdarzenia o podwyższonym ryzyku, która została wykryta podczas logowania użytkownika przyczynia się do logiczne koncepcji o nazwie ryzykowne logowania. Ryzykowne logowanie jest wskaźnikiem próby logowania, które nie mogły zostać wykonane przez prawowitym właścicielem konta użytkownika.


## <a name="sign-in-risk-level"></a>Poziom ryzyka logowania

Poziom ryzyka logowania jest wskazanie (wysoki, średni lub niski) prawdopodobieństwa, że próba logowania nie było wykonywane przez prawowitym właścicielem konta użytkownika.

## <a name="mitigating-sign-in-risk-events"></a>Łagodzenie zdarzenia ryzyka logowania

Ograniczenie to działanie, aby ograniczyć możliwość wykorzystać tożsamości ze złamanymi zabezpieczeniami lub urządzenia bez przywracania do tożsamości lub urządzenia do stanu bezpiecznego osoba atakująca. Ograniczenia nie można rozpoznać poprzednie zdarzenia ryzyka logowania skojarzone z tożsamością lub urządzenia.

Aby automatycznie rozwiązać problem dotyczący ryzykownych logowań, można skonfigurować zasady zabezpieczeń ryzyka logowania. Korzystając z tych zasad, należy rozważyć poziom ryzyka dotyczący użytkownika lub identyfikator logowania do blokowania ryzykowne logowania lub wymagać od użytkownika wykonywać uwierzytelnianie wieloskładnikowe. Te akcje mogą uniemożliwić osobie atakującej wykorzystanie kradzieży tożsamości spowodować szkodę i może stanowić trochę czasu, aby zabezpieczyć tożsamość.

## <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń ryzyka logowania
Zasady ryzyka logowania jest zasady dostępu warunkowego, która ocenia ryzyko dla określonych logowania i stosuje ograniczenia na podstawie wstępnie zdefiniowanych warunków i zasad.

![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1014.png "zasad ryzyka logowania")

Usługa Azure AD Identity Protection pomaga w zarządzaniu zapobieganie ryzykownych logowań, umożliwiając:

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1015.png "zasad ryzyka logowania")
* Ustaw ryzyka logowania poziomu wartość progową (niski, średni lub wysoki) wyzwalającego zasad:

    ![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1016.png "zasad ryzyka logowania")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasad:  

    ![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1017.png "zasad ryzyka logowania")
* Przełącz stan zasad:

    ![Rejestracja w usłudze MFA](./media/howto-sign-in-risk-policy/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed aktywowaniem go:

    ![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1018.png "zasad ryzyka logowania")

## <a name="what-you-need-to-know"></a>Co musisz wiedzieć
Można skonfigurować zasady zabezpieczeń ryzyka logowania, które wymagają uwierzytelniania wieloskładnikowego:

![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1017.png "zasad ryzyka logowania")

Jednak ze względu na bezpieczeństwo, to ustawienie działa tylko dla użytkowników, którzy mają już zarejestrowany do uwierzytelniania wieloskładnikowego. Jeśli warunek, którego chcesz wymagać uwierzytelniania wieloskładnikowego jest spełniony dla użytkownika, który nie jest jeszcze zarejestrowany do uwierzytelniania wieloskładnikowego, użytkownik jest zablokowany.

Najlepszym rozwiązaniem Jeśli chcesz wymagać uwierzytelniania wieloskładnikowego w celu ryzykownych logowań następujące czynności:

1. Włącz [zasady rejestracji uwierzytelniania wieloskładnikowego](#multi-factor-authentication-registration-policy) dla użytkowników, których to dotyczy.
2. Wymagaj dotkniętych użytkowników do logowania w sesji — ryzykowne do przeprowadzenia rejestracji usługi MFA

Wykonanie tych kroków gwarantuje, że to uwierzytelnianie wieloskładnikowe jest wymagany do ryzykowne logowania.

## <a name="best-practices"></a>Najlepsze praktyki
Wybieranie **wysokiej** próg zmniejsza liczbę razy, zasada zostanie wyzwolony i minimalizuje wpływ na użytkowników.  

Jednakże nie obejmuje **niski** i **średni** logowania oznaczonych flagą ryzyka z zasad, które nie mogą blokować atakujący korzystający z wykorzystanie tożsamości ze złamanymi zabezpieczeniami.

Podczas ustawiania zasad

* Wyklucz użytkowników, którzy nie obsługują / nie może mieć uwierzytelnianie wieloskładnikowe
* Wyklucz użytkowników w lokalizacjach, w którym włączenie zasad nie jest możliwe (na przykład brak dostępu do działu pomocy technicznej)
* Wyklucz użytkowników, którzy mogą wygenerować dużą liczbę fałszywych alarmów (deweloperzy, analityków zabezpieczeń)
* Użyj **wysokiej** próg podczas wdrażania zasad początkowej, czy należy zminimalizować wyzwania widoczne dla użytkowników końcowych.
* Użyj **niski** próg, jeśli Twoja organizacja wymaga zwiększenia bezpieczeństwa. Wybieranie **niski** próg wprowadza dodatkowego użytkownika logowania wyzwania, ale wyższy poziom bezpieczeństwa.

Zalecana domyślna w przypadku większości organizacji jest skonfigurowanie reguły dla **średni** próg, aby zachować równowagę pomiędzy użyteczność i zabezpieczeń.

Zasady ryzyka logowania to:

* Stosowane do całego ruchu w przeglądarce i logowania korzystające z nowoczesnego uwierzytelniania.
* Nie są stosowane do aplikacji przy użyciu starszych protokołów zabezpieczeń przez wyłączenie punktu końcowego protokołu WS-Trust u dostawcy tożsamości federacyjnych, takich jak usługi AD FS.

**Zdarzeń o podwyższonym ryzyku** strony w konsoli usługi Identity Protection Wyświetla listę wszystkich zdarzeń:

* Ta zasada została zastosowana do
* Możesz sprawdzić działanie i określić, czy akcja została odpowiednie

Omówienie środowiska użytkownika Zobacz:

* [Ryzykowne logowania odzyskiwania](flows.md#risky-sign-in-recovery)
* [Ryzykowne logowanie zablokowane](flows.md#risky-sign-in-blocked)  
* [Środowisko logowania za pomocą usługi Azure AD Identity Protection](flows.md)  

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

- Na **usługi Azure AD Identity Protection** bloku, w **Konfiguruj** , kliknij przycisk **zasad ryzyka logowania**.

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-sign-in-risk-policy/1014.png "zasad ryzyka dla użytkownika")




## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
