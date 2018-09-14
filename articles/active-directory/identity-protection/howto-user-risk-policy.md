---
title: Sposób konfigurowania zasad ryzyka użytkownika w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady usługi Azure AD Identity Protection ryzyka użytkownika.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: bcfab9ab95e41b723cb8a8e49d7390a2894d5219
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581370"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka użytkownika

Wszystkie aktywne [zdarzeń o podwyższonym ryzyku](../reports-monitoring/concept-risk-events.md) zostały wykryte przez usługę Azure Active Directory dla użytkownika Współtworzenie logiczne koncepcji o nazwie ryzyka związanego z użytkownikiem. Użytkownik oznaczonych flagą ryzyka jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

![Użytkownicy oflagowani w związku z ryzykiem](./media/howto-user-risk-policy/1200.png)


## <a name="user-risk-level"></a>Poziom ryzyka użytkownika

Poziom ryzyka użytkownika jest wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że tożsamość użytkownika został złamany. Jego jest obliczany na podstawie zdarzeń o podwyższonym ryzyku użytkownika, które są skojarzone z tożsamością użytkownika.

Stan zdarzenia o podwyższonym ryzyku jest **Active** lub **zamknięte**. Tylko podejrzanych zdarzeń, które są **Active** przyczyniają się do obliczania poziomu ryzyka użytkownika.

Poziom ryzyka użytkownika jest obliczana przy użyciu następujących danych wejściowych:

* Zdarzenia aktywnego ryzyka wpływających na użytkownika
* Poziom ryzyka tych zdarzeń
* Czy wykonano wszystkie akcje naprawcze wykonane

![Ryzyka użytkownika](./media/howto-user-risk-policy/1031.png "ryzyka użytkownika")

Poziomy ryzyka użytkownika umożliwia tworzenie zasad dostępu warunkowego, które blokują ryzykownych użytkowników, logowanie lub Wymuś je bezpiecznie zmiany hasła.

## <a name="closing-risk-events-manually"></a>Ręczne zamykanie zdarzeń o podwyższonym ryzyku

W większości przypadków potrwa akcji korygowania, takie jak bezpieczny resetowania hasła, aby automatycznie zamknąć zdarzeń o podwyższonym ryzyku. Jednak to może nie zawsze jest możliwe.  
Jest to, na przykład tak, gdy:

* Użytkownik ze zdarzeniami aktywnego ryzyka został usunięty
* Badanie wykaże, że zdarzenia ryzyka zgłaszanej zostały wykonywać na przez wiarygodnego użytkownika

Ponieważ zdarzenia o podwyższonym ryzyku są **Active** przyczyniają się do obliczeń ryzyka użytkownika, może być konieczne ręczne obniżyć jej poziom ryzyka przez ręczne zamykanie zdarzeń o podwyższonym ryzyku.  
W trakcie badania można wykonać dowolną z tych akcji zmiany stanu zdarzenia o podwyższonym ryzyku:

![Akcje](./media/howto-user-risk-policy/34.png "akcji")

* **Rozwiąż** — Jeśli po przeanalizowaniu zdarzenie o podwyższonym ryzyku, zajęło akcji korygowania odpowiednie poza ochrony tożsamości i uważasz, że zdarzenie o podwyższonym ryzyku powinna być uznana zamknięte, oznaczenia zdarzeń jako rozwiązane. Rozwiązane zdarzenia wartość stanu zdarzenia o podwyższonym ryzyku zamknięte i nie jest już przyczynia się zdarzenie o podwyższonym ryzyku do ryzyka związanego z użytkownikiem.
* **Oznacz jako wyników fałszywie dodatnich** — w niektórych przypadkach może zbadać zdarzenie o podwyższonym ryzyku i odnajdywanie, że został niepoprawnie oflagowana jako ryzykowne. Możesz pomóc zmniejszyć liczbę takich wystąpień, oznaczanie zdarzenia ryzyka jako fałszywie dodatnie. Ułatwi to algorytmów w celu klasyfikacji zdarzenia podobne w przyszłości uczenia maszynowego. Status zdarzenia fałszywie dodatnie **zamknięte** i już nie wpływają one ryzyka związanego z użytkownikiem.
* **Ignoruj** — Jeśli to ustawienie nie miały żadnych akcji korygowania, ale ma zdarzenia o podwyższonym ryzyku, który ma zostać usunięty z listy aktywnych, można oznaczyć zdarzenie o podwyższonym ryzyku Ignoruj i stanu zdarzenia zostaną zamknięte. Zignorowano zdarzenia nie przyczyniają się do ryzyka związanego z użytkownikiem. Ta opcja powinna być używana tylko w nietypowych sytuacjach.
* **Uaktywnij ponownie** -podejrzanych zdarzeń, które zostały ręcznie zamknięte (wybierając **rozwiązać**, **wynik fałszywie dodatni**, lub **Ignoruj**) można ponownie uaktywnić, ustawiła zdarzenie stan z powrotem do **Active**. Zdarzenia ryzyka ponownie uaktywnione przyczyniają się do obliczania poziomu ryzyka użytkownika. Nie można ponownie uaktywnić zdarzenia o podwyższonym ryzyku zamknięty przy użyciu funkcji korygowania (takie jak Resetowanie hasła bezpiecznego).

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

