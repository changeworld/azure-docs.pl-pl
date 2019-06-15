---
title: Można skonfigurować okresów istnienia tokenu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić okresy istnienia tokenów wystawionych przez usługę Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: ryanwi
ms.custom: aaddev, annaba
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1c68d9254b0da2e5296c83d8dd4c95091fde1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111807"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Można skonfigurować okresów istnienia tokenu w usłudze Azure Active Directory (wersja zapoznawcza)

Można określić okres istnienia tokenu wystawionego przez usługę Azure Active Directory (Azure AD). Możesz ustawić okresów istnienia tokenu dla wszystkich aplikacji w Twojej organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w Twojej organizacji.

> [!IMPORTANT]
> Po czekamy na informacje od klientów w trakcie okresu zapoznawczego, możemy zastąpić funkcji można konfigurować okresów istnienia tokenu przy użyciu [możliwości zarządzania sesji uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106) w funkcji dostępu warunkowego usługi Azure AD. Ta funkcja zostaną wycofane 1 listopada 2019 r. Jeśli używasz zasad można skonfigurować okres istnienia tokenu, przełącz się do nowej funkcji dostępu warunkowego. 

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane na poszczególne aplikacje lub wszystkie aplikacje w organizacji. Każdego typu zasad ma unikatowy struktury, zestaw właściwości, które są stosowane do obiektów, które są przypisane.

Zasady można wyznaczyć jako domyślną zasadę dla Twojej organizacji. Zasady są stosowane do dowolnej aplikacji w organizacji, dopóki nie zostanie on przesłonięty przez zasady z wyższym priorytetem. Można także przypisać zasady do określonych aplikacji. Kolejność pierwszeństwa, zależy od typu zasad.

> [!NOTE]
> Zasady można skonfigurować okres istnienia tokenu nie jest obsługiwane dla usługi SharePoint Online.  Mimo, że masz możliwość tworzenia tych zasad za pomocą programu PowerShell, usługi SharePoint Online nie potwierdzi tych zasad. Zapoznaj się [blog usługi SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) Aby dowiedzieć się więcej o konfigurowaniu przekroczeń limitu czasu bezczynności sesji.
>* Domyślny okres istnienia tokenu dostępu usługi SharePoint Online to 1 godzina. 
>* Maksymalny czas nieaktywności domyślnej usługi SharePoint Online token odświeżania wynosi 90 dni.

## <a name="token-types"></a>Typy tokenów

Można ustawić zasady okres istnienia tokenu dla tokenów odświeżania, tokeny dostępu, sesji tokenów i tokenów Identyfikatora.

### <a name="access-tokens"></a>Tokeny dostępu

Klienci używają tokenów dostępu do uzyskania dostępu do chronionego zasobu. Token dostępu może służyć tylko dla określonej kombinacji użytkowników, klient i zasobów. Tokeny dostępu nie można odwołać i obowiązują do momentu wygaśnięcia ich. Złośliwy aktora, który ma uzyskać token dostępu można go użyć dla zakresu cały okres ich istnienia. Dostosowywanie okresu istnienia tokenu dostępu jest kompromis między zwiększanie wydajności systemu i zwiększenie ilości czasu, że klient zachowuje dostępu po wyłączeniu konta użytkownika. Ulepszony system wydajność jest osiągana, zmniejszając liczbę razy, gdy klient musi uzyskać tokenu dostępu od nowa.  Wartość domyślna to 1 godzina — po 1 godzinie, klient musi być tokenu odświeżania (zwykle w trybie dyskretnym) uzyskać nowy token odświeżania i tokenu dostępu. 

### <a name="refresh-tokens"></a>Tokenów odświeżania

