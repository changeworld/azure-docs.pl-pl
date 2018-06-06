---
title: Ochronę tożsamości usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure AD Identity Protection umożliwia ograniczenie możliwości osoby atakującej, która wykorzystać, którego bezpieczeństwo zostało naruszone tożsamości lub urządzenie i secure tożsamości lub urządzeń, które wcześniej podejrzenia lub znane naruszenia.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, usługa cloud app discovery, zarządzanie aplikacjami, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9658eaaf35d9b937d76926c73dba23d2a4989b3a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713188"
---
# <a name="azure-active-directory-identity-protection"></a>Ochrona tożsamości w usłudze Azure Active Directory

Azure Active Directory Identity Protection to funkcja wersji Azure AD Premium P2, które umożliwia:

- Wykrywanie potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji

- Skonfiguruj automatyczne odpowiedzi wykrytych podejrzanych działań, które są związane z tożsamości organizacji  

- Zbadaj podejrzane zdarzeń i podejmij odpowiednią akcję, aby je rozwiązać   


## <a name="getting-started"></a>Wprowadzenie

Microsoft ma zabezpieczone oparte na chmurze tożsamości przez ponad dekadę. Z usługi Azure Active Directory Identity Protection w danym środowisku, można użyć tego samego systemy ochrony, wykorzystywane przez firmę Microsoft do zabezpieczania tożsamości.

Większość naruszeń zabezpieczeń ma miejsce, gdy osoby atakujące uzyskania dostępu do środowiska kradzieży tożsamości użytkownika. Całościowo osoby atakujące stały się coraz bardziej skuteczne w wykorzystaniu naruszeń innych firm i za pomocą zaawansowanej wyłudzania. Jak osoba atakująca uzyska dostęp do nawet niski uprzywilejowanych kont, jest względnie łatwe uzyskanie dostępu do zasobów firmy ważne za pośrednictwem penetracji sieci.

W wyniku tego należy:

- Ochrona wszystkich tożsamości, niezależnie od ich poziom uprawnień

- Aktywne uniemożliwić złamany tożsamości są użyte

Odnajdywanie złamany tożsamości jest bez łatwe zadania. Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjną i heurystyki w celu wykrycia nieprawidłowości i podejrzane zdarzenia, które wskazują potencjalnie naruszony tożsamości. Przy użyciu tych danych, Identity Protection generuje raporty i alerty, które pozwalają ocenić wykrytych problemów i podjąć odpowiednie środki zaradcze lub akcji korygowania.

Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. Aby chronić tożsamości organizacji, umożliwiają konfigurowanie zasad ryzyka automatycznie odpowiadać na wykryte błędy, jeśli został osiągnięty poziom określonego ryzyka. Te zasady, oprócz innych kontroli dostępu warunkowego zapewniane przez usługę Azure Active Directory i EMS, można automatycznie blokować lub zainicjować akcji korygowania adaptacyjną, które resetowania haseł włącznie i wymuszania uwierzytelnianie wieloskładnikowe.


#### <a name="identity-protection-capabilities"></a>Funkcje ochrony tożsamości

**Wykrywanie luk w zabezpieczeniach i ryzykowne kont:**  

* Zapewnianie niestandardowych zalecenia dotyczące poprawy ogólny stan zabezpieczeń przez wyróżnianie luk w zabezpieczeniach
* Obliczanie poziomów ryzyka do logowania
* Obliczanie poziomów ryzyka użytkownika


**Badanie zdarzenia ryzyka:**

* Wysyłanie powiadomienia o zdarzeniach ryzyka
* Badania ryzyka zdarzeń za pomocą odpowiednich i kontekstowych informacji
* Zapewnienie podstawowych przepływów pracy do śledzenia kontroli
* Zapewniając łatwy dostęp do akcji korygowania, takich jak Resetowanie hasła

**Zasady dostępu warunkowego opartego na ryzyka:**

* Zasady, aby ograniczyć ryzykowne logowania przez blokowanie logowania lub wymaganie uwierzytelniania wieloskładnikowego wyzwania
* Zasady na wartość Blokuj lub kont użytkowników ryzykowne bezpieczne
* Zasady, aby wymagać od użytkowników rejestracji w usłudze Multi-Factor authentication



