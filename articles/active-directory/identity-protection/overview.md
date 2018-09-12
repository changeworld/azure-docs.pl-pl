---
title: Usługa Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure AD Identity Protection umożliwia ograniczenie możliwości osobie atakującej wykorzystanie tożsamości ze złamanymi zabezpieczeniami lub urządzenia oraz ochronę tożsamości lub urządzeń, które wcześniej podejrzewa lub znane naruszenia.
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
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 791abd52ff4c016fe873288008e9d9b6adec6480
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378500"
---
# <a name="azure-active-directory-identity-protection"></a>Ochrona tożsamości w usłudze Azure Active Directory

Usługa Azure Active Directory Identity Protection to funkcja wersji Azure AD Premium P2, która pozwala na:

- Wykrywanie potencjalnych luk w zabezpieczeniach wpływających na tożsamości w organizacji

- Skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane działania, that are related to tożsamości w organizacji  

- Badanie podejrzanych zdarzeń i podejmij odpowiednią akcję, aby je rozwiązać   


## <a name="getting-started"></a>Wprowadzenie

Tożsamości w chmurze dla ponad dekadę ma bezpieczne Microsoft. Za pomocą usługi Azure Active Directory Identity Protection w danym środowisku, można użyć tych samych systemach ochrony, używaną przez firmę Microsoft do zabezpieczania tożsamości.

Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. W ciągu lat osoby atakujące stały się coraz bardziej efektywne w wykorzystaniu naruszeń innych firm i za pomocą zaawansowanych wyłudzanie informacji. Tak szybko, jak osoba atakująca uzyska dostęp do nawet użytkownika niski uprzywilejowanych kont, jest stosunkowo łatwe uzyskanie dostępu do zasobów firmy ważne za pośrednictwem ruchu poprzecznego.

W wyniku tego następujące czynności:

- Ochrona wszystkich tożsamości, niezależnie od ich poziomu uprawnień

- Proaktywnie uniemożliwić ze złamanymi zabezpieczeniami tożsamości są użyte

Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmów heurystycznych w celu wykrycia anomalii i podejrzanych zdarzeń, które wskazują na potencjalne naruszenia tożsamości. Przy użyciu tych danych, Identity Protection generuje raporty i alerty, które umożliwiają użytkownikowi oceniać wykryte problemy i podjąć odpowiednie środki zaradcze lub działania korygujące.

Usługa Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. W celu ochrony tożsamości w organizacji, można skonfigurować opartych na ryzykach zasady, które automatycznie odpowiadać na wykryte problemy, jeśli osiągnięty poziom ryzyka określony. Te zasady, oprócz innych kontrolek dostępu warunkowego, dostarczone przez usługę Azure Active Directory i usług EMS można automatycznie blokować lub zainicjować akcji adaptacyjne korygowania, które resetowania haseł w tym i wymuszanie uwierzytelniania wieloskładnikowego.


#### <a name="identity-protection-capabilities"></a>Funkcje ochrony tożsamości

**Wykrywanie luk w zabezpieczeniach i ryzykowne konta:**  

* Zapewnianie niestandardowych zalecenia, aby poprawić ogólny stan zabezpieczeń przez wyróżnianie luk w zabezpieczeniach
* Obliczanie poziomy ryzyka logowania
* Obliczanie poziomów ryzyka użytkownika


**Badanie zdarzeń o podwyższonym ryzyku:**

* Wysyłanie powiadomień dla zdarzeń o podwyższonym ryzyku
* Badanie zdarzeń o podwyższonym ryzyku, korzystając z informacji istotnych i kontekstowych
* Zapewnienie podstawowych przepływów pracy do śledzenia dochodzenia
* Zapewnianiu łatwego dostępu do akcji korygowania, takie jak resetowanie haseł

**Zasady dostępu warunkowego na podstawie ryzyka:**

* Zasady wyeliminowanie ryzykowne logowania za pomocą blokowania logowania bądź wymagają takiej wezwań do uwierzytelnienia Multi-Factor Authentication
* Zasady na wartość Blokuj lub kont bezpiecznego ryzykownych użytkowników
* Zasady, aby wymagać od użytkowników rejestracji do uwierzytelniania wieloskładnikowego



## <a name="identity-protection-roles"></a>Role ochrony tożsamości