1. Na **usługi Azure AD Identity Protection** bloku, w obszarze **zbadaj**, kliknij przycisk **zdarzeń o podwyższonym ryzyku**.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1002.png "resetowania haseł usługi ręczna")
2. W **zdarzeń o podwyższonym ryzyku** kliknij zagrożenie.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1003.png "resetowania haseł usługi ręczna")
3. W bloku o podwyższonym ryzyku kliknij prawym przyciskiem myszy użytkownika.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1004.png "resetowania haseł usługi ręczna")

## <a name="closing-all-risk-events-for-a-user-manually"></a>Zamykanie wszystkich zdarzeń o podwyższonym ryzyku dla użytkownika ręcznie
Zamiast indywidualnie Ręczne zamykanie zdarzeń o podwyższonym ryzyku dla użytkownika, usługi Azure Active Directory Identity Protection zapewnia także metody, aby zamknąć wszystkie zdarzenia dla użytkownika za pomocą jednego kliknięcia.

![Akcje](./media/howto-user-risk-policy/2222.png "akcji")

Po kliknięciu **Odrzuć wszystkie zdarzenia**, wszystkie zdarzenia są zamknięte i użytkownika, którego dotyczy nie jest już na ryzyko.

## <a name="remediating-user-risk-events"></a>Korygowanie działań na podstawie zdarzeń o podwyższonym ryzyku użytkownika

Korygowanie jest akcję, aby zabezpieczyć tożsamość lub urządzeń, które wcześniej podejrzewa lub znane naruszenia. Akcja korygowania przywraca tożsamości lub urządzenia do stanu bezpiecznego i jest rozpoznawana jako poprzednie zdarzenia o podwyższonym ryzyku skojarzone z tożsamością lub urządzenia.

Korygowania zdarzeń o podwyższonym ryzyku użytkownika, możesz wykonywać następujące czynności:

* Bezpieczne hasło resetowania ręcznego korygowania zdarzeń o podwyższonym ryzyku użytkownika
* Konfigurowanie zasad zabezpieczeń ryzyka użytkownika do ograniczenia lub automatycznego korygowania zdarzeń o podwyższonym ryzyku użytkownika
* Ponowne instalowanie obrazu zainfekowanego urządzenia  

### <a name="manual-secure-password-reset"></a>Resetowanie ręczne bezpieczne hasło
Resetowanie hasła bezpiecznego jest skuteczne rozwiązywanie problemu dotyczącego wielu zdarzeń o podwyższonym ryzyku i wykonywane, automatycznie powoduje zamknięcie tych zdarzeń o podwyższonym ryzyku i ponownie oblicza poziom ryzyka użytkownika. Pulpit nawigacyjny ochrony tożsamości służy do inicjowania resetowania hasła dla użytkownika ryzykowne.

Określone okno oferuje dwie różne metody, aby zresetować hasło:

**Resetuj hasło** — wybierz tę opcję **wymaga od użytkownika do zresetowania swojego hasła** do Zezwalaj użytkownikowi na własnym odzyskiwania, jeśli użytkownik został zarejestrowany do uwierzytelniania wieloskładnikowego. Podczas jego następnego logowania użytkownik będzie wymagane do rozwiązania pomyślnie wezwanie do uwierzytelnienia Multi-Factor Authentication i następnie zmuszany do zmiany hasła. Ta opcja jest niedostępna, jeśli konto użytkownika nie jest już zarejestrowane usługi Multi-Factor authentication.