## <a name="identity-protection-roles"></a>Role ochrony tożsamości

Na potrzeby równoważenia obciążenia zarządzaniem wokół implementacji ochronę tożsamości, można przypisać kilka ról. Azure AD Identity Protection obsługuje 3 role katalogu:

| Rola                         | Możliwość                          | Nie można wykonać
| :--                          | ---                                |  ---   |
| Administrator globalny         | Pełny dostęp do ochrony tożsamości, dołączyć Identity Protection| |
| Administrator zabezpieczeń       | Pełny dostęp do Identity Protection | Dołączyć ochronę tożsamości, zresetuj hasła dla użytkownika |
| Czytelnik zabezpieczeń              | Dostęp tylko do odczytu do Identity Protection | Dołączyć ochronę tożsamości użytkowników remidiate skonfigurować zasady, resetowania haseł |




Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Wykrywanie

### <a name="vulnerabilities"></a>Luki w zabezpieczeniach

Azure Active Directory Identity Protection analizy konfiguracji i wykrywa luk w zabezpieczeniach, które mogą mieć wpływ na tożsamości użytkownika. Aby uzyskać więcej informacji, zobacz [luk w zabezpieczeniach wykrywanych przez usługę Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Usługi Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjną i heurystyki do wykrycia podejrzanych działań, które są związane z tożsamości użytkownika. System tworzy rekord dla każdego wykrytego podejrzane działania. Te rekordy są także nazywane zdarzenia ryzyka.  
Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Badanie
Podróży za pomocą ochrony tożsamości zwykle zaczyna się od pulpit nawigacyjny ochrony tożsamości.

![Korygowanie](./media/active-directory-identityprotection/1000.png "korygowania")

Pulpit nawigacyjny umożliwia dostęp do:

* Raporty takie jak **użytkownicy oflagowani ryzyka**, **ryzyka zdarzenia** i **luk w zabezpieczeniach**
* Ustawienia, takie jak konfiguracja sieci **zasady zabezpieczeń**, **powiadomienia** i **rejestracji usługi Multi-Factor authentication**

Zwykle to punkt startowy dla badania, czyli proces oceny działania, dzienników i inne istotne informacje powiązane ze zdarzeniem ryzyka zdecydować, czy korygowania lub ograniczenie kroki są niezbędne, i jak tożsamość zostało naruszone i zrozumieć korzystania ze złamanymi zabezpieczeniami tożsamości.

Można powiązać działaniach badań do [powiadomienia](active-directory-identityprotection-notifications.md) usługi Azure Active Directory Protection wysyła na wiadomości e-mail.

Poniższe sekcje zawierają więcej szczegółowych informacji i kroki, które są związane z dochodzenia.  


## <a name="risky-sign-ins"></a>Ryzykowne logowania

Usługa Azure Active Directory wykryje [ryzyka typów zdarzeń](active-directory-reporting-risk-events.md#risk-event-types) w czasie rzeczywistym i w trybie offline. Każde zdarzenie zagrożenia wykrytego dla logowanie użytkownika przyczynia się do pojęcie logiczne, nazywane ryzykowne logowania. Ryzykowne logowanie jest wskaźnik prób logowania, które nie mogły zostać wykonane przez uprawnionego właściciela konta użytkownika.


### <a name="sign-in-risk-level"></a>Poziom ryzyka logowania

Poziom ryzyka logowania jest wskazanie (wysoki, średni lub niski) prawdopodobieństwa, że próba logowania nie została wykonana przez wiarygodnego właściciela konta użytkownika.

### <a name="mitigating-sign-in-risk-events"></a>Zmniejszenia zdarzenia logowania ryzyka

Środki zaradcze jest akcji, aby ograniczyć możliwość atakujący wykorzystać złamany tożsamości lub urządzenia bez przywrócenia tożsamości lub urządzenie to bezpieczne. Środki zaradcze nie można rozpoznać poprzednie zdarzenia logowania ryzyko związane z tożsamości lub urządzenia.

Aby uniknąć automatycznie ryzykowne logowania, można skonfigurować ryzyka logowania zasad zabezpieczeń. Korzystając z tych zasad, należy wziąć pod uwagę poziom ryzyka użytkownika lub przy logowaniu do blokowania ryzykowne logowania lub użytkownik przeprowadzać uwierzytelnianie wieloskładnikowe. Te akcje mogą uniemożliwiać osobie atakującej wykorzystanie kradzieży tożsamości, aby spowodować szkody i może spowodować pewien czas do zabezpieczania tożsamości.

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń logowania ryzyka
Zasady logowania ryzyko jest zasady dostępu warunkowego, która ocenia ryzyko dla określonych logowanie i stosuje środki zaradcze, na podstawie wstępnie zdefiniowane warunki i zasady.

![Zasady logowania ryzyka](./media/active-directory-identityprotection/1014.png "logowania zasad ryzyka")

Azure AD Identity Protection pomaga w zarządzaniu łagodzenie ryzykowne logowania umożliwiając:

* Ustaw użytkowników i grup, których dotyczy zasada:

    ![Zasady logowania ryzyka](./media/active-directory-identityprotection/1015.png "logowania zasad ryzyka")
* Należy ustawić logowania ryzyka poziomu próg (niski, średni lub wysoki) wyzwalania zasad:

    ![Zasady logowania ryzyka](./media/active-directory-identityprotection/1016.png "logowania zasad ryzyka")
* Ustaw formanty mają być egzekwowane, gdy wyzwala zasad:  

    ![Zasady logowania ryzyka](./media/active-directory-identityprotection/1017.png "logowania zasad ryzyka")
* Przełącz stan tej zasady:

    ![Rejestracja usługi MFA](./media/active-directory-identityprotection/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed uaktywnieniem go:

    ![Zasady logowania ryzyka](./media/active-directory-identityprotection/1018.png "logowania zasad ryzyka")

#### <a name="what-you-need-to-know"></a>Co należy wiedzieć
Można skonfigurować zasady zabezpieczeń ryzyka logowania, aby wymusić uwierzytelnianie wieloskładnikowe:

![Zasady logowania ryzyka](./media/active-directory-identityprotection/1017.png "logowania zasad ryzyka")

Jednak ze względu na bezpieczeństwo, to ustawienie działa tylko dla użytkowników, które zostały już zarejestrowane w usłudze Multi-Factor authentication. Jeśli spełniony jest warunek wymaganie uwierzytelniania wieloskładnikowego dla użytkownika, który nie jest jeszcze zarejestrowany w usłudze Multi-Factor authentication, użytkownik jest zablokowany.

Najlepszym rozwiązaniem Jeśli chcesz wymusić uwierzytelnianie wieloskładnikowe ryzykowne logowania, wykonaj następujące czynności:

1. Włącz [zasady rejestracji usługi Multi-Factor authentication](#multi-factor-authentication-registration-policy) określonych użytkowników.
2. Wymaga odpowiednich użytkowników, aby logowania w sesji ryzykowne do wykonania rejestracji usługi MFA

Wykonanie tych kroków gwarantuje, że uwierzytelnianie wieloskładnikowe jest wymagany dla ryzykownych logowanie.

#### <a name="best-practices"></a>Najlepsze praktyki
Wybieranie **wysokiej** próg zmniejsza liczbę razy zasadę wyzwoleniu oraz zminimalizować wpływ na użytkowników.  

Jednak nie obejmuje **małej** i **średni** logowania oznaczona flagą ryzyko związane z zasad, które nie mogą blokować osoba atakująca możliwości wykorzystania złamany tożsamości.

Podczas ustawiania zasad

* Wyklucz użytkowników, którzy nie / nie można wprowadzić uwierzytelnianie wieloskładnikowe
* Wyklucz użytkowników, w których włączenie zasad nie jest praktyczne ustawień regionalnych (na przykład brak dostępu do działu pomocy technicznej)
* Wyklucz użytkowników, które mogą generować dużą alarmów false (deweloperów, analityków zabezpieczeń)
* Użyj **wysokiej** próg podczas początkowej zasad zbiorczego, lub jeśli należy zminimalizować problemy, które zostały odebrane przez użytkowników końcowych.
* Użyj **małej** progu, jeśli organizacja wymaga wyższego poziomu bezpieczeństwa. Wybieranie **małej** próg wprowadzono dodatkowe użytkownika logowania wyzwania, ale zwiększyć bezpieczeństwo.

Jest zalecana domyślna w przypadku większości organizacji do skonfigurowania reguły dla **średni** próg uzyskanie równowagi między użyteczność i zabezpieczeń.

Zasady logowania ryzyka są:

* Stosowane do całego ruchu w przeglądarce i logowania korzystających z nowoczesnego uwierzytelniania.
* Nie dotyczy aplikacji przy użyciu starszych protokołów zabezpieczeń przez wyłączenie punkt końcowy protokołu WS-Trust w federacyjnym dostawców tożsamości, takie jak usługi AD FS.

**Zdarzenia o podwyższonym ryzyku** w konsoli programu Identity Protection Wyświetla listę wszystkich zdarzeń:

* Ta zasada została zastosowana do
* Można sprawdzić działanie i ustalić, czy akcja została odpowiednie

Omówienie powiązane funkcje użytkownika Zobacz:

* [Ryzykowne odzyskiwania logowania](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Ryzykowne logowania zablokowane](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Logowanie, korzystając z usługi Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Aby otworzyć okno dialogowe elementami konfiguracji**:

- Na **Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **logowania zasad ryzyka**.

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1014.png "zasad ryzyka użytkownika")



## <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem

Wszystkie aktywne [ryzyka zdarzenia](active-directory-identity-protection-risk-events.md) zostały wykryte przez usługę Azure Active Directory dla użytkownika współtworzyć pojęcie logiczne o nazwie użytkownika ryzyka. Użytkownik oznaczona flagą ryzyko jest wskaźnikiem dla konta użytkownika, który może być zagrożone.

![Użytkownicy oflagowani w związku z ryzykiem](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Poziom ryzyka użytkownika

Poziom ryzyka użytkownika będzie wskazywać prawdopodobieństwo, że tożsamość użytkownika została naruszona poufność (wysoki, średni lub niski). Jest obliczana na podstawie zdarzeń ryzyka użytkownika, które są skojarzone z tożsamością użytkownika.

Stan zdarzenia ryzyko jest **Active** lub **zamknięte**. Tylko ryzyka zdarzenia, które są **Active** przyczyniają się do obliczania poziomu ryzyka użytkownika.

Poziom ryzyka użytkownika jest obliczana przy użyciu następujących danych wejściowych:

* Zdarzenia aktywne ryzyka wpływu na użytkownika
* Poziom ryzyka tych zdarzeń
* Określa, czy wykonano wszystkie akcje naprawcze wykonane

![Ryzyko użytkownika](./media/active-directory-identityprotection/1031.png "zagrożeń użytkownika")

Umożliwia tworzenie zasad dostępu warunkowego, które blokują ryzykowne użytkownikom logowanie użytkownika poziomów ryzyka lub mogą bezpiecznie zmienić swoje hasło.

### <a name="closing-risk-events-manually"></a>Zamknięcie zdarzenia o podwyższonym ryzyku ręcznie

W większości przypadków potrwa akcji korygowania, takich jak bezpieczny resetowania hasła, aby automatycznie zamknąć zdarzenia ryzyka. Jednak to może nie być możliwe.  
To, na przykład sytuacji, gdy:

* Użytkownik z zdarzenia Active ryzyka został usunięty.
* Badanie wykaże, że zdarzenie zagrożenia zgłoszony został wykonać przez wiarygodnego użytkownika

Ponieważ zdarzenia ryzyka, które są **Active** przyczyniają się do obliczania ryzyka użytkownika, może być konieczne ręczne obniżyć poziom ryzyka zamknięcie zdarzenia o podwyższonym ryzyku ręcznie.  
W trakcie badania można wykonać dowolną z tych akcji, aby zmienić stan zdarzenia ryzyka:

![Akcje](./media/active-directory-identityprotection/34.png "akcje")

* **Rozwiąż** — Jeśli po zbadaniu zdarzenia ryzyka, trwało akcji korygowania odpowiednie poza ochrony tożsamości i uważasz, że zdarzenie ryzyka należy traktować jako zamknięty, oznaczenia zdarzeń jako rozwiązane. Rozwiązane zdarzeń ustawi stan zdarzenia ryzyka zamknięte i zdarzenia ryzyka już przyczyniają się do użytkownika ryzyka.
* **Oznacz jako fałszywie dodatnich** — w niektórych przypadkach można zbadać zdarzenia ryzyka i wykryć, czy został niepoprawnie oznaczone jako ryzykowne. Można ograniczyć liczbę wystąpień takich przez oznaczenie zdarzeń ryzyka jako fałszywie dodatnich. Dzięki temu algorytmów, aby zwiększyć w przyszłości klasyfikacji zdarzenia podobne uczenia maszynowego. Status zdarzenia fałszywie dodatnich **zamknięte** i nie wpływają one ryzyko użytkownika.
* **Ignoruj** — Jeśli nie miały żadnych działań korygujących, ale można usunąć z listy aktywne zdarzenie ryzyka można oznaczyć zdarzeniem ryzyka Ignoruj i stan zdarzenia zostanie zamknięty. Zignorowano zdarzenia nie przyczyniają się ryzyko użytkownika. Tej opcji należy używać tylko w niezwykłych okolicznościach.
* **Uaktywnij ponownie** -ryzyka zdarzenia, które zostały ręcznie zamknięty (przez wybranie **rozwiązać**, **wynik fałszywie dodatni**, lub **Ignoruj**) można ponownie uaktywnić, ustawienia zdarzenia stanu z powrotem do **Active**. Zdarzenia ponownie uaktywnione ryzyka przyczyniają się do obliczania poziomu ryzyka użytkownika. Nie można ponownie uaktywnić zamknięte przy użyciu funkcji korygowania (takie jak resetowania hasła bezpiecznego) zdarzenia ryzyka.

**Aby otworzyć okno dialogowe elementami konfiguracji**:

1. Na **Azure AD Identity Protection** bloku, w obszarze **zbadaj**, kliknij przycisk **ryzyka zdarzenia**.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1002.png "resetowania hasła ręczne")
2. W **ryzyka zdarzenia** kliknij zagrożenie.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1003.png "resetowania hasła ręczne")
3. W bloku ryzyka kliknij prawym przyciskiem myszy przez użytkownika.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1004.png "resetowania hasła ręczne")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Ręczne zamknięcie wszystkich zdarzeń ryzyka dla użytkownika
Zamiast ręcznie indywidualnie zamknięcie zdarzenia ryzyka dla użytkownika, Azure Active Directory Identity Protection zapewnia także metodę Zamknij wszystkie zdarzenia dla użytkownika z jednego kliknięcia.

![Akcje](./media/active-directory-identityprotection/2222.png "akcje")

Po kliknięciu **odrzucić wszystkie zdarzenia**, wszystkie zdarzenia zostaną zamknięte i użytkownika, którego dotyczy nie jest już na ryzyko.

### <a name="remediating-user-risk-events"></a>Zdarzenia o podwyższonym ryzyku korygując użytkownika

Korygowanie jest czynnością do zabezpieczania tożsamości lub urządzeń, które wcześniej podejrzenia lub znane naruszenia. Akcja korygowania przywraca tożsamości lub urządzenie to bezpieczne i usuwa poprzednie zdarzenia ryzyko związane z tożsamości lub urządzenia.

Aby skorygować użytkownika zdarzenia o podwyższonym ryzyku, można:

* Bezpieczne hasło zresetować ręcznie skorygować zdarzenia o podwyższonym ryzyku użytkownika
* Konfigurowanie zasad zabezpieczeń użytkownika ryzyko ograniczenia lub automatycznie korygować zdarzenia o podwyższonym ryzyku użytkownika
* Ponowne instalowanie obrazu zainfekowanych urządzeń  

#### <a name="manual-secure-password-reset"></a>Resetowanie ręczne bezpiecznego hasła
Bezpieczne hasło jest skuteczne korygowania w przypadku wielu zdarzeń ryzyka i wykonywana, automatycznie powoduje zamknięcie zdarzenia o podwyższonym ryzyku i ponownie oblicza poziom ryzyka użytkownika. Pulpit nawigacyjny ochrony tożsamości służy do inicjowania resetowania hasła dla użytkownika ryzykowne.

Określone okno zapewnia dwie różne metody, aby zresetować hasło:

**Zresetuj hasło** — wybierz tę opcję **wymagają od użytkownika do zresetowania swojego hasła** Aby zezwolić użytkownikowi na własnym odzyskania, jeśli użytkownik został zarejestrowany w usłudze Multi-Factor authentication. Podczas jego następnego logowania użytkownik będzie wymagane do rozwiązania pomyślnie żądanie uwierzytelniania wieloskładnikowego i następnie wymuszone, aby zmienić hasło. Ta opcja jest niedostępna, jeśli konto użytkownika nie jest już zarejestrowany uwierzytelnianie wieloskładnikowe.

**Hasło tymczasowe** — wybierz tę opcję **wygenerować hasło tymczasowe** natychmiast unieważnia istniejące hasło i utworzenie nowego hasła tymczasowego dla użytkownika. Alternatywny adres e-mail użytkownika lub menedżerem użytkownika, należy wysłać nowe hasło tymczasowe. Ponieważ hasło tymczasowe, użytkownik pojawi się monit o zmianę hasła podczas logowania.

![Zasady](./media/active-directory-identityprotection/1005.png "zasad")

**Aby otworzyć okno dialogowe elementami konfiguracji**:

1. Na **Azure AD Identity Protection** bloku, kliknij przycisk **użytkownicy oflagowani ryzyka**.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1006.png "resetowania hasła ręczne")
2. Z listy użytkowników wybierz użytkownika z ryzykiem co najmniej jednego zdarzenia.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1007.png "resetowania hasła ręczne")
3. W bloku użytkownika kliknij **resetowania hasła**.

    ![Resetowania hasła ręczne](./media/active-directory-identityprotection/1008.png "resetowania hasła ręczne")