Aby zrównoważyć obciążenie działań z zakresu zarządzania całym implementacji Identity Protection, można przypisać kilku ról. Usługa Azure AD Identity Protection obsługuje 3 role katalogu:

| Rola                         | Można zrobić                          | Nie można wykonać
| :--                          | ---                                |  ---   |
| Administrator globalny         | Pełny dostęp do usługi Identity Protection dołączanie Identity Protection| |
| Administrator zabezpieczeń       | Pełny dostęp do usługi Identity Protection | Dołączanie Identity Protection, resetować hasła dla użytkownika |
| Czytelnik zabezpieczeń              | Dostęp tylko do odczytu do usługi Identity Protection | Korygowanie użytkowników dołączanie Identity Protection, konfigurowanie zasad, resetowania haseł |




Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Wykrywanie

### <a name="vulnerabilities"></a>Luki w zabezpieczeniach

Usługa Azure Active Directory Identity Protection analizuje konfigurację i wykrywa luki w zabezpieczeniach, które mogą mieć wpływ na tożsamości użytkownika. Aby uzyskać więcej informacji, zobacz [luk w zabezpieczeniach wykrywanych przez usługi Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są związane z tożsamościami użytkowników. System tworzy rekord dla każdego wykryte podejrzane działania. Te rekordy są również nazywane zdarzeń o podwyższonym ryzyku.  
Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Badanie
Ci się podróż przez ochronę tożsamości zwykle zaczyna się od pulpitu nawigacyjnego Identity Protection.

![Korygowanie](./media/overview/1000.png "korygowania")

Pulpit nawigacyjny zapewnia dostęp do:

* Raporty takie jak **użytkownicy oflagowani w związku z ryzykiem**, **zdarzeń o podwyższonym ryzyku** i **luk w zabezpieczeniach**
* Ustawienia, takie jak konfiguracja usługi **zasad zabezpieczeń**, **powiadomienia** i **rejestracji uwierzytelniania wieloskładnikowego**

Zwykle to punkt początkowy dla badania, czyli procesu recenzowania działania, dzienniki i inne istotne informacje związane z zdarzenie o podwyższonym ryzyku, można zdecydować, czy kroki korygowania i ograniczania ryzyka są niezbędne, i jak oceniasz tożsamości naruszone i zrozumieć używania tożsamości ze złamanymi zabezpieczeniami.

Możesz powiązać badanie działań mających na celu [powiadomienia](notifications.md) ochrony usługi Azure Active Directory, wysyła wiadomości e-mail.

Poniższe sekcje zawierają szczegółowe informacje i czynności, które są powiązane z badania.  


## <a name="risky-sign-ins"></a>Ryzykowne logowania

Usługa Azure Active Directory wykrywa [typy zdarzeń ryzyka](../reports-monitoring/concept-risk-events.md#risk-event-types) w czasie rzeczywistym, jak i offline. Każdego zdarzenia o podwyższonym ryzyku, która została wykryta podczas logowania użytkownika przyczynia się do logiczne koncepcji o nazwie ryzykowne logowania. Ryzykowne logowanie jest wskaźnikiem próby logowania, które nie mogły zostać wykonane przez prawowitym właścicielem konta użytkownika.


### <a name="sign-in-risk-level"></a>Poziom ryzyka logowania

Poziom ryzyka logowania jest wskazanie (wysoki, średni lub niski) prawdopodobieństwa, że próba logowania nie było wykonywane przez prawowitym właścicielem konta użytkownika.

### <a name="mitigating-sign-in-risk-events"></a>Łagodzenie zdarzenia ryzyka logowania

Ograniczenie to działanie, aby ograniczyć możliwość wykorzystać tożsamości ze złamanymi zabezpieczeniami lub urządzenia bez przywracania do tożsamości lub urządzenia do stanu bezpiecznego osoba atakująca. Ograniczenia nie można rozpoznać poprzednie zdarzenia ryzyka logowania skojarzone z tożsamością lub urządzenia.

Aby automatycznie rozwiązać problem dotyczący ryzykownych logowań, można skonfigurować zasady zabezpieczeń ryzyka logowania. Korzystając z tych zasad, należy rozważyć poziom ryzyka dotyczący użytkownika lub identyfikator logowania do blokowania ryzykowne logowania lub wymagać od użytkownika wykonywać uwierzytelnianie wieloskładnikowe. Te akcje mogą uniemożliwić osobie atakującej wykorzystanie kradzieży tożsamości spowodować szkodę i może stanowić trochę czasu, aby zabezpieczyć tożsamość.

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń ryzyka logowania
Zasady ryzyka logowania jest zasady dostępu warunkowego, która ocenia ryzyko dla określonych logowania i stosuje ograniczenia na podstawie wstępnie zdefiniowanych warunków i zasad.

![Zasady ryzyka logowania](./media/overview/1014.png "zasad ryzyka logowania")

Usługa Azure AD Identity Protection pomaga w zarządzaniu zapobieganie ryzykownych logowań, umożliwiając:

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady ryzyka logowania](./media/overview/1015.png "zasad ryzyka logowania")
* Ustaw ryzyka logowania poziomu wartość progową (niski, średni lub wysoki) wyzwalającego zasad:

    ![Zasady ryzyka logowania](./media/overview/1016.png "zasad ryzyka logowania")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasad:  

    ![Zasady ryzyka logowania](./media/overview/1017.png "zasad ryzyka logowania")
* Przełącz stan zasad:

    ![Rejestracja w usłudze MFA](./media/overview/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed aktywowaniem go:

    ![Zasady ryzyka logowania](./media/overview/1018.png "zasad ryzyka logowania")

#### <a name="what-you-need-to-know"></a>Co musisz wiedzieć
Można skonfigurować zasady zabezpieczeń ryzyka logowania, które wymagają uwierzytelniania wieloskładnikowego:

![Zasady ryzyka logowania](./media/overview/1017.png "zasad ryzyka logowania")

Jednak ze względu na bezpieczeństwo, to ustawienie działa tylko dla użytkowników, którzy mają już zarejestrowany do uwierzytelniania wieloskładnikowego. Jeśli warunek, którego chcesz wymagać uwierzytelniania wieloskładnikowego jest spełniony dla użytkownika, który nie jest jeszcze zarejestrowany do uwierzytelniania wieloskładnikowego, użytkownik jest zablokowany.

Najlepszym rozwiązaniem Jeśli chcesz wymagać uwierzytelniania wieloskładnikowego w celu ryzykownych logowań następujące czynności:

1. Włącz [zasady rejestracji uwierzytelniania wieloskładnikowego](#multi-factor-authentication-registration-policy) dla użytkowników, których to dotyczy.
2. Wymagaj dotkniętych użytkowników do logowania w sesji — ryzykowne do przeprowadzenia rejestracji usługi MFA

Wykonanie tych kroków gwarantuje, że to uwierzytelnianie wieloskładnikowe jest wymagany do ryzykowne logowania.

#### <a name="best-practices"></a>Najlepsze praktyki
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

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1014.png "zasad ryzyka dla użytkownika")



## <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem

Wszystkie aktywne [zdarzeń o podwyższonym ryzyku](../reports-monitoring/concept-risk-events.md) zostały wykryte przez usługę Azure Active Directory dla użytkownika Współtworzenie logiczne koncepcji o nazwie ryzyka związanego z użytkownikiem. Użytkownik oznaczonych flagą ryzyka jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

![Użytkownicy oflagowani w związku z ryzykiem](./media/overview/1200.png)


### <a name="user-risk-level"></a>Poziom ryzyka użytkownika

Poziom ryzyka użytkownika jest wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że tożsamość użytkownika został złamany. Jego jest obliczany na podstawie zdarzeń o podwyższonym ryzyku użytkownika, które są skojarzone z tożsamością użytkownika.

Stan zdarzenia o podwyższonym ryzyku jest **Active** lub **zamknięte**. Tylko podejrzanych zdarzeń, które są **Active** przyczyniają się do obliczania poziomu ryzyka użytkownika.

Poziom ryzyka użytkownika jest obliczana przy użyciu następujących danych wejściowych:

* Zdarzenia aktywnego ryzyka wpływających na użytkownika
* Poziom ryzyka tych zdarzeń
* Czy wykonano wszystkie akcje naprawcze wykonane

![Ryzyka użytkownika](./media/overview/1031.png "ryzyka użytkownika")

Poziomy ryzyka użytkownika umożliwia tworzenie zasad dostępu warunkowego, które blokują ryzykownych użytkowników, logowanie lub Wymuś je bezpiecznie zmiany hasła.

### <a name="closing-risk-events-manually"></a>Ręczne zamykanie zdarzeń o podwyższonym ryzyku

W większości przypadków potrwa akcji korygowania, takie jak bezpieczny resetowania hasła, aby automatycznie zamknąć zdarzeń o podwyższonym ryzyku. Jednak to może nie zawsze jest możliwe.  
Jest to, na przykład tak, gdy:

* Użytkownik ze zdarzeniami aktywnego ryzyka został usunięty
* Badanie wykaże, że zdarzenia ryzyka zgłaszanej zostały wykonywać na przez wiarygodnego użytkownika

Ponieważ zdarzenia o podwyższonym ryzyku są **Active** przyczyniają się do obliczeń ryzyka użytkownika, może być konieczne ręczne obniżyć jej poziom ryzyka przez ręczne zamykanie zdarzeń o podwyższonym ryzyku.  
W trakcie badania można wykonać dowolną z tych akcji zmiany stanu zdarzenia o podwyższonym ryzyku:

![Akcje](./media/overview/34.png "akcji")

* **Rozwiąż** — Jeśli po przeanalizowaniu zdarzenie o podwyższonym ryzyku, zajęło akcji korygowania odpowiednie poza ochrony tożsamości i uważasz, że zdarzenie o podwyższonym ryzyku powinna być uznana zamknięte, oznaczenia zdarzeń jako rozwiązane. Rozwiązane zdarzenia wartość stanu zdarzenia o podwyższonym ryzyku zamknięte i nie jest już przyczynia się zdarzenie o podwyższonym ryzyku do ryzyka związanego z użytkownikiem.
* **Oznacz jako wyników fałszywie dodatnich** — w niektórych przypadkach może zbadać zdarzenie o podwyższonym ryzyku i odnajdywanie, że został niepoprawnie oflagowana jako ryzykowne. Możesz pomóc zmniejszyć liczbę takich wystąpień, oznaczanie zdarzenia ryzyka jako fałszywie dodatnie. Ułatwi to algorytmów w celu klasyfikacji zdarzenia podobne w przyszłości uczenia maszynowego. Status zdarzenia fałszywie dodatnie **zamknięte** i już nie wpływają one ryzyka związanego z użytkownikiem.
* **Ignoruj** — Jeśli to ustawienie nie miały żadnych akcji korygowania, ale ma zdarzenia o podwyższonym ryzyku, który ma zostać usunięty z listy aktywnych, można oznaczyć zdarzenie o podwyższonym ryzyku Ignoruj i stanu zdarzenia zostaną zamknięte. Zignorowano zdarzenia nie przyczyniają się do ryzyka związanego z użytkownikiem. Ta opcja powinna być używana tylko w nietypowych sytuacjach.
* **Uaktywnij ponownie** -podejrzanych zdarzeń, które zostały ręcznie zamknięte (wybierając **rozwiązać**, **wynik fałszywie dodatni**, lub **Ignoruj**) można ponownie uaktywnić, ustawiła zdarzenie stan z powrotem do **Active**. Zdarzenia ryzyka ponownie uaktywnione przyczyniają się do obliczania poziomu ryzyka użytkownika. Nie można ponownie uaktywnić zdarzenia o podwyższonym ryzyku zamknięty przy użyciu funkcji korygowania (takie jak Resetowanie hasła bezpiecznego).

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

1. Na **usługi Azure AD Identity Protection** bloku, w obszarze **zbadaj**, kliknij przycisk **zdarzeń o podwyższonym ryzyku**.

    ![Resetowanie hasła ręczne](./media/overview/1002.png "resetowania haseł usługi ręczna")
2. W **zdarzeń o podwyższonym ryzyku** kliknij zagrożenie.

    ![Resetowanie hasła ręczne](./media/overview/1003.png "resetowania haseł usługi ręczna")
3. W bloku o podwyższonym ryzyku kliknij prawym przyciskiem myszy użytkownika.

    ![Resetowanie hasła ręczne](./media/overview/1004.png "resetowania haseł usługi ręczna")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Zamykanie wszystkich zdarzeń o podwyższonym ryzyku dla użytkownika ręcznie
Zamiast indywidualnie Ręczne zamykanie zdarzeń o podwyższonym ryzyku dla użytkownika, usługi Azure Active Directory Identity Protection zapewnia także metody, aby zamknąć wszystkie zdarzenia dla użytkownika za pomocą jednego kliknięcia.

![Akcje](./media/overview/2222.png "akcji")

Po kliknięciu **Odrzuć wszystkie zdarzenia**, wszystkie zdarzenia są zamknięte i użytkownika, którego dotyczy nie jest już na ryzyko.

### <a name="remediating-user-risk-events"></a>Korygowanie działań na podstawie zdarzeń o podwyższonym ryzyku użytkownika

Korygowanie jest akcję, aby zabezpieczyć tożsamość lub urządzeń, które wcześniej podejrzewa lub znane naruszenia. Akcja korygowania przywraca tożsamości lub urządzenia do stanu bezpiecznego i jest rozpoznawana jako poprzednie zdarzenia o podwyższonym ryzyku skojarzone z tożsamością lub urządzenia.

Korygowania zdarzeń o podwyższonym ryzyku użytkownika, możesz wykonywać następujące czynności:

* Bezpieczne hasło resetowania ręcznego korygowania zdarzeń o podwyższonym ryzyku użytkownika
* Konfigurowanie zasad zabezpieczeń ryzyka użytkownika do ograniczenia lub automatycznego korygowania zdarzeń o podwyższonym ryzyku użytkownika
* Ponowne instalowanie obrazu zainfekowanego urządzenia  

#### <a name="manual-secure-password-reset"></a>Resetowanie ręczne bezpieczne hasło
Resetowanie hasła bezpiecznego jest skuteczne rozwiązywanie problemu dotyczącego wielu zdarzeń o podwyższonym ryzyku i wykonywane, automatycznie powoduje zamknięcie tych zdarzeń o podwyższonym ryzyku i ponownie oblicza poziom ryzyka użytkownika. Pulpit nawigacyjny ochrony tożsamości służy do inicjowania resetowania hasła dla użytkownika ryzykowne.

Określone okno oferuje dwie różne metody, aby zresetować hasło:

**Resetuj hasło** — wybierz tę opcję **wymaga od użytkownika do zresetowania swojego hasła** do Zezwalaj użytkownikowi na własnym odzyskiwania, jeśli użytkownik został zarejestrowany do uwierzytelniania wieloskładnikowego. Podczas jego następnego logowania użytkownik będzie wymagane do rozwiązania pomyślnie wezwanie do uwierzytelnienia Multi-Factor Authentication i następnie zmuszany do zmiany hasła. Ta opcja jest niedostępna, jeśli konto użytkownika nie jest już zarejestrowane usługi Multi-Factor authentication.

**Hasło tymczasowe** — wybierz tę opcję **wygenerowania hasła tymczasowego** natychmiast unieważnia istniejące hasło i utworzenie nowego hasła tymczasowego dla tego użytkownika. Wyślij nowe hasło tymczasowe, alternatywny adres e-mail użytkownika lub jego menedżera. Ponieważ hasło tymczasowe, użytkownik zostanie wyświetlony monit zmiany hasła podczas logowania.

![Zasady](./media/overview/1005.png "zasad")

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

1. Na **usługi Azure AD Identity Protection** bloku kliknij **użytkownicy oflagowani w związku z ryzykiem**.

    ![Resetowanie hasła ręczne](./media/overview/1006.png "resetowania haseł usługi ręczna")
2. Z listy użytkowników wybierz użytkownika z zdarzeń o podwyższonym ryzyku w co najmniej jeden.

    ![Resetowanie hasła ręczne](./media/overview/1007.png "resetowania haseł usługi ręczna")
3. W bloku użytkownika kliknij **Resetuj hasło**.

    ![Resetowanie hasła ręczne](./media/overview/1008.png "resetowania haseł usługi ręczna")

### <a name="user-risk-security-policy"></a>Zasady zabezpieczeń ryzyka użytkownika
Zasady zabezpieczeń ryzyka użytkownika jest zasady dostępu warunkowego, który ocenia poziom ryzyka dla określonego użytkownika, a następnie stosuje akcje korygowania i ograniczania ryzyka na podstawie wstępnie zdefiniowanych warunków i zasad.

![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1009.png "zasad ryzyka dla użytkownika")

Usługa Azure AD Identity Protection pomaga w zarządzaniu ograniczania ryzyka i korygowania użytkowników oznaczonych flagą ryzyka, ponieważ umożliwia:

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1010.png "zasad ryzyka dla użytkownika")
* Ustaw użytkownika poziomu granice ryzyka (niski, średni lub wysoki) wyzwalającego zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1011.png "zasad ryzyka dla użytkownika")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1012.png "zasad ryzyka dla użytkownika")
* Przełącz stan zasad:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed aktywowaniem go:

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1013.png "zasad ryzyka dla użytkownika")

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

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1009.png "zasad ryzyka dla użytkownika")

