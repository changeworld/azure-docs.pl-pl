---
title: Można skonfigurować tokenu okresy istnienia w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić okresy istnienia tokenów wystawionych przez usługę Azure AD.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: hirsin
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: 086a2fde5905321da7d5689b6f1ee2f5139209ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588866"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Można skonfigurować tokenu okresy istnienia w usłudze Azure Active Directory (publicznej wersji zapoznawczej)
Można określić okres istnienia token wystawiony przez usługę Azure Active Directory (Azure AD). Można ustawić tokenu okresy istnienia dla wszystkich aplikacji w organizacji, dla wielodostępnych aplikacji (wielu organizacji) lub nazwy głównej usługi określonego w organizacji.

> [!IMPORTANT]
> Po przesłuchaniu od klientów w wersji zapoznawczej, planowane jest możemy zastąpić tę funkcję z nową funkcją w usłudze Azure Active Directory dostępu warunkowego.  Po zakończeniu nowa funkcja ta funkcja po pewnym czasie zostaną wycofane po upływie powiadomień.  Jeśli używasz zasad można skonfigurować okres istnienia tokenu, można przygotować Aby przełączyć się do nowa funkcja dostępu warunkowego, gdy jest ona dostępna. 
>
>

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane na poszczególne aplikacje lub wszystkie aplikacje w organizacji. Każdy typ zasad ma unikatową strukturę, z zestawem właściwości, które są stosowane do obiektów, do których jest przypisany.

Zasady można wyznaczyć jako domyślne zasady dla Twojej organizacji. Zasady są stosowane do aplikacji w organizacji, dopóki nie zostanie on przesłonięty przez zasady z wyższym priorytetem. Można również przypisać zasady do określonych aplikacji. Według priorytetu zależy od typu zasad.

> [!NOTE]
> Zasad można skonfigurować okres istnienia tokenu nie jest obsługiwana dla usługi SharePoint Online.  Mimo że masz możliwość tworzenia tych zasad za pomocą programu PowerShell usługi SharePoint Online nie potwierdzi tych zasad. Zapoznaj się [blogu usługi SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) Aby dowiedzieć się więcej o konfigurowaniu limitów czasu bezczynności sesji.
>* Domyślny okres istnienia tokenu dostępu usługi SharePoint Online jest 1 godzina. 
>* Maksymalny czas nieaktywności domyślne usługi SharePoint Online token odświeżania wynosi 90 dni.
>

## <a name="token-types"></a>Typy tokenów

Można ustawić zasady okres istnienia tokenu dla tokenów odświeżania, tokeny dostępu, tokeny sesji i tokeny Identyfikatora.

### <a name="access-tokens"></a>Tokeny dostępu
Klienci używają tokenów dostępu do uzyskania dostępu do chronionego zasobu. Token dostępu może służyć tylko dla określonej kombinacji użytkownika, klienta i zasobów. Tokeny dostępu nie może zostać odwołany i są prawidłowe, aż do ich wygaśnięcia. Aktora złośliwy, który uzyskał token dostępu może być używany dla zakresu jego okres istnienia. Dostosowywanie okres istnienia tokenu dostępu jest zależność między poprawia wydajność systemu oraz zwiększyć ilość czasu, klient zachowuje dostępu po wyłączeniu konta użytkownika. Ulepszony system wydajność jest osiągana, zmniejszając liczbę razy, gdy klient musi uzyskać tokenu dostępu świeże.  Wartość domyślna to 1 godzina — po godzinie, klient musi używać token odświeżania (zazwyczaj dyskretnie) uzyskać nowy token odświeżania i tokenu dostępu. 