### <a name="user-risk-security-policy"></a>Zasady zabezpieczeń użytkownika ryzyka
Zasady zabezpieczeń użytkownika ryzyko jest zasady dostępu warunkowego, które ocenia poziom ryzyka dla określonego użytkownika, a następnie stosuje akcje korygowania i środki zaradcze, na podstawie wstępnie zdefiniowane warunki i zasady.

![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1009.png "zasad ryzyka użytkownika")

Azure AD Identity Protection pomaga w zarządzaniu łagodzenia i korygowania oflagowane ryzyka, umożliwiając użytkownikom:

* Ustaw użytkowników i grup, których dotyczy zasada:

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1010.png "zasad ryzyka użytkownika")
* Należy ustawić użytkownika ryzyka poziomu próg (niski, średni lub wysoki) wyzwalania zasad:

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1011.png "zasad ryzyka użytkownika")
* Ustaw formanty mają być egzekwowane, gdy wyzwala zasad:

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1012.png "zasad ryzyka użytkownika")
* Przełącz stan tej zasady:

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/403.png "rejestracji usługi MFA")
* Przegląd i ocena wpływu zmiany przed uaktywnieniem go:

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1013.png "zasad ryzyka użytkownika")

Wybieranie **wysokiej** próg zmniejsza liczbę razy zasadę wyzwoleniu oraz zminimalizować wpływ na użytkowników.
Jednak nie obejmuje **małej** i **średni** użytkowników oznaczona flagą ryzyko związane z zasad, które mogą nie zapewnić tożsamości lub urządzenia, który zostały wcześniej podejrzanych lub znane naruszenia.