### <a name="mitigating-user-risk-events"></a>Ograniczanie ryzyka użytkownika zdarzeń o podwyższonym ryzyku
Administratorzy mogą ustawić zasady zabezpieczeń ryzyka użytkownika w celu zablokowania użytkowników podczas logowania się w zależności od poziomu zagrożenia.

Blokowania logowania:

* Zapobiega generowania nowego użytkownika zdarzeń o podwyższonym ryzyku dla użytkownika, którego dotyczy
* Administratorzy mogą ręcznie korygowania zdarzeń o podwyższonym ryzyku wpływających na tożsamości użytkownika i przywracania go w bezpiecznym stanu



## <a name="multi-factor-authentication-registration-policy"></a>Zasady rejestracji uwierzytelniania wieloskładnikowego
Usługa Azure Multi-Factor authentication jest metodą weryfikacji tożsamości, która wymaga użycia więcej niż tylko nazwy użytkownika i hasła. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników i transakcji.  
Firma Microsoft zaleca, wymagają usługi Azure Multi-Factor authentication do logowania użytkownika, ponieważ jego:

* Zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji
* Odgrywa kluczową rolę w przygotowywanie organizacji do ochrony i odzyskiwania z naruszeń konta

![Zasady dotyczące ryzyka związanego z użytkownika](./media/overview/1019.png "zasad ryzyka dla użytkownika")

Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie wieloskładnikowe systemu Azure?](../authentication/multi-factor-authentication.md)