### <a name="refresh-tokens"></a>Tokenów odświeżania
Gdy klient uzyskuje token dostępu do uzyskania dostępu do chronionego zasobu, klient również odbiera token odświeżania. Token odświeżania służy do uzyskiwania dostępu do nowych/odświeżania pary tokenu, po wygaśnięciu tokenu dostępu bieżącego. Token odświeżania jest powiązany z kombinacją użytkownika i klienta. Token odświeżania może być [odwołać w dowolnym momencie](develop/active-directory-token-and-claims.md#token-revocation), a ważności tokenu jest sprawdzana za każdym razem, gdy jest używany.  

Jest ważne odróżnić klienci poufne i publicznych, ponieważ wpływa to na jak długo może służyć tokenów odświeżania. Aby uzyskać więcej informacji o różnych typach klientów, zobacz [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token okresy istnienia z tokenów odświeżania poufne klienta
Poufne klienci znajdują się aplikacje, które można bezpiecznie przechowywać hasła klienta (klucz tajny). One udowodnić, że żądania pochodzą z aplikacji zabezpieczonych klienta, a nie z złośliwego aktora. Na przykład aplikacja sieci web jest poufne klienta, ponieważ umożliwia przechowywanie klucza tajnego klienta na serwerze sieci web. Nie jest widoczne. Ponieważ te przepływy są bardziej bezpieczne, jest domyślną okresy istnienia tokenów odświeżania wystawiony dla tych przepływów `until-revoked`, nie można zmienić za pomocą zasad i nie zostanie odwołany na resetowanie haseł dobrowolny.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token okresy istnienia z tokenów odświeżania publicznych klienta

Klienci publiczny nie może bezpiecznie przechowywać hasła klienta (klucz tajny). Na przykład aplikację systemu iOS/Android nie zasłaniają klucz tajny od właściciela zasobu, jest on uznawany za publicznego klienta. Zasady można ustawić na zasoby, aby uniemożliwić uzyskanie nową parę tokenu dostępu/odświeżania tokenów odświeżania z klientów publicznych starsze niż w określonym przedziale czasu. (Aby to zrobić, użyj właściwości odświeżanie tokenu maksymalny czas nieaktywności (`MaxInactiveTime`).) Również służy zasady można ustawić okres, po przekroczeniu którego już nie są akceptowane tokenów odświeżania. (W tym celu należy użyć właściwości odświeżanie tokenu maksymalny wiek). Okres istnienia token odświeżania, aby kontrolować, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast trwa dyskretnie ponownie uwierzytelnić, korzystając z aplikacji publicznych klienta można dostosować.

### <a name="id-tokens"></a>Tokeny Identyfikatora
Identyfikator tokeny są przekazywane do witryn sieci Web i klientach natywnych. Tokeny Identyfikatora zawierają informacje profilu użytkownika. Identyfikator tokenu jest powiązany z kombinacją określonego użytkownika i klienta. Identyfikator tokeny są uznawane za prawidłowe aż do ich wygaśnięcia. Zwykle, aplikacji sieci web odpowiada użytkownik okres istnienia sesji w aplikacji na okres istnienia tokenu identyfikator wydanych dla użytkownika. Można dostosować okres istnienia tokenu identyfikator, aby kontrolować częstotliwość aplikacji sieci web wygaśnie sesja aplikacji i jak często wymaga użytkownikowi można ponownie uwierzytelnić z usługą Azure AD (dyskretnie lub interaktywnego).

### <a name="single-sign-on-session-tokens"></a>Tokeny sesji rejestracji jednokrotnej
Gdy użytkownik jest uwierzytelniany w usłudze Azure AD, sesji rejestracji jednokrotnej (SSO) jest nawiązywane z przeglądarki użytkownika i Azure AD. Token rejestracji Jednokrotnej w postaci pliku cookie, reprezentuje tej sesji. Należy pamiętać, że tokenu sesji logowania jednokrotnego nie jest powiązany z aplikacją określonego zasobu/klienta. Mogą być odwoływane tokeny sesji logowania jednokrotnego, a ich ważność jest sprawdzana za każdym razem, gdy są one używane.

Dwa rodzaje tokeny sesji rejestracji Jednokrotnej używa usługi Azure AD: stałe i nietrwałe. Tokeny trwały sesji są przechowywane jako trwałe pliki cookie przez przeglądarkę. Tokeny nietrwałych sesji są przechowywane jako pliki cookie z sesji. (Pliki cookie z sesji zostaną zniszczone zamknięcie przeglądarki). Zazwyczaj token nietrwałych sesji jest przechowywany. Jednak gdy użytkownik wybierze **wylogowuj mnie** pole wyboru podczas uwierzytelniania tokenów trwały sesji jest przechowywany.

Tokeny nietrwałych sesji ma 24-godzinny okres istnienia. Trwałe tokeny będą miały okres istnienia 180 dni. Zawsze, gdy tokenu sesji rejestracji Jednokrotnej jest używana w okresie ważności okres ważności jest dłuższy innego 24 godzin lub 180 dni, w zależności od typu tokenu. Jeśli tokenu sesji logowania jednokrotnego nie jest używana w okresie ważności, jest on uznawany za wygasła i została już zaakceptowana.

Zasady umożliwia ustawianie czasu, po pierwszym tokenu sesji został wystawiony poza którego już nie jest akceptowany tokenu sesji. (W tym celu należy użyć właściwości maksymalny wiek tokenu sesji). Okres istnienia tokenu sesji, aby kontrolować, kiedy i jak często użytkownik będzie musiał ponownie wprowadzić poświadczenia, zamiast dyskretnie uwierzytelnianego, korzystając z aplikacji sieci web można dostosować.

### <a name="token-lifetime-policy-properties"></a>Okres istnienia tokenu właściwości zasady
Zasady okres istnienia tokenu jest typem obiektu zasad, który zawiera reguły okres istnienia tokenu. Użyj właściwości zasad w celu kontroli określony token okresy istnienia. Jeśli żadne zasady nie jest ustawiona, system wymusza domyślną wartość okresu istnienia.

### <a name="configurable-token-lifetime-properties"></a>Właściwości można skonfigurować okres istnienia tokenu
| Właściwość | Ciąg właściwości zasady | Dotyczy | Domyślne | Minimalne | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Okres istnienia tokenu dostępu |AccessTokenLifetime |Tokeny dostępu, tokeny Identyfikatora, SAML2 tokenów |1 godzina |10 minut |1 dzień |
| Odśwież tokenu maksymalny czas nieaktywny |MaxInactiveTime |Tokenów odświeżania |90 dni |10 minut |90 dni |
| Token odświeżania Jednoskładnikowego maksymalny wiek |MaxAgeSingleFactor |Odśwież tokeny (dla wszystkich użytkowników) |Dopóki odwołany |10 minut |Until-revoked<sup>1</sup> |
| Maksymalny wiek tokenu wieloskładnikowego odświeżania |MaxAgeMultiFactor |Odśwież tokeny (dla wszystkich użytkowników) |Dopóki odwołany |10 minut |Until-revoked<sup>1</sup> |
| Maksymalny wiek tokenu sesji Jednoskładnikowego |MaxAgeSessionSingleFactor<sup>2</sup> |Tokeny sesji (stałe i nietrwałe) |Dopóki odwołany |10 minut |Until-revoked<sup>1</sup> |
| Maksymalny wiek tokenu wieloskładnikowego sesji |MaxAgeSessionMultiFactor<sup>3</sup> |Tokeny sesji (stałe i nietrwałe) |Dopóki odwołany |10 minut |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dni to maksymalna długość jawne, które można ustawić dla tych atrybutów.
* <sup>2</sup>Jeśli **MaxAgeSessionSingleFactor** nie jest ustawiona, ta wartość ma **MaxAgeSingleFactor** wartości. Jeśli parametr nie jest ustawiona, właściwość ma wartość domyślną (do odwołane).
* <sup>3</sup>Jeśli **MaxAgeSessionMultiFactor** nie jest ustawiona, ta wartość ma **MaxAgeMultiFactor** wartości. Jeśli parametr nie jest ustawiona, właściwość ma wartość domyślną (do odwołane).

### <a name="exceptions"></a>Wyjątki
| Właściwość | Dotyczy | Domyślne |
| --- | --- | --- |
| Odśwież tokenu maksymalny wiek (wydane dla użytkowników federacyjnych, którzy mają informacji o odwołaniu niewystarczające<sup>1</sup>) |Tokenów odświeżania (wydane dla użytkowników federacyjnych, którzy mają informacji o odwołaniu niewystarczające<sup>1</sup>) |12 godzin |
| Odśwież tokenu czas nieaktywności Max (wystawiony dla poufnych klientów) |Odśwież tokenów (wystawionych dla klientów poufnych) |90 dni |
| Odśwież tokenu maksymalny wiek (wystawiony dla poufnych klientów) |Odśwież tokenów (wystawionych dla klientów poufnych) |Dopóki odwołany |

* <sup>1</sup>federacyjnym użytkowników, którzy mają informacji o odwołaniu niewystarczające obejmują wszyscy użytkownicy, którzy nie mają atrybutu "LastPasswordChangeTimestamp" zsynchronizowane. Ci użytkownicy są podane to krótkie maksymalny wiek, ponieważ AAD nie może sprawdzić, kiedy odwołać tokeny, które są powiązane z starego poświadczeń (takich jak hasła, które zostały zmienione) i musi sprawdzić w kilku często, aby upewnić się, że użytkownik i tokeny skojarzone są nadal w dobrym stanie. Aby poprawić działanie tej funkcji, Administratorzy dzierżawy musi zapewnić, że są one synchronizowanie atrybutu "LastPasswordChangeTimestamp" (można go ustawić na obiekt użytkownika przy użyciu programu Powershell lub za pośrednictwem aplikacja AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Ocena zasad i priorytety
Można utworzyć, a następnie przypisać zasady okres istnienia tokenu, z określoną aplikacją, organizacji i nazwy główne usług. Wiele zasad mogą być stosowane do określonej aplikacji. Zasady okres istnienia tokenu, które obowiązuje obowiązują następujące reguły:

* Jeśli zasady jawnie jest przypisany do nazwy głównej usługi, jest wymuszana.
* Jeśli do nazwy głównej usługi jawnie nie przypisano żadnych zasad, zasady jawnie przypisane do organizacji nadrzędnej nazwy głównej usługi jest wymuszana.
* Jeśli żadne zasady nie zostały wyraźnie przypisane do nazwy głównej usługi lub organizacji, przypisane do aplikacji zasady są wymuszane.
* Jeśli żadne zasady nie zostanie przypisana do nazwy głównej usługi, w organizacji lub w obiekcie aplikacji, wartości domyślne są wymuszane. (Zobacz tabelę w [właściwości można skonfigurować okres istnienia tokenu](#configurable-token-lifetime-properties).)

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji i głównej usługi, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](active-directory-application-objects.md).

Ważność tokenu jest oceniane w czasie, który jest używany. Zasady o najwyższym priorytecie w aplikacji, która jest uzyskiwany obowiązuje.

Wszystkie timespans używane w tym miejscu są sformatowane zgodnie z języka C# [TimeSpan](https://msdn.microsoft.com/library/system.timespan) obiekt - D.HH:MM:SS.  Dlatego będzie 80 dni i 30 minut `80.00:30:00`.  Wiodące można było porzucić D, jeśli zero, więc 90 minut będzie `00:90:00`.  

> [!NOTE]
> Oto przykładowy scenariusz.
>
> Użytkownik chce, aby uzyskiwać dostęp do dwóch aplikacji sieci web: aplikacja sieci Web A i B. aplikacji sieci Web
> 
> Czynniki:
> * Zarówno aplikacji sieci web znajdują się w tej samej organizacji nadrzędnej.
> * Tokenu okresu istnienia zasad 1 z sesji tokenu maksymalny wiek osiem godzin jest ustawiony jako domyślny organizacji nadrzędnej.
> * Aplikacja sieci Web A to aplikacja sieci web użycia regularne i nie jest połączony z zasadami.
> * B aplikacji sieci Web jest używana do bardzo poufnych procesów. Nazwy głównej usługi jest połączony z tokenu okresu istnienia zasad 2, który ma tokenu sesji maksymalny wiek 30 minut.
>
> Godzinie 12:00 użytkownik uruchamia nową sesję i próbuje uzyskać dostęp A. aplikacji sieci Web Użytkownik zostanie przekierowany do usługi Azure AD i jest prośba o zalogowanie. Spowoduje to utworzenie pliku cookie, który ma tokenu sesji w przeglądarce. Użytkownik jest przekierowany z powrotem do aplikacji sieci Web A tokenem identyfikator, który umożliwia użytkownikowi dostęp do aplikacji.
>
> Na 12:15:00 użytkownik próbuje uzyskać dostęp B. aplikacji sieci Web Przeglądarka przekierowuje do usługi Azure AD, która wykrywa plików cookie sesji. Nazwy głównej usługi B aplikacji sieci Web jest połączony z tokenu okresu istnienia zasad 2, ale jest również częścią organizacji nadrzędnej domyślnie tokenu okresu istnienia zasad 1. Tokenu okresu istnienia zasad 2 zostanie zastosowana, ponieważ zasady połączone nazwy główne usług ma wyższy priorytet niż zasady domyślne organizacji. Tokenu sesji było początkowo wydane w ciągu ostatnich 30 minut, dlatego jest uznawany za ważny. Użytkownik jest przekierowany z powrotem do B aplikacji sieci Web z tokenem identyfikator, który przyznaje im dostęp.
>
> Godzinie 1:00 użytkownik próbuje uzyskać dostępu do aplikacji sieci Web A. Użytkownik jest przekierowywany do usługi Azure AD. A aplikacja sieci Web nie jest połączony z zasadami, ale ponieważ jest w organizacji z domyślnego tokenu okresu istnienia zasad 1, ta zasada zostanie uwzględniona. Wykryto plik cookie sesji, które było początkowo wydane w ciągu ostatnich ośmiu godzin. Użytkownik jest dyskretnie przekierowany z powrotem do aplikacji sieci Web A z tokenem nowy identyfikator. Użytkownik nie jest wymagane do uwierzytelniania.
>
> Natychmiast potem użytkownik próbuje uzyskać dostęp B. aplikacji sieci Web Użytkownik jest przekierowywany do usługi Azure AD. Jak przedtem tokenu okresu istnienia zasad 2 obowiązuje. Ponieważ token został wystawiony ponad 30 minut temu, użytkownik jest monitowany o ponowne wprowadzenie poświadczeń logowania. Tokenu sesji całkowicie nowy i identyfikator token są wystawiane. Użytkownik może uzyskać dostęp B. aplikacji sieci Web
>
>

## <a name="configurable-policy-property-details"></a>Zasady można skonfigurować właściwości szczegóły
### <a name="access-token-lifetime"></a>Okres istnienia tokenu dostępu
**Ciąg:** AccessTokenLifetime

**Wpływ:** tokenów dostępu, tokeny Identyfikatora

**Podsumowanie:** ta zasada kontroluje, jak długo dostępu i tokeny Identyfikatora dla tego zasobu są uznawane za prawidłowe. Zmniejszenie właściwości okres istnienia tokenu dostępu zmniejsza ryzyko tokenu dostępu lub identyfikator token używany przez złośliwe aktora przez dłuższy czas. (Te tokenów nie może zostać odwołany.) Równowagę jest, czy jest niekorzystny wpływ na wydajność, ponieważ tokeny muszą zostać zastąpione częściej.

### <a name="refresh-token-max-inactive-time"></a>Odśwież tokenu maksymalny czas nieaktywny
**Ciąg:** MaxInactiveTime

**Wpływ:** tokenów odświeżania

**Podsumowanie:** ta zasada kontroluje, jak stary token odświeżania może być, zanim klient może używać go nie można pobrać nowej pary tokenu dostępu/odświeżania podczas próby dostępu do tego zasobu. Ponieważ zazwyczaj nowy token odświeżania jest zwracany, gdy jest używany token odświeżania, ta zasada uniemożliwia dostęp, jeśli klient próbuje uzyskać dostępu do wszystkich zasobów przy użyciu bieżącego tokenu odświeżania w określonym przedziale czasu.

Ta zasada powoduje użytkowników, którzy nie są aktywne w wyjątkach klientowi ponownego uwierzytelnienia do pobrania nowego tokenu odświeżania.

Mniejszą wartość niż maksymalny wiek Jednoskładnikowego Token i właściwości wieloskładnikowego odświeżanie tokenu maksymalny wiek musi mieć ustawioną właściwość odświeżanie tokenu maksymalny czas nieaktywności.

### <a name="single-factor-refresh-token-max-age"></a>Token odświeżania Jednoskładnikowego maksymalny wiek
**Ciąg:** MaxAgeSingleFactor

**Wpływ:** tokenów odświeżania

**Podsumowanie:** to jak długo użytkownik może używać token odświeżania, aby uzyskać nową parę tokenu dostępu/odświeżania po ich ostatniego uwierzytelnieniu pomyślnie za pomocą tylko jednego współczynnik określa zasady. Po użytkownik jest uwierzytelniany i odbiera nowy token odświeżania, użytkownik może użyć przepływu tokenu odświeżania w określonym okresie czasu. (Dotyczy to tak długo, jak bieżący token odświeżania nie został odwołany, a nie pozostanie nieużywane przez czas dłuższy niż czas nieaktywności.) W tym momencie użytkownik będzie zmuszony do ponownego uwierzytelnienia do odbierania nowego tokenu odświeżania.

Zmniejszenie maksymalny wiek wymuszającej uwierzytelnianie częściej. Ponieważ przyjęto, że uwierzytelniania jednoskładnikowego jest mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, firma Microsoft zaleca, ustaw tą właściwość na wartość, która jest równa lub mniejsza niż wartość właściwości wieloskładnikowego odświeżanie tokenu maksymalny wiek.

### <a name="multi-factor-refresh-token-max-age"></a>Maksymalny wiek tokenu wieloskładnikowego odświeżania
**Ciąg:** MaxAgeMultiFactor

**Wpływ:** tokenów odświeżania

**Podsumowanie:** tego ustawień zasad, jak długo użytkownik może używać token odświeżania, aby uzyskać nową parę tokenu dostępu/odświeżania po ich ostatniego uwierzytelnieniu pomyślnie za pomocą wiele czynników. Po użytkownik jest uwierzytelniany i odbiera nowy token odświeżania, użytkownik może użyć przepływu tokenu odświeżania w określonym okresie czasu. (Dotyczy to tak długo, jak bieżący token odświeżania nie został odwołany i nie jest nieużywany przez czas dłuższy niż czas nieaktywności.) W tym momencie użytkownicy muszą ponownie uwierzytelniać do odbierania nowego tokenu odświeżania.

Zmniejszenie maksymalny wiek wymuszającej uwierzytelnianie częściej. Ponieważ przyjęto, że uwierzytelniania jednoskładnikowego jest mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, firma Microsoft zaleca, ustaw tą właściwość na wartość, która jest równa lub większa niż wartość właściwości Jednoskładnikowego odświeżanie tokenu maksymalny wiek.

### <a name="single-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji Jednoskładnikowego
**Ciąg:** MaxAgeSessionSingleFactor

**Wpływ:** tokeny sesji (stałe i nietrwałe)

**Podsumowanie:** tego ustawień zasad, jak długo użytkownik może używać tokenu sesji, aby uzyskać nowy identyfikator i tokenu sesji po ich ostatniego uwierzytelnieniu pomyślnie za pomocą tylko jednego współczynnik. Po użytkownik jest uwierzytelniany i otrzymuje token nowej sesji, użytkownik może użyć przepływu tokenu sesji w określonym okresie czasu. (Dotyczy to tak długo, jak tokenu bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik będzie zmuszony do ponownego uwierzytelnienia otrzymujących token nowej sesji.

Zmniejszenie maksymalny wiek wymuszającej uwierzytelnianie częściej. Ponieważ przyjęto, że uwierzytelniania jednoskładnikowego jest mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, firma Microsoft zaleca, ustaw tą właściwość na wartość, która jest równa lub mniejsza niż wartość właściwości wieloskładnikowego sesji tokenu maksymalny wiek.

### <a name="multi-factor-session-token-max-age"></a>Maksymalny wiek tokenu wieloskładnikowego sesji
**Ciąg:** MaxAgeSessionMultiFactor

**Wpływ:** tokeny sesji (stałe i nietrwałe)

**Podsumowanie:** to zasady Określa jak długo użytkownik może używać tokenu sesji, aby uzyskać nowy identyfikator i sesji tokenu po ostatnim pomyślnie uwierzytelnione przy użyciu wiele czynników. Po użytkownik jest uwierzytelniany i otrzymuje token nowej sesji, użytkownik może użyć przepływu tokenu sesji w określonym okresie czasu. (Dotyczy to tak długo, jak tokenu bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik będzie zmuszony do ponownego uwierzytelnienia otrzymujących token nowej sesji.

Zmniejszenie maksymalny wiek wymuszającej uwierzytelnianie częściej. Ponieważ przyjęto, że uwierzytelniania jednoskładnikowego jest mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, firma Microsoft zaleca, ustaw tą właściwość na wartość, która jest równa lub większa niż wartość właściwości Jednoskładnikowego sesji tokenu maksymalny wiek.

## <a name="example-token-lifetime-policies"></a>Przykładowe zasady okres istnienia tokenu
Wiele scenariuszy są możliwe w usłudze Azure AD, gdy można tworzyć i zarządzać istnienia tokenu dla aplikacji, nazwy główne usług i Twojej organizacji. W tej sekcji możemy przeprowadzenie kilka typowych scenariuszy zasad, które mogą pomóc Ci skonfigurować nowe zasady:

* Okres istnienia tokenu
* Token maksymalny czas nieaktywny
* Maksymalny wiek token

W przykładach, można dowiedzieć się jak:

* Zarządzanie organizacji domyślne zasady
* Tworzenie zasad dla rejestracji w sieci web
* Tworzenie zasad dla natywnych aplikacji, która wywołuje interfejs API sieci web
* Zarządzanie zasadami zaawansowane

### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasady dla aplikacji, nazwy główne usług i Twojej organizacji. Jeśli jesteś nowym użytkownikiem usługi Azure AD, firma Microsoft zaleca zapoznanie się [jak uzyskać dzierżawę usługi Azure AD](active-directory-howto-tenant.md) przed przystąpieniem do tych przykładów.  

Aby rozpocząć pracę, wykonaj następujące czynności:

1. Pobierz najnowszą [Azure AD PowerShell modułu publicznej wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview).
2. Uruchom `Connect` polecenie, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, należy uruchomić nową sesję.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom następujące polecenie. Uruchom to polecenie po większości operacji w następujących scenariuszach. Uruchamiając polecenie pozwala Ci ** ** zasad.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Przykład: Zarządzanie organizacji domyślne zasady
W tym przykładzie utworzysz zasady, które pozwala użytkownikom zalogować się rzadziej w całej organizacji. Aby to zrobić, należy utworzyć zasadę okres istnienia tokenu Jednoskładnikowego Odśwież tokenów, która jest stosowana w całej organizacji. Zasady są stosowane do każdej aplikacji w organizacji, a do każdej nazwy głównej usługi, która nie ma jeszcze zestaw zasad.

1. Utwórz zasadę okres ważności tokenu.

    1.  Zestaw Token odświeżania Jednoskładnikowego do "do odwołanych." Token nie wygasa, dopóki dostęp został odwołany. Utwórz definicję zasad:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Aby utworzyć zasady, uruchom następujące polecenie:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Aby zobaczyć nowe zasady, a także aby pobrać zasady wymagane **ObjectId**, uruchom następujące polecenie:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Aktualizacja zasad.

    Możesz pierwszy zasad ustawionych w tym przykładzie nie jest tak rygorystycznych, jak usługa wymaga. Aby ustawić Twojej Jednoskładnikowego odświeżanie tokenu wygaśnie w ciągu dwóch dni, uruchom następujące polecenie:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Przykład: Tworzenie zasad dla rejestracji w sieci web

W tym przykładzie należy utworzyć zasadę, która wymaga od użytkowników często uwierzytelnienia w aplikacji sieci web. Ta zasada ustawia okres istnienia tokenów dostępu/identyfikator i maksymalny wiek tokenu wieloskładnikowego sesji do nazwy głównej usługi aplikacji sieci web.

1. Utwórz zasadę okres ważności tokenu.

    Te zasady dla sieci web logowania, Ustawia/identyfikator dostępu okres istnienia tokenu i wiek tokenu sesji jednoskładnikowego max do dwóch godzin.

    1.  Aby utworzyć zasady, uruchom następujące polecenie:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Aby wyświetlić nowe zasady i pobrania zasad **ObjectId**, uruchom następujące polecenie:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Przypisz zasady do Twojej nazwy głównej usługi. Należy też uzyskać **ObjectId** z Twojej nazwy głównej usługi. 

    1.  Aby wyświetlić nazwy główne usług wszystkich organizacji, można zbadać albo [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) lub [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ponadto można to sprawdzić w [Explorer Azure AD Graph](https://graphexplorer.azurewebsites.net/)i [Eksplorator systemu Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) przy użyciu konta usługi Azure AD.

    2.  Jeśli masz **ObjectId** z Twojej nazwy głównej usługi, uruchom następujące polecenie:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Przykład: Tworzenie zasad dla natywnych aplikacji, która wywołuje interfejs API sieci web
W tym przykładzie należy utworzyć zasadę, która wymaga od użytkowników uwierzytelniania rzadziej. Zasady powoduje nieznaczne również wydłużenie ilość czasu, który użytkownik może być nieaktywne, zanim użytkownik musi ponownie uwierzytelniać. Zasady są stosowane do interfejsu API sieci web. W przypadku aplikacji natywnej żądań interfejsu API jako zasób sieci web, ta zasada jest stosowana.

1. Utwórz zasadę okres ważności tokenu.

    1.  Aby utworzyć zasadę strict dla interfejsu API sieci web, uruchom następujące polecenie:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Aby wyświetlić nowe zasady i pobrania zasad **ObjectId**, uruchom następujące polecenie:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Przypisz zasady do interfejsu API sieci web. Należy też uzyskać **ObjectId** aplikacji. Najlepszym sposobem postrzegać twoją aplikację **ObjectId** jest użycie [portalu Azure](https://portal.azure.com/).

   Jeśli masz **ObjectId** aplikacji, uruchom następujące polecenie:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Przykład: Zarządzanie zaawansowane zasad
W tym przykładzie utworzysz kilka zasad, aby dowiedzieć się, jak działa system priorytet. Można również można Dowiedz się, jak zarządzać wiele zasad, które są stosowane do wielu obiektów.

1. Utwórz zasadę okres ważności tokenu.

    1.  Aby utworzyć domyślne zasady organizacji, która ustawia okres istnienia tokenu odświeżania Jednoskładnikowego do 30 dni, uruchom następujące polecenie:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Aby zobaczyć nowe zasady, a także aby pobrać zasady wymagane **ObjectId**, uruchom następujące polecenie:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Zasady można przypisać nazwy głównej usługi.

    Masz teraz zasady, która ma zastosowanie do całej organizacji. Należy zachować te zasady 30-dniowej dla podmiotu określonej usługi, ale zmienić domyślne zasady organizacji do górnej granicy "do odwołanych."

    1.  Aby wyświetlić nazwy główne usług wszystkich organizacji, można zbadać albo [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) lub [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ponadto można to sprawdzić w [Explorer Azure AD Graph](https://graphexplorer.cloudapp.net/)i [Eksplorator systemu Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) przy użyciu konta usługi Azure AD.

    2.  Jeśli masz **ObjectId** z Twojej nazwy głównej usługi, uruchom następujące polecenie:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Ustaw `IsOrganizationDefault` flagi na wartość false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Utwórz nowe zasady domyślne organizacji:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Masz teraz oryginalnymi zasadami, które są połączone z Twojej nazwy głównej usługi, a nowe zasady zostały ustawione jako domyślne zasady organizacji. Należy pamiętać, że zasady są stosowane do nazwy główne usług mają priorytet nad organizacji domyślne zasady.

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

### <a name="manage-policies"></a>Zarządzanie zasadami

Można użyć następujących poleceń cmdlet do zarządzania zasadami.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Tworzy nowe zasady.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tablica stringified JSON, który zawiera wszystkie zasady reguły. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Ciąg nazwę zasady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Jeśli PRAWDA, ustawia zasad jako zasady domyślne Twojej organizacji. W przypadku wartości FAŁSZ nie działa. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zasad. Okresy istnienia tokenu zawsze używaj "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcjonalnie] |Ustawia alternatywny identyfikator zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Pobiera wszystkie zasady usługi Azure AD lub określonych zasad.

```PowerShell
Get-AzureADPolicy
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcjonalnie] |**Identyfikator obiektu (Id)** ma zasad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Pobiera wszystkie aplikacje i nazwy główne usług, które są połączone z zasadami.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** ma zasad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje istniejące zasady.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** ma zasad. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Ciąg nazwę zasady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcjonalnie] |Tablica stringified JSON, który zawiera wszystkie zasady reguły. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcjonalnie] |Jeśli PRAWDA, ustawia zasad jako zasady domyślne Twojej organizacji. W przypadku wartości FAŁSZ nie działa. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcjonalnie] |Typ zasad. Okresy istnienia tokenu zawsze używaj "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcjonalnie] |Ustawia alternatywny identyfikator zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Usuwa określonych zasad.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** ma zasad. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zasady aplikacji
Zasady aplikacji, można użyć następujących poleceń cmdlet.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Łącza do aplikacji określonej zasady.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identyfikator obiektu** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Pobiera zasady, który jest przypisany do aplikacji.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Usuwa zasady z aplikacją.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identyfikator obiektu** zasad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zasady dotyczące nazw głównych usług
Można użyć następujących poleceń cmdlet dla podmiotu zabezpieczeń zasad usługi.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Łączy określonych zasad nazwy głównej usługi.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identyfikator obiektu** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Pobiera wszelkie zasady połączone z główną określonej usługi.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Usuwa zasady z określonej nazwy głównej usługi.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Identyfikator obiektu (Id)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identyfikator obiektu** zasad. | `-PolicyId <ObjectId of Policy>` |