Podczas ustawiania zasad

* Wyklucz użytkowników, które mogą generować dużą alarmów false (deweloperów, analityków zabezpieczeń)
* Wyklucz użytkowników, w których włączenie zasad nie jest praktyczne ustawień regionalnych (na przykład brak dostępu do działu pomocy technicznej)
* Użyj **wysokiej** próg podczas początkowej zasad zbiorczego, lub jeśli należy zminimalizować problemy, które zostały odebrane przez użytkowników końcowych.
* Użyj **małej** progu, jeśli organizacja wymaga wyższego poziomu bezpieczeństwa. Wybieranie **małej** próg wprowadzono dodatkowe użytkownika logowania wyzwania, ale zwiększyć bezpieczeństwo.

Jest zalecana domyślna w przypadku większości organizacji do skonfigurowania reguły dla **średni** próg uzyskanie równowagi między użyteczność i zabezpieczeń.

Omówienie powiązane funkcje użytkownika Zobacz:

* [Złamania zabezpieczeń konta przepływu odzyskiwania](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Naruszone zablokowano konto przepływu](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Aby otworzyć okno dialogowe elementami konfiguracji**:

- Na **Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **zasad ryzyka użytkownika**.

    ![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1009.png "zasad ryzyka użytkownika")

### <a name="mitigating-user-risk-events"></a>Zmniejszenia zdarzenia o podwyższonym ryzyku użytkownika
Administratorzy mogą skonfigurować zasady zabezpieczeń ryzyka użytkownika, aby uniemożliwić użytkownikom na logowanie się w zależności od poziomu zagrożenia.

Blokowanie logowania:

* Uniemożliwia wygenerowanie nowego zdarzenia ryzyka użytkownika dla użytkownika, którego dotyczy
* Administratorzy mogą ręcznie skorygować zdarzenia o podwyższonym ryzyku wpływających na tożsamości użytkownika i przywrócenia stanu bezpiecznego



## <a name="multi-factor-authentication-registration-policy"></a>Zasady rejestracji uwierzytelniania wieloskładnikowego
Uwierzytelnianie wieloskładnikowe platformy Azure jest metodę weryfikacji tożsamości, która wymaga użycia więcej niż tylko nazwę użytkownika i hasło. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników i transakcji.  
Firma Microsoft zaleca wymagane uwierzytelnianie wieloskładnikowe platformy Azure logowania użytkownika, ponieważ jego:

* Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe
* Odgrywa kluczową rolę w przygotowywanie organizacji do ochrony i odzyskiwania z dokonywania konta

![Zasady użytkownika ryzyka](./media/active-directory-identityprotection/1019.png "zasad ryzyka użytkownika")

Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie wieloskładnikowe Azure?](authentication/multi-factor-authentication.md)

