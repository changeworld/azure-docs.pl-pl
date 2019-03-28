---
title: Planowanie zasad dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak zaplanować zasady dostępu warunkowego usługi Azure Active Directory.
services: active-directory
author: MicrosoftGuyJFlo
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4832055bffa72732991cb766ce88cc48c07530e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518715"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Instrukcje: Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory

Planowanie wdrożenia dostępu warunkowego jest niezwykle upewnij się, że strategia wymagany dostęp do aplikacji i zasobów są dostępne w Twojej organizacji. Powinni spędzać większość czasu na etapie planowania wdrożenia i projektowanie różnych zasad, wymagają, aby udzielić lub zablokować dostęp dla użytkowników, zgodnie z warunkami, które wybierzesz. W tym dokumencie opisano kroki, które należy podjąć w celu zaimplementowania zasad dostępu warunkowego w bezpieczne i skuteczne. Przed rozpoczęciem upewnij się, należy zrozumieć jak [dostępu warunkowego](overview.md) działa i kiedy należy go używać.


## <a name="what-you-should-know"></a>Co należy wiedzieć

Dostęp warunkowy można traktować jako strukturę, która pozwala na kontrolowanie dostępu do aplikacji i zasobów, zamiast autonomicznych funkcji Twojej organizacji. W związku z tym niektóre ustawienia dostępu warunkowego wymagają dodatkowych funkcji, należy skonfigurować. Na przykład, można skonfigurować zasadę, która odpowiada konkretnym [poziom ryzyka logowania](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). Zasada, która opiera się na poziom ryzyka logowania wymaga jednak [usługi Azure Active Directory identity protection](../identity-protection/overview.md) włączenia.

Jeśli wymagane są dodatkowe funkcje, również może być konieczne uzyskanie licencji powiązane. Na przykład dostęp warunkowy jest funkcją usługi Azure AD Premium P1, ochrony tożsamości wymaga licencji usługi Azure AD Premium P2.

Istnieją dwa typy zasad dostępu warunkowego: linii bazowej i standard. A [bazowymi zasadami](baseline-protection.md) zasady dostępu warunkowego wstępnie zdefiniowane. Celem tych zasad jest upewnij się, że masz co najmniej poziom odniesienia z włączoną obsługą zabezpieczeń. Zasady linii bazowej. Zasady linii bazowej są dostępne we wszystkich wersjach programu Azure AD i zapewniają tylko opcje dostosowywania ograniczone. Jeśli scenariusz wymaga większej elastyczności, wyłączyć zasady w linii bazowej i implementowanie wymagań w przypadku standardowych zasad niestandardowych.

W zasadach dostępu warunkowego standardowego możesz dostosować wszystkie ustawienia, aby dostosować zasady aby Twoje wymagania biznesowe. Standardowe zasady wymaga licencji usługi Azure AD Premium P1.




## <a name="draft-policies"></a>Zasady narzędzia draft

Dostęp warunkowy usługi Azure Active Directory umożliwia zebranie ochrony aplikacji w chmurze na nowy poziom. W tym nowy poziom uzyskać dostęp aplikacji w chmurze opiera się na oceniania zasady dynamicznego, zamiast konfiguracji statycznej dostępu. Za pomocą zasad dostępu warunkowego, możesz zdefiniować odpowiedzi (**to**) do warunku dostępu (**w takim przypadku**).

![Przyczyna i odpowiedzi](./media/plan-conditional-access/10.png)

Definiowanie zasad dostępu warunkowego, co potrzebne do wdrożenia przy użyciu tego modelu planowania. Ćwiczenie planowania:

- Pomaga w odpowiedzi i warunki dla każdej zasady.
- Wyniki w wykazie zasad dostępu warunkowego dobrze udokumentowane dla Twojej organizacji. 

Twój wykaz służy do oceny, czy implementacji zasad odzwierciedla wymagania biznesowe Twojej organizacji. 

Użyj następującego przykładowego szablonu, aby utworzyć zasady dostępu warunkowego dla Twojej organizacji:

