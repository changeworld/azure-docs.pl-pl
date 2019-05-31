---
title: Token odświeżania głównej (PRT) i Azure AD — usługa Azure Active Directory
description: Co to jest rola i jak możemy zarządzać głównej odświeżanie tokenu (PRT) w usłudze Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ecf6d04056a91f1f9dd62a5238f60177d2bf59
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420591"
---
# <a name="what-is-a-primary-refresh-token"></a>Co to jest podstawowym tokenu odświeżania?

Podstawowy odświeżanie tokenu (PRT) jest kluczowe artefakt uwierzytelniania usługi Azure AD w systemie Windows 10, iOS i urządzenia z systemem Android. Jest to Web tokenu JSON (JWT) specjalnie wystawiony dla Microsoft pierwszej strony tokenu brokerów włączyć logowanie jednokrotne (SSO) w aplikacjach, używane na tych urządzeniach. W tym artykule udostępnimy szczegóły jak chronione na urządzeniach z systemem Windows 10, używane i wydawane PRT.

W tym artykule założono, już rozumiesz stany różnych urządzeń dostępnych w usłudze Azure AD i jak logowania jednokrotnego działa w systemie Windows 10. Aby uzyskać więcej informacji dotyczących urządzeń w usłudze Azure AD, zobacz artykuł [co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Kluczową terminologię i składniki

Następujące składniki Windows odgrywają kluczową rolę w żądania i za pomocą PRT:

* **Dostawca uwierzytelniania w chmurze** (CloudAP): CloudAP jest dostawca nowoczesnego uwierzytelniania Windows logowania, które sprawdza użytkowników logujących się na urządzeniu z systemem Windows 10. CloudAP udostępnia strukturę wtyczki tej tożsamości, które dostawcy ją tworzyć przy użyciu umożliwiające uwierzytelnianie Windows przy użyciu poświadczeń tego dostawcy tożsamości.
* **Menedżer kont sieci Web** (WAM): WAM jest brokera tokenu domyślny na urządzeniach z systemem Windows 10. WAM także strukturę wtyczki tej tożsamości dostawców rozwijać i włączyć logowanie Jednokrotne do aplikacji, ich opierając się na tym dostawcy tożsamości.
* **Wtyczka usługi Azure AD CloudAP**: Określone wtyczki usługi Azure AD oparty na CloudAP framework, które sprawdza poświadczenia użytkownika w usłudze Azure AD podczas logowania do Windows.
* **Wtyczka usługi Azure AD WAM**: Określone wtyczki usługi Azure AD oparty na WAM framework, który umożliwia logowanie Jednokrotne do aplikacji, które opierają się na usłudze Azure AD do uwierzytelniania.
* **Dsreg**: Usługa Azure AD określonego składnika w systemie Windows 10, który obsługuje proces rejestracji urządzeń dla wszystkich urządzeń.
* **Moduł TPM** (TPM): Moduł TPM jest składnikiem sprzętowym, wbudowane w urządzeniu, która zapewnia funkcje zabezpieczeń oparte na sprzęcie wpisami tajnymi użytkowników i urządzeń. Więcej szczegółów można znaleźć w artykule [zaufanych omówienie technologii modułu platformy](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co zawiera PRT

PRT zawiera oświadczenia, które zwykle są zawarte w dowolnej token odświeżania usługi Azure AD. Ponadto istnieją pewne oświadczenia specyficznych dla urządzenia objęte PRT. Są one w następujący sposób:

* **Identyfikator urządzenia**: PRT wystawionego dla użytkownika na konkretnym urządzeniu. Oświadczenie Identyfikatora urządzenia `deviceID` określa urządzenia PRT została wystawiona dla użytkownika. To oświadczenie wystawiono później tokeny uzyskanymi za pośrednictwem PRT. Oświadczenie Identyfikatora urządzenia jest używany do określenia autoryzacji dostępu warunkowego na podstawie stanu urządzenia lub zgodności.
* **Klucz sesji**: Klucz sesji jest zaszyfrowany klucz symetryczny generowane przez usługę uwierzytelniania usługi Azure AD, wydane jako część PRT. Klucz sesji działa jako dowodu posiadania stosowania PRT umożliwiające uzyskanie tokenów dla innych aplikacji.

### <a name="can-i-see-whats-in-a-prt"></a>Można wyświetlić co znajduje się w PRT?

PRT to nieprzezroczysty obiekt blob wysyłane z usługi Azure AD, w których zawartość nie wiadomo, że wszystkie składniki klienta. Nie można zobaczyć, co znajduje się wewnątrz PRT.

## <a name="how-is-a-prt-issued"></a>Jak są wydawane PRT

Rejestracja urządzeń jest wymaganiem wstępnym dla urządzenia na podstawie uwierzytelniania w usłudze Azure AD. PRT jest wydawane użytkownikom tylko na zarejestrowanych urządzeniach. Aby uzyskać więcej szczegółowych informacji o rejestracji urządzenia, zobacz artykuł [Windows Hello dla firm i usługi rejestracji urządzeń](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Podczas rejestracji urządzenia składnika dsreg generuje dwa zestawy par kluczy kryptograficznych:

* Klucz urządzenia (dkpub/dkpriv)
* Klucz transportu (tkpub/tkpriv)

Klucze prywatne są powiązane z urządzenia modułu TPM, jeśli urządzenie ma prawidłowe i czy działa modułu TPM, gdy klucze publiczne są wysyłane do usługi Azure AD podczas procesu rejestracji urządzenia. Klucze te służą do sprawdzania poprawności stanu urządzenia podczas żądania PRT.

PRT jest wystawione podczas uwierzytelniania użytkownika na urządzeniu z systemem Windows 10 w przypadku dwóch scenariuszy:

* **Dołączono do usługi Azure AD** lub **przyłączone do hybrydowej usługi Azure AD**: PRT wystawiany jest podczas logowania Windows, gdy użytkownik loguje się przy użyciu swoich poświadczeń w organizacji. PRT jest wystawiony wszystkie poświadczenia systemu Windows 10 obsługiwanych, na przykład, hasło i Windows Hello dla firm. W tym scenariuszu wtyczki Azure AD CloudAP jest podstawowym serwerem autorytatywnym dla PRT.
* **Usługa Azure AD, rejestrowania urządzenia**: Gdy użytkownik doda konto służbowe pomocniczego na urządzenie z systemem Windows 10, wystawiany jest PRT. Użytkownicy mogą dodawać konta do systemu Windows 10 na dwa różne sposoby-  
   * Dodawanie konta za pośrednictwem **Użyj tego konta wszędzie na tym urządzeniu** monitu po zalogowaniu się do aplikacji (na przykład Outlook)
   * Dodawanie konta z **ustawienia** > **kont** > **dostęp do zasobów służbowych** > **Connect**

W tych scenariuszach wtyczki Azure AD WAM jest podstawowym urzędu dla PRT, ponieważ Windows logowania nie pojawia się przy użyciu tego konta usługi Azure AD.

## <a name="what-is-the-lifetime-of-a-prt"></a>Co to jest okres istnienia PRT?

Po wydaniu PRT jest ważny przez 14 dni i stale zostanie odnowiony, tak długo, jak użytkownik aktywnie korzysta z urządzenia.  

## <a name="how-is-a-prt-used"></a>Jak są używane PRT?

PRT jest używany przez dwa główne składniki Windows:

* **Wtyczka usługi Azure AD CloudAP**: Podczas Windows logowania wtyczka usługi Azure AD CloudAP żądań PRT z usługi Azure AD przy użyciu poświadczeń dostarczonych przez użytkownika. Buforuje PRT umożliwiające buforowane logowania po użytkownik ma dostęp do połączenia internetowego.
* **Wtyczka usługi Azure AD WAM**: W przypadku próby uzyskania dostępu do aplikacji, wtyczka usługi Azure AD WAM używa PRT do włączenia funkcji logowania jednokrotnego w systemie Windows 10. Wtyczka usługi Azure AD WAM użyto PRT do żądania tokenów odświeżania i dostępem dla aplikacji, które zależą od WAM dla żądania tokenu. Umożliwia ona także logowanie Jednokrotne w przeglądarkach przez iniekcję PRT do żądania przeglądarki. Przeglądarka logowania jednokrotnego w systemie Windows 10 jest obsługiwana w programie Microsoft Edge (natywną) i Chrome (za pośrednictwem rozszerzenia systemu Windows 10 kont lub witryny Office Online).

## <a name="how-is-a-prt-renewed"></a>Jak jest odnawiany PRT

PRT zostanie odnowiony w dwóch różnych metod:

* **Wtyczka usługi Azure AD CloudAP co 4 godziny**: Wtyczka CloudAP odnawia PRT co 4 godziny podczas logowania do Windows. Jeśli użytkownik nie ma połączenia internetowego, w tym samym czasie, wtyczka CloudAP odnowi PRT po urządzenie jest połączone z Internetem.
* **Wtyczka usługi Azure AD WAM podczas żądania tokenu aplikacji**: Wtyczki WAM umożliwia logowanie Jednokrotne na urządzeniach z systemem Windows 10, należy włączyć dyskretnej żądania tokenu dla aplikacji. Wtyczka WAM można odnowić PRT podczas żądania tokenu na dwa sposoby:
   * Aplikacja żąda WAM tokenu dostępu dyskretnie, ale token odświeżania nie jest dostępna dla danej aplikacji. W tym przypadku WAM używa PRT do wysłania żądania tokenu dla aplikacji i otrzymuje nowe PRT w odpowiedzi.
   * Aplikacja żąda WAM tokenu dostępu, ale PRT jest nieprawidłowy lub usługa Azure AD wymaga autoryzacji dodatkowe (na przykład usługi Azure Multi-Factor Authentication). W tym scenariuszu WAM inicjuje interakcyjnego logowania, użytkownik musi ponownie uwierzytelniać lub podaj dodatkowej weryfikacji i wystawiono nowy PRT na pomyślne uwierzytelnienie.

### <a name="key-considerations"></a>Najważniejsze zagadnienia

* PRT tylko jest wystawiony i odnawiać każdorazowo podczas uwierzytelniania aplikacji natywnej. PRT jest odnawiana lub nie wydanych w trakcie sesji przeglądarki.
* I w usłudze Azure AD przyłączone do hybrydowej przyłączony Azure AD urządzenia, wtyczka CloudAP jest podstawowym urzędu dla PRT. Jeśli PRT zostanie odnowiony podczas żądania tokenu WAM, PRT jest wysyłane z powrotem do CloudAP wtyczki, które sprawdza poprawność PRT z usługą Azure AD przed zaakceptowaniem go.

## <a name="how-is-the-prt-protected"></a>Jak jest chroniona PRT

PRT jest chroniony przez powiązanie, które go do urządzeń użytkownik zalogował się na. Usługa Azure AD i systemu Windows 10 Włącz ochronę PRT za pomocą następujących metod:

* **Podczas pierwszego logowania**: Podczas pierwszego logowania PRT wystawiony przez podpisywania żądań przy użyciu klucza urządzenia kryptograficznie generowane podczas rejestracji urządzenia. Na urządzeniu z modułem TPM prawidłowe i czy działa klucz urządzenia jest zabezpieczony przez moduł TPM zapobieganie wszelkie nieautoryzowanym dostępem. PRT nie zostanie wystawiony, jeśli nie można sprawdzić poprawności odpowiedniego podpis klucza urządzenia.
* **Podczas żądania tokenu i odnawiania**: Po wygenerowaniu PRT, usługa Azure AD wystawia na urządzeniu klucza szyfrowanej sesji. Jest on zaszyfrowany przy użyciu klucza publicznego transportu (tkpub) wygenerowanych i wysłanych do usługi Azure AD w ramach rejestracji urządzeń. Ten klucz sesji odszyfrować je mogą tylko przy użyciu klucza prywatnego transportu (tkpriv) chronione przez moduł TPM. Klucz sesji to klucz dowodu posiadania (POP) wszystkie żądania wysyłane do usługi Azure AD.  Klucz sesji jest również chroniony przez moduł TPM i innych składników systemu operacyjnego do niego dostęp. Token żądania lub żądania odnowienia PRT bezpiecznie są podpisane przez ten klucz sesji przy użyciu modułu TPM i z tego powodu nie dało się. Usługa Azure AD spowoduje unieważnienie żądań z urządzenia, które nie są podpisane przy użyciu odpowiedniego klucza sesji.

Poprzez zabezpieczenie tych kluczy w module TPM, uczestników złośliwych działań przechwycenie kluczy ani powtarzania PRT w innym miejscu, ponieważ moduł TPM jest niedostępny, nawet jeśli osoba atakująca ma fizyczny dostęp do urządzenia.  W związku z tym, za pomocą modułu TPM znacznie podnosi poziom bezpieczeństwa usługi Azure AD dołączono do hybrydowej przyłączonych do usługi Azure AD, usługa Azure AD zarejestrowany i urządzenia przed kradzieżą poświadczeń. Na wydajność i niezawodność modułu TPM 2.0 jest zalecana wersja dla wszystkich scenariuszy rejestracji urządzeń usługi Azure AD w systemie Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>W jaki sposób tokenów aplikacji i chronione pliki cookie przeglądarki?

**Tokenów aplikacji**: Gdy aplikacja żąda tokenu za pośrednictwem WAM, usługa Azure AD wystawia token odświeżania i token dostępu. Jednak WAM tylko zwraca token dostępu do aplikacji i zabezpiecza token odświeżania w pamięci podręcznej przez ich szyfrowanie kluczem przez użytkownika dane ochrony aplikacji programowania interfejsu (DPAPI). WAM używa bezpiecznego token odświeżania, rejestrując żądań przy użyciu klucza sesji do dalszego wystawiania tokenów dostępu. Klucza DPAPI jest zabezpieczony przez usługi Azure AD na podstawie klucza symetrycznego w usłudze Azure AD sama. Gdy urządzenie musi odszyfrować profilu użytkownika przy użyciu klucza interfejsu DPAPI, usługa Azure AD zapewnia zaszyfrowane przy użyciu klucza sesji, których wtyczka CloudAP żąda modułu TPM do odszyfrowania klucza DPAPI. Ta funkcja zapewnia spójność w zabezpieczaniu tokeny odświeżania i pozwala uniknąć Implementowanie własne mechanizmy ochrony aplikacji.  

**Pliki cookie przeglądarki**: W systemie Windows 10 usługi Azure AD obsługuje logowanie Jednokrotne w programie Internet Explorer i Microsoft Edge przeglądarki natywnie lub w przeglądarce Google Chrome za pośrednictwem rozszerzenia konta systemu Windows 10. Bezpieczeństwo jest wbudowane nie tylko do ochrony plików cookie, ale również punkty końcowe, do których wysyłane są pliki cookie. Pliki cookie są chronione tak samo jest PRT, przy użyciu klucza sesji, zaloguj się do ochrony plików cookie.

Gdy użytkownik inicjuje interakcji z przeglądarką, hosta klienta natywnego modelu COM wywołuje przeglądarki (lub rozszerzenia). Hosta klienta natywnego gwarantuje, że strona jest jedną z dozwolonych domen. Przeglądarka może wysłać inne parametry do natywnego klienta hosta w tym identyfikatora jednorazowego, jednak host natywny klient gwarantuje weryfikacji nazwy hosta. Host natywny klient żąda PRT pliku cookie z CloudAP wtyczki, który tworzy i podpisuje go przy użyciu klucza chronionego przez moduł TPM sesji. Jak plik cookie PRT jest podpisany przy użyciu klucza sesji, go nie dało się. Ten PRT plik cookie znajduje się w nagłówku żądania dla usługi Azure AD do weryfikowania urządzenia, które jest pochodzących. Jeśli używasz przeglądarki Chrome, można wywoływać tylko to rozszerzenie, które są jawnie zdefiniowany w manifeście hosta klienta natywnego go uniemożliwia dowolnego rozszerzenia te żądania. Po PRT plik cookie jest weryfikowana przez usługi Azure AD, generuje plik cookie sesji przeglądarki. Ten plik cookie sesji zawiera także ten sam klucz sesji z PRT. Podczas kolejnych żądań klucza sesji jest weryfikowana skutecznie powiązania pliku cookie na urządzeniu i zapobieganie odtworzenie z innego miejsca.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Kiedy PRT uzyskać oświadczenia usługi MFA?

PRT mogą uzyskać oświadczenia uwierzytelniania wieloskładnikowego (MFA) w określonych scenariuszach. Gdy PRT na podstawie uwierzytelniania Wieloskładnikowego jest używany do żądania tokenów dla aplikacji, oświadczenia usługi MFA jest przekazywana do tych tokenów aplikacji. Ta funkcja zapewnia bezproblemową obsługę użytkowników poprzez uniemożliwienie żądania uwierzytelniania MFA dla każdej aplikacji, który go wymaga. PRT można uzyskać oświadczenia usługi MFA w następujący sposób:

* **Zaloguj się przy użyciu Windows Hello dla firm**: Windows Hello dla firm zastępuje haseł i używa kluczy kryptograficznych w celu zapewnienia silnego uwierzytelniania dwuskładnikowego. Windows Hello dla firm są specyficzne dla użytkownika na urządzeniu, a sama MFA wymaga, aby aprowizować. Gdy użytkownik zaloguje się przy użyciu Windows Hello dla firm, PRT użytkownika pobiera oświadczenia usługi MFA. Ten scenariusz dotyczy również użytkowników logujących się przy użyciu kart inteligentnych, jeśli uwierzytelnienie karty inteligentnej generuje oświadczenia usługi MFA w usługach AD FS.
* **Uwierzytelnianie wieloskładnikowe podczas WAM interakcyjnego logowania**: Podczas żądania tokenu za pośrednictwem WAM Jeśli użytkownik jest wymagane uwierzytelnianie wieloskładnikowe, aby uzyskać dostęp do aplikacji PRT, który jest odnowiona w trakcie ta interakcja jest nadrukowany z oświadczenia usługi MFA.
* **Uwierzytelnianie wieloskładnikowe podczas rejestracji urządzenia**: Jeśli administrator skonfigurował ustawienia ich urządzenia w usłudze Azure AD [wymagać uwierzytelniania Wieloskładnikowego zarejestrować urządzenia](device-management-azure-portal.md#configure-device-settings), użytkownik musi wykonać uwierzytelnianie wieloskładnikowe w celu ukończenia rejestracji. W trakcie tego procesu PRT, wydawanego użytkownikowi zawiera oświadczenia usługi MFA uzyskanego podczas rejestracji. Ta funkcja ma zastosowanie tylko do użytkownika, który wykonał operacji join nie do innych użytkowników, którzy Zaloguj się na tym urządzeniu.

Windows 10 przechowuje listę partycjonowane PRTs dla każdego poświadczenia. Dlatego jest PRT dla każdej usługi Windows Hello dla firm, hasła lub karty inteligentnej. Tej partycji gwarantuje, że oświadczenia usługi MFA są izolowane oparte na poświadczenia używane i nie zostały zamienione podczas żądania tokenu.

## <a name="how-is-a-prt-invalidated"></a>Jak zostaje unieważniony PRT

PRT zostaje unieważniony w następujących scenariuszach:

* **Nieprawidłowy użytkownik**: Jeśli użytkownik jest usunięte lub wyłączone w usłudze Azure AD, ich PRT zostaje unieważniony i nie może służyć do uzyskiwania tokenów dla aplikacji. Jeśli usunięto lub wyłączonego użytkownika zostało to zrobione na urządzenie przed buforowane logowania rejestruje je, dopóki CloudAP ma informacje o jego nieprawidłowym stanie. Po CloudAP okaże się, że użytkownik jest nieprawidłowy, blokuje kolejnych logowań. Nieprawidłowy użytkownik jest automatycznie zablokowane z logowania do nowych urządzeń, które nie mają poświadczeń w pamięci podręcznej.
* **Nieprawidłowe urządzenie**: Jeśli usunięte lub wyłączone w usłudze Azure AD urządzenia PRT uzyskany z tego urządzenia zostaje unieważniony i nie może służyć do uzyskiwania tokenów dla innych aplikacji. Jeśli użytkownik jest już zalogowany do urządzenia z systemem nieprawidłowy, mogą nadal to zrobić. Ale nie są unieważniane wszystkie tokeny na urządzeniu, a użytkownik nie ma logowania jednokrotnego do żadnych zasobów z tego urządzenia.
* **Zmiana hasła**: Po użytkownik zmieni swoje hasło, PRT otrzymany wraz z poprzednich haseł zostaje unieważniony przez usługę Azure AD. Wyniki zmiany hasła użytkownika wprowadzenie nowego PRT. Ta unieważniania może się zdarzyć na dwa sposoby:
   * Jeśli użytkownik loguje się do Windows przy użyciu nowego hasła, CloudAP odrzuca stare PRT i żądań usługi Azure AD, aby wystawić nowy PRT przy użyciu nowego hasła. Jeśli użytkownik nie ma dostępu do Internetu, nie można zweryfikować nowe hasło, Windows może wymagać od użytkownika wprowadzenia ich stare hasło.
   * Jeśli użytkownik ma zalogowania się przy użyciu starego hasła lub ich hasło zostało zmienione po zarejestrowaniu się w Windows, stare PRT służy do żądania tokenu wszelkie WAM. W tym scenariuszu użytkownik jest monitowany o ponowne uwierzytelnianie podczas żądania tokenu WAM i wygenerowaniu nowych PRT.
* **Problemy z modułem TPM**: Czasami urządzenia modułu TPM można falter lub nie powiedzie się, co prowadzi do inaccessibility klucze chronione przez moduł TPM. W takim przypadku urządzenie jest zdolny do pobierania PRT i żądania tokenów przy użyciu istniejących PRT nie udowodnić, że posiadanie kluczy kryptograficznych. W rezultacie wszystkie istniejące PRT zostaje unieważniony przez usługę Azure AD. W przypadku systemu Windows 10 wykryje błąd, inicjuje przepływ odzyskiwania, aby ponownie zarejestrować urządzenie, za pomocą nowych kluczy kryptograficznych. Za pomocą hybrydowych usługi Azure Ad join, podobnie jak wstępnej rejestracji odzyskiwanie odbywa się trybie dyskretnym bez udziału użytkownika. Dla urządzeń zarejestrowanych w usłudze Azure AD przyłączony lub Azure AD, odzyskiwania musi zostać wykonana przez użytkownika, który ma uprawnienia administratora na urządzeniu. W tym scenariuszu przepływ odzyskiwania jest inicjowane przez wiersz Windows, który prowadzi użytkownika, aby pomyślnie odzyskać na urządzeniu.

## <a name="detailed-flows"></a>Szczegółowe przepływów

Następujące diagramy przedstawiają podstawowe szczegóły w wystawiania, odnowienia i żądania tokenu dostępu dla aplikacji przy użyciu PRT. Ponadto te również opisano sposób stosowania mechanizmów zabezpieczeń wyżej podczas tych interakcji.

### <a name="prt-issuance-during-first-sign-in"></a>Wystawiania PRT podczas pierwszego logowania

![Wystawiania PRT podczas pierwszego logowania w usłudze flow szczegółowe](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> W usłudze Azure AD urządzenia przyłączone do, tej wymiany się synchronicznie stanie ogłaszania PRT, zanim użytkownik może logować się na Windows. Hybrydowy urządzeń do usługi Azure AD, w lokalnej usłudze Active Directory jest podstawowej urzędu. Tak użytkownik tylko czeka, dopóki nie można pobrać biletu TGT do logowania, podczas wystawiania PRT się dzieje w sposób asynchroniczny. W tym scenariuszu nie ma zastosowania do usługi Azure AD, zarejestrowanych urządzeń, ponieważ logowanie nie korzysta z poświadczeń usługi Azure AD.

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło logowania w interfejsie użytkownika. LogonUI przekazuje poświadczenia w buforze uwierzytelniania do procesu LSA, która wyłącza wewnętrznie przekazuje go do CloudAP. CloudAP przekazuje na wtyczkę CloudAP tego żądania. |
| B | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru, aby zidentyfikować dostawcy tożsamości dla użytkownika. Jeśli w dzierżawie użytkownika Instalatora dostawcy federacyjnego, usługi Azure AD zwraca wymiany metadanych dostawcy federacyjnego punktu końcowego punktu końcowego (MEX). Jeśli nie, usługi Azure AD zwraca, czy użytkownik jest zarządzany, wskazujący, że z usługą Azure AD można uwierzytelnić tego użytkownika. |
| C | Jeśli użytkownik jest zarządzany, CloudAP otrzyma identyfikator jednorazowy z usługi Azure AD. Jeśli użytkownik jest sfederowana, wtyczki CloudAP żąda tokenu SAML od dostawcy federacyjnego, przy użyciu poświadczeń użytkownika. Po odebraniu tokenu SAML żądań identyfikatora jednorazowego z usługi Azure AD. |
| D | Wtyczka CloudAP tworzy żądanie uwierzytelniania przy użyciu poświadczeń użytkownika, identyfikator jednorazowy i zakres brokera, podpisuje żądanie kluczem urządzenia (dkpriv) i wysyła je do usługi Azure AD. W środowisku federacyjnym CloudAP wtyczka korzysta z tokenu SAML zwracany przez dostawcę federacyjnego zamiast użytkownika "poświadczeń. |
| E | Usługi Azure AD weryfikuje poświadczenia użytkownika, identyfikator jednorazowy, i podpis urządzenie sprawdza, że urządzenie jest prawidłowa w ramach dzierżawy i problemy z zaszyfrowanego PRT. Wraz z PRT usługa Azure AD wystawia klucza symetrycznego, o nazwie klucza sesji szyfrowane przez usługę Azure AD przy użyciu klucza transportu (tkpub). Ponadto klucza sesji jest również osadzona w PRT. Ten klucz sesji działa jako klucz dowodu posiadania (PoP) dla kolejnych żądań PRT. |
| F | Wtyczka CloudAP przekazuje CloudAP zaszyfrowanego klucza PRT i sesji. CloudAP żądań modułu TPM można odszyfrować klucza sesji przy użyciu klucza transportu (tkpriv) i ponownie ją zaszyfrować przy użyciu klucza modułu TPM. CloudAP przechowuje zaszyfrowanego klucza sesji w pamięci podręcznej oraz PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Odnawianie PRT w kolejnych logowań

![Odnawianie PRT w kolejnych logowań](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło logowania w interfejsie użytkownika. LogonUI przekazuje poświadczenia w buforze uwierzytelniania do procesu LSA, która wyłącza wewnętrznie przekazuje go do CloudAP. CloudAP przekazuje na wtyczkę CloudAP tego żądania. |
| B | Jeśli wcześniej zalogował użytkownika dla użytkownika, Windows zainicjuje pamięci podręcznej Zaloguj i sprawdza poprawność poświadczeń do logowania użytkownika. Co 4 godziny wtyczki CloudAP inicjuje odnowienia PRT asynchronicznie. |
| C | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru, aby zidentyfikować dostawcy tożsamości dla użytkownika. Jeśli w dzierżawie użytkownika Instalatora dostawcy federacyjnego, usługi Azure AD zwraca wymiany metadanych dostawcy federacyjnego punktu końcowego punktu końcowego (MEX). Jeśli nie, usługi Azure AD zwraca, czy użytkownik jest zarządzany, wskazujący, że z usługą Azure AD można uwierzytelnić tego użytkownika. |
| D | Jeśli użytkownik jest sfederowana, wtyczki CloudAP żąda tokenu SAML od dostawcy federacyjnego, przy użyciu poświadczeń użytkownika. Po odebraniu tokenu SAML żądań identyfikatora jednorazowego z usługi Azure AD. Jeśli użytkownik jest zarządzany, CloudAP bezpośrednio uzyskać identyfikator jednorazowy z usługi Azure AD. |
| E | Wtyczka CloudAP tworzy żądanie uwierzytelnienia poświadczeń użytkownika, identyfikator jednorazowy i istniejących PRT, podpisuje żądanie przy użyciu klucza sesji i wysyła je do usługi Azure AD. W środowisku federacyjnym CloudAP wtyczka korzysta z tokenu SAML zwracany przez dostawcę federacyjnego zamiast użytkownika "poświadczeń. |
| F | Usługi Azure AD weryfikuje podpis klucza sesji przez porównanie klucza sesji w PRT, sprawdza poprawność identyfikatora jednorazowego i sprawdza, czy urządzenie jest prawidłowa w ramach dzierżawy i wystawia nowe PRT. Jak wspomniano wcześniej, PRT jest ponownie wraz z kluczem sesji szyfrowany przez klucz transportu (tkpub). |
| G | Wtyczka CloudAP przekazuje CloudAP zaszyfrowanego klucza PRT i sesji. CloudAP żąda modułu TPM można odszyfrować klucza sesji przy użyciu klucza transportu (tkpriv) i ponownie ją zaszyfrować przy użyciu klucza modułu TPM. CloudAP przechowuje zaszyfrowanego klucza sesji w pamięci podręcznej oraz PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Użycie PRT podczas żądania tokenu aplikacji

![Użycie PRT podczas żądania tokenu aplikacji](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Opis |
| :---: | --- |
| A | Aplikacji (na przykład, Outlook, OneNote itp.) inicjuje żądania tokenu do WAM. WAM, z kolei pyta, czy wtyczka usługi Azure AD WAM do obsłużenia żądania tokenu. |
| B | Jeśli token odświeżania dla aplikacji jest już dostępna, wtyczka usługi Azure AD WAM używa ich do żądania tokenu dostępu. Poda powiązania urządzenia, wtyczka WAM podpisuje żądanie za pomocą klucza sesji. Usługa Azure AD weryfikuje klucz sesji i wystawia token dostępu i nowego tokena odświeżania dla aplikacji, zaszyfrowane przy użyciu klucza sesji. Wtyczka WAM żądań AP chmury dodatek typu plug-in do odszyfrowywania tokenów, który z kolei żąda modułu TPM można odszyfrować przy użyciu klucza sesji, wynikowa we wtyczce WAM wprowadzenie zarówno tokenów. Następnie WAM wtyczki zawiera tylko token dostępu do aplikacji, ponownie szyfruje token odświeżania przy użyciu interfejsu DPAPI, i zapisuje go w swojej własnej pamięci podręcznej  |
| C |  Jeśli token odświeżania dla aplikacji nie jest dostępna, wtyczka usługi Azure AD WAM używa PRT do wysłania żądania tokenu dostępu. Aby zapewnić dowodu posiadania, wtyczka WAM podpisuje żądanie zawierające PRT przy użyciu klucza sesji. Usługa Azure AD weryfikuje podpis klucza sesji przez porównanie klucza sesji w PRT, sprawdza, czy urządzenie jest prawidłowa i wystawia token dostępu i token odświeżania dla aplikacji. Ponadto usługa Azure AD może wystawiać nowe PRT (oparty na cyklu odświeżania), wszystkie zaszyfrowane przy użyciu klucza sesji. |
| D | Wtyczka WAM żądań AP chmury dodatek typu plug-in do odszyfrowywania tokenów, który z kolei żąda modułu TPM można odszyfrować przy użyciu klucza sesji, wynikowa we wtyczce WAM wprowadzenie zarówno tokenów. Następnie wtyczka WAM zapewnia tylko token dostępu do aplikacji, mimo że ponownie szyfruje token odświeżania przy użyciu interfejsu DPAPI i zapisuje go w swojej własnej pamięci podręcznej. Wtyczka WAM użyje token odświeżania, przechodząc do przodu dla tej aplikacji. Wtyczka WAM zapewnia również ponownie nowe PRT do wtyczki wschodni Region Azji i w chmurze, która weryfikuje PRT z usługą Azure AD przed aktualizacją w jego własnej pamięci podręcznej. Wtyczki AP chmury będą używać nowego PRT, w przyszłości. |
| E | WAM zawiera token dostępu nowo wydane do WAM, który z kolei umożliwia powrót do aplikacji wywołującej|

### <a name="browser-sso-using-prt"></a>Przeglądarka logowania jednokrotnego przy użyciu PRT

![Przeglądarka logowania jednokrotnego przy użyciu PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik loguje się do Windows przy użyciu swoich poświadczeń, aby uzyskać PRT. Po otwarciu przeglądarki, przeglądarki (lub rozszerzenia) ładuje adresy URL z rejestru. |
| B | Gdy użytkownik otwiera adres URL logowania usługi Azure AD, przeglądarki lub rozszerzenie sprawdza poprawność adresu URL z nich uzyskany z rejestru. Jeśli są zgodne, przeglądarka wywołuje hosta klienta natywnego w celu uzyskania tokenu. |
| C | Hosta natywny klient sprawdza poprawność adresy URL należą do dostawcy tożsamości firmy Microsoft (konto Microsoft lub usługi Azure AD), wyodrębnianie identyfikatora jednorazowego wysłana z adresu URL i wywołuje wtyczkę CloudAP można pobrać plik cookie PRT. |
| D | Wtyczka CloudAP utworzysz plik cookie PRT, zaloguj się przy użyciu klucza sesji powiązanych z modułu TPM i wyślij go do hosta klienta natywnego. Jak plik cookie jest podpisany przy użyciu klucza sesji, nie może zostać zmieniony z. |
| E | Hosta klienta natywnego zwróci ten plik cookie PRT do przeglądarki, która będzie zawierać go jako części nagłówka żądania o nazwie x-ms-RefreshTokenCredential i żądania tokenów z usługi Azure AD. |
| F | Usługa Azure AD weryfikuje podpis klucza sesji w pliku cookie PRT, sprawdza poprawność identyfikatora jednorazowego, sprawdza, czy urządzenie jest prawidłowa w ramach dzierżawy i wystawia token Identyfikatora dla strony sieci web i plik cookie szyfrowanej sesji przeglądarki. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z PRT, zobacz artykuł [urządzenia systemu Windows 10 i Windows Server 2016 przyłączone do hybrydowej Rozwiązywanie problemów z usługi Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).