**Hasło tymczasowe** — wybierz tę opcję **wygenerowania hasła tymczasowego** natychmiast unieważnia istniejące hasło i utworzenie nowego hasła tymczasowego dla tego użytkownika. Wyślij nowe hasło tymczasowe, alternatywny adres e-mail użytkownika lub jego menedżera. Ponieważ hasło tymczasowe, użytkownik zostanie wyświetlony monit zmiany hasła podczas logowania.

![Zasady](./media/howto-user-risk-policy/1005.png "zasad")

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

1. Na **usługi Azure AD Identity Protection** bloku kliknij **użytkownicy oflagowani w związku z ryzykiem**.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1006.png "resetowania haseł usługi ręczna")
2. Z listy użytkowników wybierz użytkownika z zdarzeń o podwyższonym ryzyku w co najmniej jeden.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1007.png "resetowania haseł usługi ręczna")
3. W bloku użytkownika kliknij **Resetuj hasło**.

    ![Resetowanie hasła ręczne](./media/howto-user-risk-policy/1008.png "resetowania haseł usługi ręczna")

## <a name="user-risk-security-policy"></a>Zasady zabezpieczeń ryzyka użytkownika
Zasady zabezpieczeń ryzyka użytkownika jest zasady dostępu warunkowego, który ocenia poziom ryzyka dla określonego użytkownika, a następnie stosuje akcje korygowania i ograniczania ryzyka na podstawie wstępnie zdefiniowanych warunków i zasad.

![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1009.png "zasad ryzyka dla użytkownika")

Usługa Azure AD Identity Protection pomaga w zarządzaniu ograniczania ryzyka i korygowania użytkowników oznaczonych flagą ryzyka, ponieważ umożliwia:

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1010.png "zasad ryzyka dla użytkownika")
* Ustaw użytkownika poziomu granice ryzyka (niski, średni lub wysoki) wyzwalającego zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1011.png "zasad ryzyka dla użytkownika")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1012.png "zasad ryzyka dla użytkownika")
* Przełącz stan zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed aktywowaniem go:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1013.png "zasad ryzyka dla użytkownika")

Wybieranie **wysokiej** próg zmniejsza liczbę razy, zasada zostanie wyzwolony i minimalizuje wpływ na użytkowników.
Jednakże nie obejmuje **niski** i **średni** użytkowników generujących ryzyko związane z zasad, które mogą nie zapewnić tożsamości lub urządzeń, zostały wcześniej podejrzenie lub znane naruszenia.

Podczas ustawiania zasad

* Wyklucz użytkowników, którzy mogą wygenerować dużą liczbę fałszywych alarmów (deweloperzy, analityków zabezpieczeń)
* Wyklucz użytkowników w lokalizacjach, w którym włączenie zasad nie jest możliwe (na przykład brak dostępu do działu pomocy technicznej)
* Użyj **wysokiej** próg podczas wdrażania zasad początkowej, czy należy zminimalizować wyzwania widoczne dla użytkowników końcowych.
* Użyj **niski** próg, jeśli Twoja organizacja wymaga zwiększenia bezpieczeństwa. Wybieranie **niski** próg wprowadza dodatkowego użytkownika logowania wyzwania, ale wyższy poziom bezpieczeństwa.

Zalecana domyślna w przypadku większości organizacji jest skonfigurowanie reguły dla **średni** próg, aby zachować równowagę pomiędzy użyteczność i zabezpieczeń.

Omówienie środowiska użytkownika Zobacz:

* [Naruszenia zabezpieczeń konta odzyskiwania przepływ](flows.md#compromised-account-recovery).  
* [Naruszone zablokowano konto przepływu](flows.md#compromised-account-blocked).  

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

- Na **usługi Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **zasad ryzyka dla użytkownika**.

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1009.png "zasad ryzyka dla użytkownika")

## <a name="mitigating-user-risk-events"></a>Ograniczanie ryzyka użytkownika zdarzeń o podwyższonym ryzyku
Administratorzy mogą ustawić zasady zabezpieczeń ryzyka użytkownika w celu zablokowania użytkowników podczas logowania się w zależności od poziomu zagrożenia.

Blokowania logowania:

* Zapobiega generowania nowego użytkownika zdarzeń o podwyższonym ryzyku dla użytkownika, którego dotyczy
* Administratorzy mogą ręcznie korygowania zdarzeń o podwyższonym ryzyku wpływających na tożsamości użytkownika i przywracania go w bezpiecznym stanu


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