Usługa Azure AD Identity Protection pomaga w zarządzaniu wdrożenie rejestracji uwierzytelniania wieloskładnikowego przez skonfigurowanie zasad, która pozwala na:

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady MFA](./media/overview/1020.png "zasad MFA")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasady::  

    ![Zasady MFA](./media/overview/1021.png "zasad MFA")
* Przełącz stan zasad:

    ![Zasady MFA](./media/overview/403.png "zasad MFA")
* Wyświetl bieżący stan rejestracji:

    ![Zasady MFA](./media/overview/1022.png "zasad MFA")

Omówienie środowiska użytkownika Zobacz:

* [Uwierzytelnianie wieloskładnikowe rejestracji w usłudze flow](flows.md#multi-factor-authentication-registration).  
* [Logowania środowisk przy użyciu usługi Azure AD Identity Protection](flows.md).  

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

- Na **usługi Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **rejestracji uwierzytelniania wieloskładnikowego**.

    ![Zasady MFA](./media/overview/1019.png "zasad MFA")

## <a name="next-steps"></a>Kolejne kroki
* [Witrynie Channel 9: Usługi Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Włączanie usługi Azure Active Directory Identity Protection](enable.md)

* [Luki w zabezpieczeniach wykryte przez usługi Azure Active Directory Identity Protection](vulnerabilities.md)

* [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../active-directory-identity-protection-risk-events.md)

* [Powiadomienia usługi Azure Active Directory Identity Protection](notifications.md)

* [Podręcznik usługi Azure Active Directory Identity Protection](playbook.md)

* [Słownik platformy Azure Active Directory Identity Protection](glossary.md)

* [Środowisko logowania za pomocą usługi Azure AD Identity Protection](flows.md)

* [Usługa Azure Active Directory Identity Protection - sposób odblokowania użytkowników](howto-unblock-user.md)

* [Rozpoczynanie pracy z usługą Azure Active Directory Identity Protection i Microsoft Graph](graph-get-started.md)