|Gdy *to* się dzieje:|Następnie wykonaj *to*:|
|-|-|
|Zostanie podjęta próba dostępu:<br>— Do aplikacji w chmurze*<br>— przez użytkowników i grup*<br>Przy użyciu:<br>-Stan 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platform urządzeń)|Blokuj dostęp do aplikacji|
|Zostanie podjęta próba dostępu:<br>— Do aplikacji w chmurze*<br>— przez użytkowników i grup*<br>Przy użyciu:<br>-Stan 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platform urządzeń)|Udzielanie dostępu z (i):<br>-Wymaganie 1 (na przykład, MFA)<br>-Wymaganie 2 (na przykład zgodność urządzenia)|
|Zostanie podjęta próba dostępu:<br>— Do aplikacji w chmurze*<br>— przez użytkowników i grup*<br>Przy użyciu:<br>-Stan 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platform urządzeń)|Udzielanie dostępu z (lub):<br>-Wymaganie 1 (na przykład, MFA)<br>-Wymaganie 2 (na przykład zgodność urządzenia)|

Co najmniej **w takim przypadku** definiuje podmiotu zabezpieczeń (**kto**) który próbuje uzyskać dostęp aplikacji w chmurze (**co**). Jeśli to konieczne, możesz również uwzględnić **jak** odbywa się próba dostępu. W funkcji dostępu warunkowego, elementy definiują kto, co się stanie i jak są znane jako warunki. Aby uzyskać więcej informacji, zobacz [jakie są warunki dostępu warunkowego usługi Azure Active Directory?](conditions.md) 

Za pomocą **to zrobić**, zdefiniuj odpowiedź zasad do warunku dostępu. W odpowiedzi można zablokować lub przyznać dostęp z dodatkowymi wymaganiami, na przykład uwierzytelnianie wieloskładnikowe (MFA). Aby uzyskać pełny przegląd zobacz [co to są dostępu kontroluje dostępu warunkowego usługi Azure Active Directory?](controls.md)  
 

Kombinacja warunków i mechanizmów kontroli dostępu tworzy zasadę dostępu warunkowego.

![Przyczyna i odpowiedzi](./media/plan-conditional-access/51.png)