Gdy klient uzyskuje token dostępu, aby uzyskać dostęp do chronionego zasobu, klient również odbiera token odświeżania. Token odświeżania jest używany do uzyskiwania dostępu do nowych/odświeżania tokenu pary, po upływie bieżącego tokenu dostępu. Token odświeżania jest powiązany z kombinacją użytkownika i klienta. Token odświeżania może być [w dowolnej chwili odebrać](access-tokens.md#token-revocation), a ważności tokenu jest sprawdzana za każdym razem, gdy token jest używany.  Odśwież tokeny nie zostaną odwołane, gdy jest używana do pobierania nowych tokenów dostępu — jest najlepszym rozwiązaniem jest jednak, aby bezpiecznie usunąć stary token podczas pobierania nowego. 

Ważne jest do rozróżnienia między klientami poufnymi i klientów publicznych, jak ma to wpływ na jak długo można użyć tokenów odświeżania. Aby uzyskać więcej informacji na temat różnych typów klientów, zobacz [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Okresów istnienia tokenu przy użyciu tokenów odświeżania poufne klienta
Poufne klienci znajdują się aplikacje, które mogą być bezpiecznie przechowywane hasła klienta (klucz tajny). Oni udowodnić, że żądania pochodzą z aplikacji klienckiej zabezpieczonej a nie z złośliwego aktora. Na przykład aplikacja sieci web jest poufne klienta, ponieważ on przechowywać klucz tajny klienta na serwerze sieci web. Nie jest widoczne. Ponieważ te przepływy są bezpieczniejsze, domyślnych okresów istnienia tokenów odświeżania wydane do tych przepływów jest `until-revoked`, nie można zmienić za pomocą zasad i nie zostanie odwołana resetowaniu hasła dobrowolne.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokenów odświeżania okresów istnienia tokenu przy użyciu publicznych klienta

Klienci nie mogą bezpiecznie przechowywać hasła klienta (klucz tajny). Na przykład aplikacji dla systemu iOS/Android nie zaciemniania hasła właściciela zasobu, więc jest uznawany za publicznych klienta. Można ustawić zasady dotyczące zasobów, aby uniemożliwić uzyskanie nową parę tokenu dostępu/odświeżanie tokenów odświeżania z klientów publicznych, które są starsze niż określonym przedziale czasu. (Aby to zrobić, należy użyć właściwości odświeżanie tokenu maksymalny czas nieaktywności (`MaxInactiveTime`).) Możesz również użyć zasad można ustawić okres, po przekroczeniu którego już nie są akceptowane tokenów odświeżania. (W tym celu należy użyć właściwości odświeżanie tokenu maksymalny wiek). Możesz dostosować okres istnienia tokenu odświeżania do kontrolowania, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast jest dyskretnie ponownie uwierzytelniany, korzystając z aplikacji klienckiej publicznego.

### <a name="id-tokens"></a>Tokeny identyfikatorów
Identyfikator tokeny są przekazywane do witryny sieci Web i klientów natywnych. Tokeny Identyfikatora zawierają informacje o profilu użytkownika. Identyfikator tokenu jest powiązany z kombinacją określonego użytkownika i klienta. Identyfikator tokeny są uznawane za prawidłowe aż do ich wygaśnięcia. Zazwyczaj pasuje do aplikacji sieci web przez użytkownika okres istnienia sesji w aplikacji z okresem istnienia tokenu Identyfikacyjnego wydanych dla użytkownika. Możesz dostosować okres istnienia tokenu Identyfikacyjnego do kontrolowania, jak często aplikacja sieci web wygaśnie sesja aplikacji i jak często wymaga użytkownika być ponownie uwierzytelniany przy użyciu usługi Azure AD (dyskretnie lub interaktywnego).

### <a name="single-sign-on-session-tokens"></a>Tokeny sesji rejestracji jednokrotnej
Gdy użytkownik uwierzytelnia się za pomocą usługi Azure AD, sesji rejestracji jednokrotnej (SSO) jest nawiązywane z przeglądarki użytkownika a usługą Azure AD. Token logowania jednokrotnego, w postaci pliku cookie, reprezentuje tej sesji. Token sesji logowania jednokrotnego nie jest powiązany z aplikacją określonego zasobu/klienta. Można odwołać tokenów sesji logowania jednokrotnego, a ich ważności jest zaznaczone, za każdym razem, gdy są one używane.

Usługa Azure AD używa dwóch rodzajów tokenów sesji logowania jednokrotnego: stałe i nietrwałe. Tokeny trwałych sesji są przechowywane jako trwałe pliki cookie przez przeglądarkę. Tokeny nietrwałych sesji są przechowywane jako pliki cookie z sesji. (Pliki cookie dotyczące sesji są niszczone, gdy przeglądarka jest zamknięta.) Zazwyczaj token nietrwałych sesji jest przechowywany. Jednak gdy użytkownik wybierze **nie wylogowuj mnie** znajduje się pole wyboru podczas uwierzytelniania tokenów trwałych sesji.

Sesja nietrwałych tokeny mają okres istnienia 24 godzin. Trwałe tokeny mają okres istnienia 180 dni. W dowolnym momencie tokenu sesji rejestracji Jednokrotnej jest używana w okresie ważności, okres ważności jest rozszerzany innego 24 godzin lub 180 dni, w zależności od typu tokenu. Jeśli token sesji logowania jednokrotnego nie jest używana w okresie ważności, uważa się wygasła i nie jest akceptowane.

Zasady można użyć, aby ustawić czas, po pierwszym tokenu sesji został wystawiony po przekroczeniu którego już nie jest akceptowana tokenu sesji. (W tym celu należy użyć właściwości sesji tokenu maksymalny wiek). Możesz dostosować okres istnienia tokenu sesji w celu kontrolowania, kiedy i jak często użytkownik będzie musiał ponownie wprowadzić poświadczenia, zamiast dyskretnie uwierzytelniane, korzystając z aplikacji sieci web.

### <a name="token-lifetime-policy-properties"></a>Właściwości zasad okres istnienia tokenu
Zasady okres istnienia tokenu jest typem obiektu zasad, który zawiera reguły okres istnienia tokenu. Właściwości zasady umożliwiają kontrolowanie określonego okresów istnienia tokenu. Jeśli żadna zasada jest ustawiona, system wymusza domyślną wartość okresu istnienia.

### <a name="configurable-token-lifetime-properties"></a>Właściwości można skonfigurować okres istnienia tokenu
| Właściwość | Ciąg właściwości zasad | Dotyczy | Domyślne | Minimalne | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Okres istnienia tokenu dostępu |AccessTokenLifetime |Tokeny dostępu, tokeny Identyfikatora, SAML2 tokenów |1 godzina |10 minut |1 dzień |
| Odświeżanie tokenu maksymalny czas nieaktywny |MaxInactiveTime |Tokenów odświeżania |90 dni |10 minut |90 dni |
| Token odświeżania Jednoskładnikowego maksymalny wiek |MaxAgeSingleFactor |Odświeżanie tokenów (dla wszystkich użytkowników) |Until-revoked |10 minut |Until-revoked<sup>1</sup> |
| Maksymalny wiek Token odświeżania usługi Multi-Factor Authentication |MaxAgeMultiFactor |Odświeżanie tokenów (dla wszystkich użytkowników) |Until-revoked |10 minut |Until-revoked<sup>1</sup> |
| Sesja Jednoskładnikowego tokenu maksymalny wiek |MaxAgeSessionSingleFactor<sup>2</sup> |Tokeny sesji (trwałe i nietrwałe) |Until-revoked |10 minut |Until-revoked<sup>1</sup> |
| Maksymalny wiek tokenu sesji usługi Multi-Factor Authentication |MaxAgeSessionMultiFactor<sup>3</sup> |Tokeny sesji (trwałe i nietrwałe) |Until-revoked |10 minut |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dni jest maksymalna długość jawne, które mogą być ustawione dla tych atrybutów.

### <a name="exceptions"></a>Wyjątki
| Właściwość | Dotyczy | Domyślne |
| --- | --- | --- |
| Odświeżanie tokenu maksymalny wiek (wydane dla użytkowników federacyjnych, posiadający odwołanie za mało informacji<sup>1</sup>) |Tokenów odświeżania (wydane dla użytkowników federacyjnych, posiadający odwołanie za mało informacji<sup>1</sup>) |12 godz. |
| Odświeżanie tokenu czas nieaktywności Max (wystawiony dla poufnych klientów) |Odśwież tokeny (wystawione dla poufnych klientów) |90 dni |
| Odświeżanie tokenu maksymalny wiek (wystawiony dla poufnych klientów) |Odśwież tokeny (wystawione dla poufnych klientów) |Until-revoked |

* <sup>1</sup>Sfederowane osób, które mają odwołania niewystarczające informacje obejmują użytkownicy, którzy nie mają atrybutu "LastPasswordChangeTimestamp" zsynchronizowane. Tacy użytkownicy otrzymują tej krótkiej maksymalny wiek, ponieważ usługi AAD nie może sprawdzić, kiedy można odwołać tokenów, które są powiązane z starych poświadczeń (takich jak hasła, które zostało zmienione) i należy zaewidencjonować ponownie więcej często, aby upewnić się, że użytkownika i skojarzone tokeny nadal w dobrej  stały. Aby poprawić to środowisko, Administratorzy dzierżawy musi zapewnić, że trwa synchronizacja się atrybut "LastPasswordChangeTimestamp" (można ją skonfigurować w obiekcie użytkownika przy użyciu programu Powershell lub za pośrednictwem aplikacji AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Ocena zasad i nadawania priorytetów
Można tworzyć i następnie przypisz zasady okres istnienia tokenu do konkretnej aplikacji, organizacji i jednostki usługi. Wiele zasad mogą być stosowane do określonej aplikacji. Zasady okres istnienia tokenu, które będą obowiązywać obowiązują następujące reguły:

* Jeśli zasady jawnie jest przypisany do nazwy głównej usługi, jest wymuszana.
* Jeśli żadna zasada jawnie jest przypisany do nazwy głównej usługi, zostanie wymuszona zasady jawnie przypisane do organizacji nadrzędnej jednostki usługi.
* Jeśli żadne zasady nie zostały wyraźnie przypisane do nazwy głównej usługi lub organizacji, przypisanych do zastosowania zasad zostanie wymuszona.
* Jeśli nie zasady zostały przypisane do jednostki usługi, organizacji lub obiekt aplikacji, wartości domyślne są wymuszane. (Zobacz tabelę w [właściwości można skonfigurować okres istnienia tokenu](#configurable-token-lifetime-properties).)

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji i obiektów nazw głównych usług, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](app-objects-and-service-principals.md).

Ważność tokenu jest obliczane w czasie, token jest używana. Zasady o najwyższym priorytecie w aplikacji, który jest dostępny w życie.

Wszystkie timespans używane w tym miejscu są sformatowane zgodnie z języka C# [TimeSpan](/dotnet/api/system.timespan) obiekt - D.HH:MM:SS.  Więc 80 dni i 30 minut będzie `80.00:30:00`.  Wiodące można było porzucić D, jeśli zero, więc 90 minut będzie `00:90:00`.  

> [!NOTE]
> Poniżej przedstawiono przykładowy scenariusz.
>
> Użytkownik chce, aby uzyskiwać dostęp do dwóch aplikacji sieci web: Aplikacja sieci Web, A i B. aplikacji sieci Web
> 
> Czynniki:
> * Obie aplikacje sieci web znajdują się w tej samej organizacji nadrzędnej.
> * Token 1 zasad okres istnienia sesji tokenu maksymalny wiek osiem godzin jest ustawiony jako domyślny organizacji nadrzędnej.
> * Aplikacja sieci Web, A aplikacja sieci web użycia zwykłych i nie jest połączony z żadnymi zasadami.
> * B aplikacji sieci Web jest używana do wysoce poufnych procesów. Jednostka usługi jest połączony z tokenu okres istnienia zasad 2, który ma sesji tokenu maksymalny wiek 30 minut.
>
> O 12:00 użytkownik uruchamia nową sesję i próbuje uzyskać dostęp, aplikacja sieci Web A. Użytkownik jest przekierowywany do usługi Azure AD i zostanie poproszony o Zaloguj się. Spowoduje to utworzenie pliku cookie, który ma tokenu sesji w przeglądarce. Użytkownik jest przekierowany z powrotem do aplikacji sieci Web A przy użyciu tokenu Identyfikatora, który umożliwia użytkownikowi dostęp do aplikacji.
>
> O 12:15 użytkownik próbuje uzyskać dostęp w sieci Web aplikacji B. Przeglądarka przekierowuje do usługi Azure AD, która wykrywa plików cookie sesji. Nazwa główna usługi sieci Web aplikacji B jest połączona z tokenu 2 zasad okres istnienia, ale jest również częścią organizacji nadrzędnej, wartość domyślna: 1 okres istnienia tokenu w zasad. Token 2 zasad okres istnienia zaczyna obowiązywać, ponieważ zasady połączone z jednostki usługi mają wyższy priorytet niż zasady domyślne dla organizacji. Token sesji było początkowo wydane w ciągu ostatnich 30 minut, dlatego jest uważany za prawidłowy. Użytkownik jest przekierowany z powrotem do B aplikacji sieci Web przy użyciu tokenu Identyfikatora, który przyznaje im dostęp.
>
> O 13:00 użytkownik próbuje uzyskać dostęp aplikacji sieci Web A. Użytkownik jest przekierowywany do usługi Azure AD. A aplikacji sieci Web nie jest połączona z żadnymi zasadami, ale ponieważ jest w organizacji. wartość domyślna: 1 okres istnienia tokenu w zasad, te zasady będą obowiązywać. Wykryto plik cookie sesji, który pierwotnie został wydany w ciągu ostatnich ośmiu godzin. Użytkownik jest dyskretnie przekierowany z powrotem do aplikacji sieci Web A za pomocą nowego tokenu Identyfikatora. Użytkownik nie jest wymagane do uwierzytelnienia.
>
> Zaraz potem użytkownik próbuje uzyskać dostęp w sieci Web aplikacji B. Użytkownik jest przekierowywany do usługi Azure AD. Jak wcześniej, Token okres istnienia zasad 2 staje się skuteczny. Ponieważ token został wystawiony ponad 30 minut temu, użytkownik jest monitowany o ponowne wprowadzenie poświadczeń logowania. Wystawiane są dla tokenu sesji dobór i identyfikator tokenu. Użytkownik może uzyskać dostęp w sieci Web aplikacji B.
>
>

## <a name="configurable-policy-property-details"></a>Szczegóły dotyczące właściwości możliwymi do skonfigurowani zasadami
### <a name="access-token-lifetime"></a>Okres istnienia tokenu dostępu
**Ciąg:** AccessTokenLifetime

**Dotyczy:** Tokeny dostępu, tokeny Identyfikatora

**Podsumowanie:** Ta zasada kontroluje, jak długo dostępu i tokeny Identyfikatora dla tego zasobu są uznawane za prawidłowe. Zmniejszenie właściwość okres istnienia tokenu dostępu zmniejsza ryzyko tokenu dostępu lub identyfikator tokenu używany przez złośliwe Aktor przez dłuższy czas. (Nie można odwołać te tokeny.) Jest to kompromis, to niekorzystny wpływ na wydajność, ponieważ tokeny muszą zostać zastąpione częściej.

### <a name="refresh-token-max-inactive-time"></a>Odświeżanie tokenu maksymalny czas nieaktywny
**Ciąg:** MaxInactiveTime

**Dotyczy:** Tokenów odświeżania

**Podsumowanie:** Ta zasada kontroluje, jak stary token odświeżania może być, zanim klient nie jest już służy do pobierania nową parę tokenu dostępu/odświeżanie, podczas próby dostępu do tego zasobu. Ponieważ zazwyczaj nowego tokena odświeżania jest zwracana, gdy jest używany token odświeżania, ta zasada uniemożliwia dostęp, jeśli klient próbuje uzyskać dostęp do dowolnego zasobu za pomocą bieżącego tokenu odświeżania w określonym przedziale czasu.

Ta zasada wymusza użytkowników, którzy nie byli aktywni na komputerze klienckim, ich ponownego uwierzytelnienia do nowego tokena odświeżania do pobrania.

Właściwość odświeżanie tokenu maksymalny czas nieaktywności, musi być równa mniejszą wartość niż Jednoskładnikowego tokenu maksymalny wiek i właściwości usługi Multi-Factor Authentication odświeżanie tokenu maksymalny wiek.

### <a name="single-factor-refresh-token-max-age"></a>Token odświeżania Jednoskładnikowego maksymalny wiek
**Ciąg:** MaxAgeSingleFactor

**Dotyczy:** Tokenów odświeżania

**Podsumowanie:** To jak długo użytkownik może używać tokenu odświeżania, aby uzyskać nową parę tokenu dostępu/odświeżania po ich ostatniego uwierzytelnieniu pomyślnie za pomocą pojedynczego współczynnika kontroli zasad. Po użytkownik uwierzytelni i otrzymuje nowego tokena odświeżania, użytkownik może użyć przepływu tokenu odświeżania w określonym okresie czasu. (Ta zasada obowiązuje tak długo, jak bieżący token odświeżania nie został odwołany, a nie pozostanie niewykorzystane przez czas dłuższy niż czas nieaktywności.) W tym momencie użytkownik jest zmuszony do ponownego uwierzytelnienia do odbierania nowego tokena odświeżania.

Ograniczenie maksymalnego wieku wymuszającej uwierzytelnianie częściej. Ponieważ uwierzytelniania jednoskładnikowego jest uznawana za mniej bezpieczna niż uwierzytelnianie wieloskładnikowe, zaleca się ustawić tę właściwość na wartość, która jest równa lub mniejsza niż wartość właściwości usługi Multi-Factor Authentication odświeżanie tokenu maksymalny wiek.

### <a name="multi-factor-refresh-token-max-age"></a>Maksymalny wiek Token odświeżania usługi Multi-Factor Authentication
**Ciąg:** MaxAgeMultiFactor

**Dotyczy:** Tokenów odświeżania

**Podsumowanie:** To jak długo użytkownik może używać tokenu odświeżania, aby uzyskać nową parę tokenu dostępu/Odśwież po ich ostatniego uwierzytelnieniu pomyślnie za pomocą wielu czynników kontroli zasad. Po użytkownik uwierzytelni i otrzymuje nowego tokena odświeżania, użytkownik może użyć przepływu tokenu odświeżania w określonym okresie czasu. (Ta zasada obowiązuje tak długo, jak bieżący token odświeżania nie został odwołany i nie jest nieużywany przez czas dłuższy niż czas nieaktywności.) W tym momencie użytkownicy muszą ponownie uwierzytelniać do odbierania nowego tokena odświeżania.

Ograniczenie maksymalnego wieku wymuszającej uwierzytelnianie częściej. Ponieważ uwierzytelniania jednoskładnikowego jest uznawana za mniej bezpieczna niż uwierzytelnianie wieloskładnikowe, zaleca się ustawić tę właściwość na wartość, która jest równa lub większa niż wartość właściwości Jednoskładnikowego odświeżanie tokenu maksymalny wiek.

### <a name="single-factor-session-token-max-age"></a>Sesja Jednoskładnikowego tokenu maksymalny wiek
**Ciąg:** MaxAgeSessionSingleFactor

**Dotyczy:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** To jak długo użytkownik może używać tokenu sesji na otrzymanie nowy identyfikator i tokenu sesji nastąpiło ostatnie uwierzytelnienie pomyślnie za pomocą pojedynczego współczynnika kontroli zasad. Po użytkownik uwierzytelni i odbiera token nowej sesji, użytkownik może użyć przepływu tokenu sesji w określonym przedziale czasu. (Ta zasada obowiązuje tak długo, jak token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia do odbierania token nowej sesji.

Ograniczenie maksymalnego wieku wymuszającej uwierzytelnianie częściej. Ponieważ uwierzytelniania jednoskładnikowego jest uznawana za mniej bezpieczna niż uwierzytelnianie wieloskładnikowe, zaleca się ustawić tę właściwość na wartość, która jest równa lub mniejsza niż wartość właściwości usługi Multi-Factor Authentication sesji tokenu maksymalny wiek.

### <a name="multi-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji usługi Multi-Factor Authentication
**Ciąg:** MaxAgeSessionMultiFactor

**Dotyczy:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** Tego kontroli zasad, jak długo użytkownik może używać tokenu sesji w celu uzyskania nowego Identyfikatora i sesji tokenu po ostatniej one uwierzytelniane pomyślnie za pomocą wielu czynników. Po użytkownik uwierzytelni i odbiera token nowej sesji, użytkownik może użyć przepływu tokenu sesji w określonym przedziale czasu. (Ta zasada obowiązuje tak długo, jak token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia do odbierania token nowej sesji.

Ograniczenie maksymalnego wieku wymuszającej uwierzytelnianie częściej. Ponieważ uwierzytelniania jednoskładnikowego jest uznawana za mniej bezpieczna niż uwierzytelnianie wieloskładnikowe, zaleca się ustawić tę właściwość na wartość, która jest równa lub większa niż wartość właściwości Jednoskładnikowego sesji tokenu maksymalny wiek.

## <a name="example-token-lifetime-policies"></a>Przykładowe zasady okres istnienia tokenu
Wiele scenariuszy są możliwe w usłudze Azure AD, gdy można tworzyć i zarządzać okresów istnienia tokenu dla aplikacji, nazwy główne usług i Twojej organizacji. W tej sekcji omówimy kilka typowych scenariuszy zasad, które mogą pomóc Ci nakładają nowe reguły:

* Czas życia tokenu
* Token maksymalny czas nieaktywny
* Token maksymalny wiek

W przykładach, można dowiedzieć się jak:

* Zarządzanie zasadami domyślne w organizacji
* Tworzenie zasad dla rejestracji w sieci web
* Tworzenie zasad dla natywnych aplikacji, która wywołuje interfejs API sieci web
* Zarządzanie zaawansowane zasady

### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad aplikacji, nazwy główne usług i Twojej organizacji. Jeśli jesteś nowym użytkownikiem usługi Azure AD, firma Microsoft zaleca zapoznanie się [jak uzyskać dzierżawę usługi Azure AD](quickstart-create-new-tenant.md) przed wykonaniem tych przykładów.  

Aby rozpocząć pracę, wykonaj następujące czynności:

1. Pobierz najnowszy [usługi Azure AD PowerShell Module publicznej wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview).
2. Uruchom `Connect` polecenie, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, Rozpocznij nową sesję.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Aby wyświetlić wszystkie zasady, które zostały utworzone w Twojej organizacji, uruchom następujące polecenie. To polecenie jest uruchamiane po większość operacji w następujących scenariuszach. Uruchomienie polecenia pomaga też pobrać ** ** zasad.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Przykład: Zarządzanie zasadami domyślne w organizacji
W tym przykładzie utworzysz zasadę, która umożliwia logowanie użytkowników rzadziej w całej organizacji. Aby to zrobić, należy utworzyć zasadę okres istnienia tokenu Jednoskładnikowego odświeżanie tokenów, która jest stosowana w całej organizacji. Zasady są stosowane do każdej aplikacji w Twojej organizacji i każdej jednostki usługi, która nie ma jeszcze zestaw zasad.

1. Utwórz zasady okres istnienia tokenu.

    1. Ustaw Token odświeżania Jednoskładnikowego "do momentu-odwołane." Token nie wygasa, dopóki nie został odwołany dostęp. Utwórz następującą definicję zasad:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Aby utworzyć zasady, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane **ObjectId**, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Aktualizacja zasad.

    Można zdecydować, pierwszego zasad ustawionych w tym przykładzie nie jest tak rygorystycznych wymaga Twoja usługa. Aby ustawić swoje Jednoskładnikowego Odśwież Token wygaśnie w ciągu dwóch dni, uruchom następujące polecenie:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Przykład: Tworzenie zasad dla rejestracji w sieci web

W tym przykładzie utworzysz zasadę, która wymaga od użytkowników uwierzytelniania częściej w aplikacji sieci web. Ta zasada ustawia okres istnienia tokenów dostępu/ID i maksymalnego wieku tokenu sesji usługi Multi-Factor Authentication do nazwy głównej usługi aplikacji sieci web.

1. Utwórz zasady okres istnienia tokenu.

    Te zasady, logowanie w sieci web, Ustawia/identyfikator dostępu okres istnienia tokenu i okres ważności tokenu max sesji jednoskładnikowego do dwóch godzin.

    1. Aby utworzyć zasady, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Aby wyświetlić nowe zasady, a aby pobrać zasady wymagane **ObjectId**, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Przypisz zasady do jednostki usługi. Musisz również pobrać **ObjectId** jednostki usługi.

    1. Użyj [polecenia Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) polecenia cmdlet, aby wyświetlić nazwy główne usług wszystkich w organizacji lub ta jednostka jednej usługi.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Jeśli masz nazwę główną usługi, uruchom następujące polecenie:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Przykład: Tworzenie zasad dla natywnych aplikacji, która wywołuje interfejs API sieci web
W tym przykładzie utworzysz zasadę, która wymaga od użytkowników uwierzytelniania się rzadziej. Zasady powoduje nieznaczne również wydłużenie czasu, który użytkownik może być nieaktywne, zanim użytkownik musi ponownie uwierzytelniać. Zasady są stosowane do internetowego interfejsu API. Ta zasada jest stosowana, gdy aplikacji natywnej zażąda internetowy interfejs API jako zasób.

1. Utwórz zasady okres istnienia tokenu.

    1. Aby utworzyć ścisłe zasady dla interfejsu API sieci web, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Przypisz zasady do interfejsu API sieci web. Musisz również pobrać **ObjectId** aplikacji. Użyj [Get AzureADApplication](/powershell/module/azuread/get-azureadapplication) polecenia cmdlet użytkownikom znalezienie Twojej aplikacji **ObjectId**, lub użyj [witryny Azure portal](https://portal.azure.com/).

    Pobierz **ObjectId** swojej aplikacji i przypisz zasady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Przykład: Zarządzanie zaawansowane zasady
W tym przykładzie utworzysz kilka zasad, aby dowiedzieć się, jak działa system priorytetu. Poznasz również sposób zarządzania wiele zasad, które są stosowane do wielu obiektów.

1. Utwórz zasady okres istnienia tokenu.

    1. Aby utworzyć zasady domyślne organizacji, która ustawia okres istnienia tokenu odświeżania Jednoskładnikowego do 30 dni, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Przypisz zasady do nazwy głównej usługi.

    Masz teraz zasady, które mają zastosowanie do całej organizacji. Możesz chcieć zachować te zasady 30-dniowej dla określonej jednostki usługi, ale Zmień domyślne zasady organizacji do górnego limitu "do momentu odwołane."

    1. Aby wyświetlić nazwy główne usług wszystkich w organizacji, należy użyć [polecenia Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) polecenia cmdlet.

    2. Jeśli masz nazwę główną usługi, uruchom następujące polecenie:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Ustaw `IsOrganizationDefault` flagi na wartość false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Utwórz nowe zasady domyślne dla organizacji:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Masz teraz oryginalnych zasad, które są połączone z jednostki usługi, a nowych zasad jest ustawiony jako zasady domyślne organizacji. Należy pamiętać, że zasady zastosowane do jednostki usługi mają priorytet nad organizacji domyślne zasady.

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

### <a name="manage-policies"></a>Zarządzanie zasadami

Można użyć następujących poleceń cmdlet do zarządzania zasadami.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Tworzy nową zasadę.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tablica JSON skonwertowanej do formatu tekstowego, który zawiera wszystkie zasady reguły. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |W przypadku opcji true ustawia zasady jako zasady domyślne w organizacji. W przypadku wartości FAŁSZ nie działa. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zasad. Dla okresów istnienia tokenu należy zawsze używać "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcjonalnie] |Ustawia alternatywnego Identyfikatora zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Pobiera wszystkie zasady usługi Azure AD lub z określonymi zasadami.

```powershell
Get-AzureADPolicy
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcjonalnie] |**Identyfikator obiektu (identyfikator)** zasad ma. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Pobiera wszystkie aplikacje i jednostki usługi, które są połączone z zasadami.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** zasad ma. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje istniejące zasady.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** zasad ma. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcjonalnie] |Tablica JSON skonwertowanej do formatu tekstowego, który zawiera wszystkie zasady reguły. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcjonalnie] |W przypadku opcji true ustawia zasady jako zasady domyślne w organizacji. W przypadku wartości FAŁSZ nie działa. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcjonalnie] |Typ zasad. Dla okresów istnienia tokenu należy zawsze używać "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcjonalnie] |Ustawia alternatywnego Identyfikatora zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Usuwa określonych zasad.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** zasad ma. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zasady aplikacji
W przypadku zasad aplikacji, można użyć następujących poleceń cmdlet.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Łączy określonymi zasadami aplikacji.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identyfikator obiektu** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Pobiera zasady, która jest przypisana do aplikacji.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Usuwa zasady z aplikacją.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identyfikator obiektu** zasad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zasady dotyczące nazw głównych usług
Można użyć następujących poleceń cmdlet dla zasady dotyczące nazw głównych usługi.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Łączy określonymi zasadami dla jednostki usługi.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identyfikator obiektu** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Pobiera wszelkie zasady połączone z określona nazwa główna usługi.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Usuwa zasady z określonym jednostki usługi.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (identyfikator)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identyfikator obiektu** zasad. | `-PolicyId <ObjectId of Policy>` |