Azure AD Identity Protection pomaga w zarządzaniu wdrożenie uwierzytelniania wieloskładnikowego rejestracji przez skonfigurowanie zasad, które umożliwia:

* Ustaw użytkowników i grup, których dotyczy zasada:

    ![Zasady MFA](./media/active-directory-identityprotection/1020.png "zasad MFA")
* Ustawianie formantów, które mają być egzekwowane, gdy zasady wyzwala::  

    ![Zasady MFA](./media/active-directory-identityprotection/1021.png "zasad MFA")
* Przełącz stan tej zasady:

    ![Zasady MFA](./media/active-directory-identityprotection/403.png "zasad MFA")
* Wyświetl bieżący stan rejestracji:

    ![Zasady MFA](./media/active-directory-identityprotection/1022.png "zasad MFA")

Omówienie powiązane funkcje użytkownika Zobacz:

* [Uwierzytelnianie wieloskładnikowe rejestracji przepływu](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Logowanie napotyka przy użyciu usługi Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Aby otworzyć okno dialogowe elementami konfiguracji**:

- Na **Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **rejestracji usługi Multi-Factor authentication**.

    ![Zasady MFA](./media/active-directory-identityprotection/1019.png "zasad MFA")

## <a name="next-steps"></a>Kolejne kroki
* [Kanał 9: Usługi Azure AD i Pokaż tożsamości: Podgląd ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Włączenie ochrony tożsamości usługi Azure Active Directory](active-directory-identityprotection-enable.md)

* [Luk w zabezpieczeniach wykrywanych przez usługę Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Zdarzenia o podwyższonym ryzyku Azure Active Directory](active-directory-identity-protection-risk-events.md)

* [Azure Active Directory Identity Protection powiadomienia](active-directory-identityprotection-notifications.md)

* [Azure podręcznikowym ochronę tożsamości w usłudze Active Directory](active-directory-identityprotection-playbook.md)

* [Azure Active Directory Identity Protection słownik](active-directory-identityprotection-glossary.md)

* [Logowanie, korzystając z usługi Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory Identity Protection — sposób odblokowania użytkowników](active-directory-identityprotection-unblock-howto.md)

* [Wprowadzenie do usługi Azure Active Directory Identity Protection oraz Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