Aby uzyskać więcej informacji, zobacz [co to są wymagane w celu zmiany celu zasad działają](best-practices.md#whats-required-to-make-a-policy-work).

W tym momencie jest odpowiedni moment, aby decyzję w sprawie standard nazewnictwa dla zasad. Zasady nazewnictwa pomaga znaleźć zasady i zrozumienie ich przeznaczenie bez ich otwierania w portalu administracyjnego platformy Azure. Należy nadać nazwę zasady, aby wyświetlić:

- Numer sekwencyjny
- W aplikacji w chmurze, których on obowiązuje
- Odpowiedź
- Kto ma zastosowanie do
- Kiedy ma zastosowanie (jeśli dotyczy)
 
![Standard nazewnictwa](./media/plan-conditional-access/11.png)

Gdy nazwę opisową ułatwia zapewnienie Omówienie wdrożenia dostępu warunkowego, numer sekwencji jest przydatne, jeśli muszą odwoływać się do zasad w konwersacji. Na przykład jeśli administrator innymi możesz skontaktować się telefonicznie, możesz poprosić go, aby otworzyć przystawkę Zasady EM063 do rozwiązania problemu.



Na przykład następująca Nazwa stwierdza, że zasady wymagają usługi MFA dla użytkowników zewnętrznych sieci za pomocą aplikacji Dynamics CRP marketing:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`


Oprócz aktywnego zasad, zaleca się również zasady Implementowanie wyłączone, które działają jako pomocniczą [kontroli dostępu odporne na błędy w scenariuszach awaryjnego/awaria](../authentication/concept-resilient-controls.md). Usługi standard nazewnictwa awaryjny zasad powinien zawierać kilka elementów: 

- `ENABLE IN EMERGENCY` na początku, aby nazwa wyróżnić się wśród innych zasad.

- Nazwa przerw w działaniu, które mają dotyczyć.

- Szeregowania numer sekwencyjny mają ułatwić administratorowi znać, w jakiej kolejności zasad powinno być włączone. 


Na przykład następująca nazwa wskazuje, że ta zasada jest pierwszej zasady z czterema, które należy włączyć w przypadku przerwy w działaniu usługi MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`







## <a name="plan-policies"></a>Planowanie zasad

Podczas planowania rozwiązania zasad dostępu warunkowego należy ocenić, czy należy utworzyć zasady w celu osiągnięcia następujących rezultatów. 


### <a name="block-access"></a>Blokuj dostęp

Opcja w celu zablokowania dostępu jest zaawansowane ponieważ go:

- Trumps wszystkich przypisań dla użytkownika

- Ma uprawnienia do blokowania w całej organizacji, zalogowanie się do swojej dzierżawy
 
Jeśli chcesz zablokować dostęp dla wszystkich użytkowników w co najmniej powinno wykluczać jednego użytkownika (kont dostępu awaryjnego zazwyczaj) z zasad. Aby uzyskać więcej informacji, zobacz [Wybieranie użytkowników i grup](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>Wymaganie usługi MFA

Aby uprościć środowisko logowania użytkowników, można zezwolić im na Zaloguj się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Zazwyczaj istnieją co najmniej kilka scenariuszy, dla których zaleca się wymagają silniejszego formularz weryfikacji konta. Za pomocą zasad dostępu warunkowego można ograniczyć wymaganie usługi MFA w niektórych scenariuszach. 

Typowe przypadki użycia, aby wymagać uwierzytelniania Wieloskładnikowego są dostępu:

- [Przez administratorów](baseline-protection.md#require-mfa-for-admins)
- [Do określonych aplikacji](app-based-mfa.md) 
- [Z lokalizacji sieciowej, nie ufasz](untrusted-networks.md).


### <a name="respond-to-potentially-compromised-accounts"></a>Odpowiadanie na mogą mieć złamane zabezpieczenia konta

Za pomocą zasad dostępu warunkowego można zaimplementować automatycznych odpowiedzi do logowania z tożsamości mogą mieć złamane zabezpieczenia. Prawdopodobieństwo, że konto zostało zaatakowane jest wyrażona w formie poziomów ryzyka. Istnieją dwa poziomy ryzyka, obliczana na podstawie tożsamości ochrony: ryzyka logowania i ryzyka związanego z użytkownikiem. Aby zaimplementować odpowiedź ryzyka logowania, masz dwie opcje:

- [Warunkiem ryzyka logowania](conditions.md#sign-in-risk) w zasadach dostępu warunkowego
- [Zasady ryzyka logowania](../identity-protection/howto-sign-in-risk-policy.md) identity Protection 

Adresowanie ryzyka logowania jako warunku jest preferowaną metodą, ponieważ zapewnia dodatkowe opcje dostosowywania.

Poziom ryzyka użytkownika jest dostępna tylko [zasad ryzyka dla użytkownika](../identity-protection/howto-user-risk-policy.md) w ochronę tożsamości. 

Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory Identity Protection?](../identity-protection/overview.md) 


### <a name="require-managed-devices"></a>Wymaganie urządzeń zarządzanych

Rozprzestrzenianie obsługiwanych urządzeń, dostęp do zasobów chmury pozwala zwiększyć produktywność użytkowników. Na drugiej strony prawdopodobnie nie ma niektórych zasobów w danym środowisku, aby były dostępne dla urządzeń z poziomu ochrony nieznany. Dla zasobów, których dotyczy problem należy wymagać, czy użytkownicy mogą dostęp tylko do ich za pomocą urządzenia zarządzanego. Aby uzyskać więcej informacji, zobacz [sposób wymagać zarządzanych urządzeń w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Wymaganie zatwierdzonych aplikacji klienckich

Jedną z pierwszych decyzji, które należy wykonać w przypadku przynoszą własne urządzenia (BYOD) scenariuszy, czy trzeba zarządzać całym urządzeniem, czy po prostu dane na nim. Pracownicy używają urządzeń przenośnych zarówno osobistych i służbowych. Upewnij się, że Twoi pracownicy mogą być produktywność, ma zapobiegać utracie danych. Przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) można ograniczyć dostęp do aplikacji w chmurze do zatwierdzonych aplikacji klienckich, które może chronić dane firmowe. Aby uzyskać więcej informacji, zobacz [jak wymagać zatwierdzonych aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Usługa Azure AD obsługuje niektóre z najczęściej używanych protokołów uwierzytelniania i autoryzacji, w tym starsze uwierzytelnianie. Jak można zapobiec aplikacji korzystających z uwierzytelniania starszej wersji, uzyskiwanie dostępu do zasobów dzierżawy Zalecane jest po prostu je zablokować przy użyciu zasad dostępu warunkowego. Jeśli to konieczne, możesz zezwolić tylko niektórych użytkowników i określone lokalizacje sieciowe korzystać z aplikacji, które są oparte na starszych uwierzytelniania. Aby uzyskać więcej informacji, zobacz [jak zablokować starsze uwierzytelnianie do usługi Azure AD przy użyciu dostępu warunkowego](block-legacy-authentication.md).


## <a name="test-your-policy"></a>Testowanie zasad

Zanim zetknie zasad w środowisku produkcyjnym, należy przetestować jest sprawdzenie, czy zachowanie jest zgodne z oczekiwaniami.

1. Utwórz użytkowników testowych

2. Tworzenie planu testu

3. Konfigurowanie zasad

4. Oceń symulowane logowania

5. Testowanie zasad

6. Czyszczenie



### <a name="create-test-users"></a>Utwórz użytkowników testowych

Aby sprawdzić zasady, należy utworzyć zbiór użytkowników, która jest podobna do użytkowników w danym środowisku. Tworzenie użytkowników testowych pozwala sprawdzić, czy zasady działają zgodnie z oczekiwaniami, zanim mają wpływ na rzeczywistych użytkowników i zakłócać ich dostęp do aplikacji i zasobów. 


Niektóre organizacje mają dzierżaw testu do tego celu. Jednak może być trudne do odtworzenia wszystkich warunków i aplikacji w dzierżawie testowej, aby w pełni przetestować wyniki zasad. 

### <a name="create-a-test-plan"></a>Tworzenie planu testu

Plan testu jest posiadanie porównanie oczekiwanych wyników i rzeczywiste wyniki. Powinien zawsze być Oczekiwanie przed testowaniem coś. W poniższej tabeli przedstawiono przykład przypadków testowych. Dostosuj scenariuszy i oczekiwanych wyników, w oparciu o konfiguracji z zasad dostępu Warunkowego.

|Zasady |Scenariusz |Oczekiwany wynik | Wynik |
|---|---|---|---|
|[Wymagać uwierzytelniania Wieloskładnikowego poza pracą](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowane zalogowaniu się użytkownika do *aplikacji* while w zaufanej lokalizacji / pracy|Użytkownik nie jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Wymagać uwierzytelniania Wieloskładnikowego poza pracą](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowane zalogowaniu się użytkownika do *aplikacji* podczas nie w zaufanej lokalizacji / pracy|Użytkownik jest monitowany o MFA i zalogować się pomyślnie| |
|[Wymagać uwierzytelniania Wieloskładnikowego (dla administratora)](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|Administrator globalny, który zaloguje się do *aplikacji*|Administrator otrzyma monit o uwierzytelnianie wieloskładnikowe| |
|[Ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Użytkownik zaloguje się do *aplikacji* przy użyciu [Tor przeglądarki](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Administrator otrzyma monit o uwierzytelnianie wieloskładnikowe| |
|[Zarządzanie urządzeniami](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik spróbuje się zalogować z autoryzowanych urządzeń|Udzielono dostępu| |
|[Zarządzanie urządzeniami](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik próbuje Zaloguj się za pomocą nieautoryzowanego urządzenia|Zablokowany dostęp| |
|[Zmiana hasła dla ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Autoryzowany użytkownik próbuje zalogować się przy użyciu przejęcie poświadczeń (o wysokim ryzyku Zaloguj się)|Użytkownik jest monitowany o zmianę hasła lub dostęp jest zablokowany, oparte na zasadach| |


### <a name="configure-the-policy"></a>Konfigurowanie zasad

Zarządzanie zasadami dostępu warunkowego jest zadanie ręczne. W witrynie Azure portal można zarządzać zasady dostępu warunkowego w jednej centralnej lokalizacji - stronę dostępu warunkowego. Jeden punkt wejścia do strony dostęp warunkowy jest **zabezpieczeń** sekcji **usługi Active Directory** okienka nawigacji. 

![Dostęp warunkowy](media/plan-conditional-access/03.png)


Jeśli chcesz dowiedzieć się więcej na temat sposobu tworzenia zasad dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md). Ten przewodnik Szybki Start pomaga:

- Zapoznanie się z interfejsem użytkownika.
- Uzyskaj pierwsze wrażenie sposobie działania dostępu warunkowego. 


### <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działają one zgodnie z oczekiwaniami. Pierwszym krokiem używać dostępu warunkowego [co zrobić, jeśli narzędzie zasad](what-if-tool.md) do symulowania logowania dla użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.

>[!NOTE]
> Uruchomienie symulowanego daje wyobrażenie wpływu zasad dostępu warunkowego, nie zastępuje rzeczywiste przebiegu testu.


### <a name="test-your-policy"></a>Testowanie zasad

Uruchamianie przypadków testowych zgodnie z planem testu. W tym kroku, możesz uruchomić za pomocą testu end-to-end poszczególnych zasad dla użytkowników testowych, aby upewnij się, że każda zasada działa poprawnie. Użyj scenariusze utworzone powyżej wykonanie każdego testu.

Koniecznie upewnij się, że testujesz kryteria wykluczenia zasad. Na przykład może wyłączyć użytkownika bądź grupy z zasad, które wymagają usługi MFA. Jeśli wykluczonych użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, należy przetestować w związku z tym ponieważ kombinacji innych zasad może wymagać uwierzytelniania Wieloskładnikowego dla tych użytkowników.


### <a name="cleanup"></a>Czyszczenie

Procedura czyszczenia składa się z następujących czynności:

1. Wyłączenie tych zasad.

2. Usuń przypisanych użytkowników i grup.

3. Usuń użytkowników testowych.  




## <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Jeśli nowe zasady są gotowe dla danego środowiska, wdrażać je w fazach::

- Zapewniają komunikację zmian wewnętrznych, użytkowników końcowych.

- Uruchom wprowadzaj w małej grupie użytkowników, a następnie sprawdź, czy zasady zachowuje się zgodnie z oczekiwaniami.

- Po rozwinięciu zasady w celu uwzględnienia większej liczby użytkowników, w dalszym ciągu wykluczenia wszystkich administratorów. Administratorzy z wyjątkiem gwarantuje, że nadal przez kogoś innego ma dostęp do zasad wymagających używania Jeśli wymagana jest zmiana.

- Zastosuj zasady do wszystkich użytkowników tylko wtedy, gdy jest to wymagane.

Najlepszym rozwiązaniem należy utworzyć co najmniej jedno konto użytkownika, który jest:

- Dedykowane administrowanie zasadami

- Wykluczone ze wszystkich zasad

 



## <a name="rollback-steps"></a>Kroki wycofywania

W razie potrzeby można wycofać nowo wdrożonych zasad można wycofać należy użyć co najmniej jeden z następujących opcji:

1. **Wyłącz zasady** — wyłączanie zasad zapewnia, że nie ma zastosowania, gdy użytkownik próbuje się zalogować. Zawsze możesz wrócić i włączyć zasady, jeśli chcesz z niego korzystać.

    ![Wyłącz zasady](media/plan-conditional-access/07.png)

2. **Wykluczanie użytkownika / grupy z zasad** — Jeśli użytkownik nie może uzyskać dostęp do aplikacji, możesz wykluczyć użytkowników z zasad

    ![Użytkownicy Exluce](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Ta opcja powinna należy używać oszczędnie, tylko w sytuacji, w którym użytkownik jest zaufany. Użytkownik powinien zostać ponownie dodane do zasad lub grupy tak szybko, jak to możliwe.

3. **Usuń zasady** — Jeśli to ustawienie zasad nie jest już potrzebne, usuń go.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [dokumentacji dostępu warunkowego usługi Azure AD](index.yml) , aby uzyskać przegląd dostępnych informacji.
