---
title: Podstawowy token odświeżania (PRT) i usługa Azure AD — usługa Azure Active Directory
description: Jaka jest rola i jak zarządzamy podstawowym tokenem odświeżania (PRT) w usłudze Azure Active Directory?
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
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672647"
---
# <a name="what-is-a-primary-refresh-token"></a>Co to jest podstawowy token odświeżania?

Podstawowy token odświeżania (PRT) jest kluczowym artefaktem uwierzytelniania usługi Azure AD na urządzeniach z systemem Windows 10, iOS i Android. Jest to token json web token (JWT) specjalnie wydane do firmy Microsoft pierwszej firmy brokerów tokenu, aby włączyć logowanie jednokrotne (SSO) w aplikacjach używanych na tych urządzeniach. W tym artykule przedstawimy szczegółowe informacje na temat sposobu wydawania, używanego i chronionego PRT na urządzeniach z systemem Windows 10.

W tym artykule przyjęto założenie, że znasz już różne stany urządzeń dostępne w usłudze Azure AD i jak działa znak jednokrotny w systemie Windows 10. Aby uzyskać więcej informacji o urządzeniach w usłudze Azure AD, zobacz artykuł [Co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Kluczowa terminologia i komponenty

Następujące składniki systemu Windows odgrywają kluczową rolę w żądaniu i używaniu PRT:

* **Dostawca uwierzytelniania** w chmurze (CloudAP): CloudAP jest nowoczesnym dostawcą uwierzytelniania dla logowania systemu Windows, który weryfikuje użytkowników logujących się do urządzenia z systemem Windows 10. CloudAP zapewnia platformę wtyczek, na podstawie których dostawcy tożsamości mogą korzystać, aby włączyć uwierzytelnianie w systemie Windows przy użyciu poświadczeń tego dostawcy tożsamości.
* **Menedżer konta sieci Web** (WAM): WAM jest domyślnym brokerem tokenów na urządzeniach z systemem Windows 10. WAM zapewnia również platformę wtyczek, na podstawie których dostawcy tożsamości mogą chylić i włączać funkcję rejestracji sytej w swoich aplikacjach, korzystając z tego dostawcy tożsamości.
* **Wtyczka Azure AD CloudAP:** wtyczka specyficzne dla usługi Azure AD oparta na platformie CloudAP, która weryfikuje poświadczenia użytkownika za pomocą usługi Azure AD podczas logowania systemu Windows.
* **Wtyczka Usługi Azure AD WAM:** wtyczka specyficzne dla usługi Azure AD oparta na platformie WAM, która umożliwia rejestracji w błąd dla aplikacji, które opierają się na usłudze Azure AD do uwierzytelniania.
* **Dsreg:** składnik specyficzne dla usługi Azure AD w systemie Windows 10, który obsługuje proces rejestracji urządzenia dla wszystkich stanów urządzenia.
* Moduł TPM **(Trusted Platform Module):** moduł TPM to składnik sprzętowy wbudowany w urządzenie, który udostępnia sprzętowe funkcje zabezpieczeń dla wpisów tajnych użytkowników i urządzeń. Więcej szczegółów można znaleźć w artykule [Przegląd technologii modułu zaufanej platformy](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co zawiera PRT?

PRT zawiera oświadczenia zazwyczaj zawarte w dowolnym tokenie odświeżania usługi Azure AD. Ponadto istnieją pewne oświadczenia specyficzne dla urządzenia zawarte w PRT. Są one następujące:

* **Identyfikator urządzenia:** PRT jest wydawany użytkownikowi na określonym urządzeniu. Oświadczenie `deviceID` identyfikatora urządzenia określa urządzenie, na którym został wystawiony użytkownikowi. Oświadczenie to jest później wydawane tokenom uzyskanym za pośrednictwem PRT. Oświadczenie identyfikatora urządzenia służy do określania autoryzacji dostępu warunkowego na podstawie stanu urządzenia lub zgodności.
* **Klucz sesji:** Klucz sesji jest zaszyfrowanym kluczem symetrycznym, generowanym przez usługę uwierzytelniania usługi Azure AD, wydanym jako część PRT. Klucz sesji działa jako dowód posiadania, gdy PRT jest używany do uzyskiwania tokenów dla innych aplikacji.

### <a name="can-i-see-whats-in-a-prt"></a>Czy mogę zobaczyć, co jest w PRT?

PRT jest nieprzezroczystym obiektem blob wysłanym z usługi Azure AD, którego zawartość nie jest znana żadnym składnikom klienta. Nie widać, co znajduje się wewnątrz PRT.

## <a name="how-is-a-prt-issued"></a>W jaki sposób wystawiany jest PRT?

Rejestracja urządzenia jest warunkiem wstępnym uwierzytelniania opartego na urządzeniu w usłudze Azure AD. PRT jest wydawane użytkownikom tylko na zarejestrowanych urządzeniach. Aby uzyskać więcej szczegółowych informacji na temat rejestracji urządzeń, zobacz artykuł [Windows Hello dla firm i rejestracji urządzeń](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Podczas rejestracji urządzenia składnik dsreg generuje dwa zestawy par kluczy kryptograficznych:

* Klucz urządzenia (dkpub/dkpriv)
* Klucz transportowy (tkpub/tkpriv)

Klucze prywatne są powiązane z modułem TPM urządzenia, jeśli urządzenie ma prawidłowy i działający moduł TPM, podczas gdy klucze publiczne są wysyłane do usługi Azure AD podczas procesu rejestracji urządzenia. Klucze te są używane do sprawdzania poprawności stanu urządzenia podczas żądań PRT.

PRT jest wystawiany podczas uwierzytelniania użytkownika na urządzeniu z systemem Windows 10 w dwóch scenariuszach:

* **Usługa Azure AD przyłączona** lub **przyłączona do usługi Azure AD hybrydowa:** PRT jest wystawiany podczas logowania systemu Windows, gdy użytkownik loguje się przy użyciu poświadczeń organizacji. PRT jest wydawane ze wszystkimi poświadczeniami obsługiwanymi przez system Windows 10, na przykład hasłem i windows hello dla firm. W tym scenariuszu wtyczka Azure AD CloudAP jest podstawowym urzędem dla PRT.
* **Urządzenie zarejestrowane w usłudze Azure AD:** PRT jest wystawiane, gdy użytkownik dodaje dodatkowe konto pracy do swojego urządzenia z systemem Windows 10. Użytkownicy mogą dodać konto do systemu Windows 10 na dwa różne sposoby -  
   * Dodawanie konta za pośrednictwem monitu **Użyj tego konta wszędzie na tym urządzeniu** po zalogowaniu się do aplikacji (na przykład Outlook)
   * Dodawanie konta z **pozycji Ustawienia** > **dostęp** > do konta**pracy lub połączenia szkolnego** > **Connect**

W scenariuszach urządzeń zarejestrowanych w usłudze Azure AD wtyczka Azure AD WAM jest podstawowym urzędem dla PRT, ponieważ logowanie systemu Windows nie odbywa się z tym kontem usługi Azure AD.

> [!NOTE]
> Dostawcy tożsamości innych firm muszą obsługiwać protokół WS-Trust, aby umożliwić wydawanie PRT na urządzeniach z systemem Windows 10. Bez usługi WS-Trust nie można wystawiać PRT użytkownikom na urządzeniach przyłączonych do usługi Azure AD lub usługi Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Jaki jest okres istnienia PRT?

Po wydaniu PRT jest ważny przez 14 dni i jest stale odnawiany tak długo, jak długo użytkownik aktywnie korzysta z urządzenia.  

## <a name="how-is-a-prt-used"></a>W jaki sposób stosuje się PRT?

PRT jest używany przez dwa kluczowe składniki w systemie Windows:

* **Wtyczka Azure AD CloudAP:** Podczas logowania systemu Windows wtyczka Azure AD CloudAP żąda PRT z usługi Azure AD przy użyciu poświadczeń dostarczonych przez użytkownika. Jest również buforowana PRT, aby włączyć buforowane logowania, gdy użytkownik nie ma dostępu do połączenia internetowego.
* **Wtyczka usługi Azure AD WAM:** Gdy użytkownicy próbują uzyskać dostęp do aplikacji, wtyczka Azure AD WAM używa PRT, aby włączyć sso w systemie Windows 10. Wtyczka usługi Azure AD WAM używa PRT do żądania tokenów odświeżania i dostępu dla aplikacji, które opierają się na WAM dla żądań tokenu. Umożliwia również wprowadzenie identyfikatora SSO w przeglądarkach, wstrzykując PRT do żądań przeglądarki. Sso przeglądarki w systemie Windows 10 jest obsługiwane w programie Microsoft Edge (natywnie) i Chrome (za pośrednictwem kont systemu Windows 10 lub rozszerzenia Office Online).

## <a name="how-is-a-prt-renewed"></a>W jaki sposób odnawiany jest PRT?

PRT jest odnawiany w dwóch różnych metodach:

* **Wtyczka Azure AD CloudAP co 4 godziny:** wtyczka CloudAP odnawia PRT co 4 godziny podczas logowania systemu Windows. Jeśli użytkownik nie ma połączenia z Internetem w tym czasie, wtyczka CloudAP odnowi PRT po podłączeniu urządzenia do Internetu.
* **Wtyczka Usługi Azure AD WAM podczas żądań tokenów aplikacji:** Wtyczka WAM umożliwia sso na urządzeniach z systemem Windows 10, włączając ciche żądania tokenów dla aplikacji. Wtyczka WAM można odnowić PRT podczas tych żądań tokenu na dwa różne sposoby:
   * Aplikacja żąda WAM token dostępu dyskretnie, ale nie ma tokenu odświeżania dostępne dla tej aplikacji. W takim przypadku WAM używa PRT do żądania tokenu dla aplikacji i pobiera z powrotem nowy PRT w odpowiedzi.
   * Aplikacja żąda WAM dla tokenu dostępu, ale PRT jest nieprawidłowy lub usługa Azure AD wymaga dodatkowej autoryzacji (na przykład azure uwierzytelnianie wieloskładnikowe). W tym scenariuszu WAM inicjuje interaktywne logowanie wymagające od użytkownika ponownego uwierzytelnienia lub zapewnienia dodatkowej weryfikacji, a nowy identyfikator PRT jest wystawiany po pomyślnym uwierzytelnieniu.

### <a name="key-considerations"></a>Najważniejsze zagadnienia

* PRT jest wystawiany i odnawiany tylko podczas uwierzytelniania aplikacji natywnej. PRT nie jest odnawiany ani wystawiany podczas sesji przeglądarki.
* W usłudze Azure AD przyłączone i hybrydowe urządzenia platformy Azure AD dołączył wtyczki CloudAP jest podstawowym organem dla PRT. Jeśli PRT jest odnawiany podczas żądania tokenu opartego na wycieńczuł, PRT jest wysyłany z powrotem do wtyczki CloudAP, która weryfikuje ważność PRT z usługą Azure AD przed zaakceptowaniem go.

## <a name="how-is-the-prt-protected"></a>W jaki sposób chronione jest PRT?

PRT jest chroniony przez powiązanie go z urządzeniem, do na które użytkownik się zalogował. Usługi Azure AD i Windows 10 umożliwiają ochronę PRT za pomocą następujących metod:

* **Podczas pierwszego logowania**: Podczas pierwszego logowania PRT jest wystawiany przez żądania podpisywania przy użyciu klucza urządzenia kryptograficznie generowane podczas rejestracji urządzenia. Na urządzeniu z prawidłowym i działającym modułem TPM klucz urządzenia jest zabezpieczony przez moduł TPM uniemożliwiający złośliwy dostęp. PRT nie jest wystawiany, jeśli nie można zweryfikować odpowiedniego podpisu klucza urządzenia.
* **Podczas żądania tokenu i odnawianie:** Po wystawieniu PRT usługi Azure AD również wystawia zaszyfrowany klucz sesji do urządzenia. Jest szyfrowany przy pomocą klucza transportu publicznego (tkpub) generowane i wysyłane do usługi Azure AD w ramach rejestracji urządzenia. Ten klucz sesji można odszyfrować tylko przez klucz transportu prywatnego (tkpriv) zabezpieczony przez moduł TPM. Klucz sesji jest kluczem dowód posiadania (POP) dla wszystkich żądań wysyłanych do usługi Azure AD.  Klucz sesji jest również chroniony przez moduł TPM i żaden inny składnik systemu operacyjnego nie może uzyskać do niego dostępu. Żądania tokenu lub żądania odnowienia PRT są bezpiecznie podpisane przez ten klucz sesji za pośrednictwem modułu TPM i w związku z tym nie mogą być modyfikowane. Usługa Azure AD unieważni wszystkie żądania z urządzenia, które nie są podpisane przez odpowiedni klucz sesji.

Zabezpieczając te klucze za pomocą modułu TPM, złośliwi aktorzy nie mogą ukraść kluczy ani odtworzyć PRT w innym miejscu, ponieważ moduł TPM jest niedostępny, nawet jeśli osoba atakująca ma fizyczne posiadanie urządzenia.  W związku z tym przy użyciu modułu TPM znacznie zwiększa bezpieczeństwo usługi Azure AD joined, hybrid Azure AD przyłączone i urządzenia zarejestrowane usługi Azure AD przed kradzieżą poświadczeń. Ze względu na wydajność i niezawodność moduł TPM 2.0 jest zalecaną wersją dla wszystkich scenariuszy rejestracji urządzeń usługi Azure AD w systemie Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>W jaki sposób tokeny aplikacji i pliki cookie przeglądarki są chronione?

**Tokeny aplikacji:** Gdy aplikacja żąda tokenu za pośrednictwem wam, usługi Azure AD wystawia token odświeżania i token dostępu. Jednak WAM zwraca tylko token dostępu do aplikacji i zabezpiecza token odświeżania w pamięci podręcznej, szyfrując go za pomocą klucza interfejsu programowania aplikacji ochrony danych użytkownika (DPAPI). WAM bezpiecznie używa tokenu odświeżania przez podpisywanie żądań z kluczem sesji do wystawiania dodatkowych tokenów dostępu. Klucz DPAPI jest zabezpieczony przez klucz symetryczny oparty na usłudze Azure AD w samej usłudze Azure AD. Gdy urządzenie musi odszyfrować profil użytkownika za pomocą klucza DPAPI, usługa Azure AD udostępnia klucz DPAPI zaszyfrowany przez klucz sesji, który wtyczka CloudAP żąda modułu TPM do odszyfrowania. Ta funkcja zapewnia spójność w zabezpieczaniu tokenów odświeżania i pozwala uniknąć aplikacji implementujących własne mechanizmy ochrony.  

**Pliki cookie przeglądarki:** W systemie Windows 10 usługa Azure AD obsługuje sygosk jednosojowe przeglądarki w programie Internet Explorer i Microsoft Edge natywnie lub w Google Chrome za pośrednictwem rozszerzenia kont systemu Windows 10. Zabezpieczenia są tworzone nie tylko w celu ochrony plików cookie, ale także punktów końcowych, do których są wysyłane pliki cookie. Pliki cookie przeglądarki są chronione w taki sam sposób, jak PRT, wykorzystując klucz sesji do podpisywania i ochrony plików cookie.

Gdy użytkownik inicjuje interakcję z przeglądarką, przeglądarka (lub rozszerzenie) wywołuje natywnego hosta klienta COM. Macierzysty host klienta zapewnia, że strona pochodzi z jednej z dozwolonych domen. Przeglądarka może wysyłać inne parametry do macierzystego hosta klienta, w tym nonce, jednak natywny host klienta gwarantuje sprawdzenie poprawności nazwy hosta. Natywny host klienta żąda pliku COOKIE PRT z wtyczki CloudAP, która tworzy i podpisuje go za pomocą klucza sesji chronionego modułu TPM. Ponieważ plik cookie PRT jest podpisany przez klucz sesji, nie można go manipulować. Ten plik cookie PRT znajduje się w nagłówku żądania dla usługi Azure AD, aby sprawdzić poprawność urządzenia, z której pochodzi. Jeśli używasz przeglądarki Chrome, tylko rozszerzenie jawnie zdefiniowane w manifeście hosta klienta macierzystego może wywołać go, uniemożliwiając dowolne rozszerzenia z tych żądań. Gdy usługa Azure AD sprawdza poprawność pliku cookie PRT, wystawia plik cookie sesji do przeglądarki. Ten plik cookie sesji zawiera również ten sam klucz sesji wystawiony z PRT. Podczas kolejnych żądań klucz sesji jest skutecznie weryfikowany, wiążąc plik cookie z urządzeniem i zapobiegając powtórkom z innego miejsca.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Kiedy PRT otrzymuje oświadczenie o pomocy makrofinansowej?

PRT można uzyskać oświadczenie uwierzytelniania wieloskładnikowego (MFA) w określonych scenariuszach. Gdy PRT oparte na umięsienia jest używany do żądania tokenów dla aplikacji, oświadczenie usługi MFA jest przenoszony do tych tokenów aplikacji. Ta funkcja zapewnia bezproblemowe środowisko dla użytkowników, zapobiegając wyzwaniu usługi MFA dla każdej aplikacji, która tego wymaga. PRT może uzyskać oświadczenie usługi MFA w następujący sposób:

* **Zaloguj się za pomocą funkcji Windows Hello dla firm:** Funkcja Windows Hello dla firm zastępuje hasła i używa kluczy kryptograficznych w celu zapewnienia silnego uwierzytelniania dwuskładnikowego. Windows Hello for Business jest specyficzne dla użytkownika na urządzeniu i sam wymaga usługi MFA do aprowizowania. Gdy użytkownik loguje się za pomocą funkcji Windows Hello dla firm, PRT użytkownika otrzymuje oświadczenie usługi MFA. Ten scenariusz dotyczy również użytkowników logujących się za pomocą kart inteligentnych, jeśli uwierzytelnianie kartą inteligentną powoduje powstanie usługi MFA z usługi ADFS.
   * Ponieważ windows Hello for Business jest uważany za uwierzytelnianie wieloskładnikowe, oświadczenie usługi MFA jest aktualizowane, gdy sam PRT jest odświeżany, więc czas trwania usługi MFA będzie stale się rozszerzał, gdy użytkownicy logują się za pomocą funkcji WIndows Hello for Business
* **Usługa MFA podczas interakcyjnego logowania WAM:** Podczas żądania tokenu za pośrednictwem wam, jeśli użytkownik jest wymagany do wykonania usługi MFA, aby uzyskać dostęp do aplikacji, PRT, który jest odnawiany podczas tej interakcji jest nadrukowany z oświadczeniem usługi MFA.
   * W takim przypadku oświadczenie usługi MFA nie jest aktualizowana w sposób ciągły, więc czas trwania usługi MFA jest oparty na okresie istnienia ustawionym w katalogu.
   * Gdy poprzedni istniejący PRT i RT są używane do dostępu do aplikacji, PRT i RT będą traktowane jako pierwszy dowód uwierzytelniania. Nowy AT będzie wymagane z drugiego dowodu i nadrukowane roszczenia MFA. Spowoduje to również wydanie nowego PRT i RT.
* **Usługa MFA podczas rejestracji urządzenia:** Jeśli administrator skonfigurował ustawienia urządzenia w usłudze Azure AD, aby [wymagać usługi MFA do rejestrowania urządzeń,](device-management-azure-portal.md#configure-device-settings)użytkownik musi wykonać uwierzytelnianie wieloskładnikowe, aby ukończyć rejestrację. Podczas tego procesu PRT, który jest wystawiany użytkownikowi ma oświadczenie usługi MFA uzyskane podczas rejestracji. Ta funkcja dotyczy tylko użytkownika, który wykonał operację sprzężenia, a nie innych użytkowników, którzy logują się do tego urządzenia.
   * Podobnie jak w przypadku logowania interaktywnego WAM, oświadczenie usługi MFA nie jest aktualizowane w sposób ciągły, więc czas trwania usługi MFA jest oparty na okresie istnienia ustawionym w katalogu.

System Windows 10 przechowuje partycjonowane listy PRT dla każdego poświadczenia. Tak, istnieje PRT dla każdego z Windows Hello dla firm, hasło, lub karty inteligentnej. Ta partycjonowanie zapewnia, że oświadczenia usługi MFA są izolowane na podstawie poświadczeń używanych i nie miesza się podczas żądań tokenu.

## <a name="how-is-a-prt-invalidated"></a>W jaki sposób unieważnia się PRT?

PRT jest unieważniony w następujących scenariuszach:

* **Nieprawidłowy użytkownik:** Jeśli użytkownik zostanie usunięty lub wyłączony w usłudze Azure AD, jego PRT jest unieważniony i nie można użyć do uzyskania tokenów dla aplikacji. Jeśli użytkownik usunięty lub wyłączony zalogował się już na urządzeniu przed, buforowane logowanie będzie je zalogować, dopóki CloudAP nie jest świadomy ich nieprawidłowego stanu. Gdy CloudAP określa, że użytkownik jest nieprawidłowy, blokuje kolejne logowania. Nieprawidłowy użytkownik jest automatycznie blokowany przed logowanie do nowych urządzeń, które nie mają ich poświadczenia buforowane.
* **Nieprawidłowe urządzenie:** Jeśli urządzenie zostanie usunięte lub wyłączone w usłudze Azure AD, PRT uzyskane na tym urządzeniu jest unieważnione i nie można użyć do uzyskania tokenów dla innych aplikacji. Jeśli użytkownik jest już zalogowany do nieprawidłowego urządzenia, może to kontynuować. Ale wszystkie tokeny na urządzeniu są unieważnione i użytkownik nie ma rejestracji w dowolnym momencie do żadnych zasobów z tego urządzenia.
* **Zmiana hasła:** Po zmianie hasła przez użytkownika prt uzyskany przy poprzednim haśle jest unieważniony przez usługę Azure AD. Zmiana hasła powoduje, że użytkownik otrzymuje nowy PRT. To unieważnienie może się zdarzyć na dwa różne sposoby:
   * Jeśli użytkownik zaloguje się do systemu Windows za pomocą nowego hasła, CloudAP odrzuca stary PRT i żąda usługi Azure AD do wydania nowego PRT z nowym hasłem. Jeśli użytkownik nie ma połączenia z Internetem, nie można zweryfikować nowego hasła, system Windows może wymagać od użytkownika wprowadzenia starego hasła.
   * Jeśli użytkownik zalogował się przy użyciu starego hasła lub zmienił hasło po zalogowaniu się do systemu Windows, stary PRT jest używany dla wszystkich żądań tokenów opartych na wycieleniu. W tym scenariuszu użytkownik jest monitowany o ponowne uwierzytelnienie podczas żądania tokenu WAM i zostanie wystawiony nowy PRT.
* **Problemy z modułem TPM:** Czasami moduł TPM urządzenia może słabnąć lub zakończyć się niepowodzeniem, co prowadzi do niedostępności kluczy zabezpieczonych modułem TPM. W takim przypadku urządzenie nie jest w stanie uzyskać PRT lub żądać tokenów przy użyciu istniejącego PRT, ponieważ nie może udowodnić posiadania kluczy kryptograficznych. W rezultacie wszystkie istniejące PRT jest unieważniony przez usługę Azure AD. Gdy system Windows 10 wykryje błąd, inicjuje przepływ odzyskiwania, aby ponownie zarejestrować urządzenie za pomocą nowych kluczy kryptograficznych. Z hybrydowego sprzężenia usługi Azure Ad, podobnie jak początkowej rejestracji, odzyskiwanie odbywa się po cichu bez danych wejściowych użytkownika. W przypadku urządzeń przyłączonych do usługi Azure AD lub zarejestrowanych w usłudze Azure AD odzyskiwanie musi być wykonywane przez użytkownika, który ma uprawnienia administratora na urządzeniu. W tym scenariuszu przepływ odzyskiwania jest inicjowany przez monit systemu Windows, który prowadzi użytkownika do pomyślnego odzyskania urządzenia.

## <a name="detailed-flows"></a>Przepływy szczegółowe

Na poniższych diagramach przedstawiono podstawowe szczegóły w wydawaniu, odnawianiu i używaniu PRT do żądania tokenu dostępu dla aplikacji. Ponadto w tych krokach opisano również, w jaki sposób wyżej wymienione mechanizmy zabezpieczeń są stosowane podczas tych interakcji.

### <a name="prt-issuance-during-first-sign-in"></a>Wydawanie PRT podczas pierwszego logowania

![Wydawanie PRT podczas pierwszego logowania w szczegółowym przepływie](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> W urządzeniach przyłączonych do usługi Azure AD ta wymiana odbywa się synchronicznie, aby wydać PRT, zanim użytkownik będzie mógł zalogować się do systemu Windows. W hybrydowych urządzeniach przyłączonych do usługi Azure AD lokalna usługa Active Directory jest podstawowym urzędem. Tak, użytkownik czeka tylko, aż mogą uzyskać TGT do logowania, podczas gdy wydawanie PRT dzieje się asynchronicznie. Ten scenariusz nie dotyczy urządzeń zarejestrowanych w usłudze Azure AD, ponieważ logowanie nie używa poświadczeń usługi Azure AD.

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło w interfejsie logowania logowania. LognUI przekazuje poświadczenia w buforze akcesji do LSA, który z kolei przekazuje go wewnętrznie do CloudAP. CloudAP przekazuje to żądanie do wtyczki CloudAP. |
| B | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru w celu zidentyfikowania dostawcy tożsamości dla użytkownika. Jeśli dzierżawa użytkownika ma konfigurację dostawcy federacji, usługa Azure AD zwraca punkt końcowy punktu końcowego programu Metadata Exchange (MEX) dostawcy federacji. Jeśli nie, usługa Azure AD zwraca, że użytkownik jest zarządzany wskazując, że użytkownik może uwierzytelnić się za pomocą usługi Azure AD. |
| C | Jeśli użytkownik jest zarządzany, CloudAP otrzyma nonce z usługi Azure AD. Jeśli użytkownik jest sfederowany, wtyczka CloudAP żąda tokenu SAML od dostawcy federacji z poświadczeniami użytkownika. Po odebraniu tokenu SAML żąda nonce z usługi Azure AD. |
| D | Wtyczka CloudAP konstruuje żądanie uwierzytelniania z poświadczeniami użytkownika, nonce i zakres brokera, podpisuje żądanie za pomocą klucza urządzenia (dkpriv) i wysyła go do usługi Azure AD. W środowisku federacyjnym wtyczka CloudAP używa tokenu SAML zwróconego przez dostawcę federacji zamiast poświadczeń użytkownika. |
| E | Usługa Azure AD sprawdza poprawność poświadczeń użytkownika, identyfikatora ściernego i podpisu urządzenia, sprawdza, czy urządzenie jest prawidłowe w dzierżawie i wystawia zaszyfrowany PRT. Wraz z PRT usługi Azure AD również wystawia klucz symetryczny, o nazwie klucz sesji zaszyfrowane przez usługę Azure AD przy użyciu klucza transportu (tkpub). Ponadto klucz session jest również osadzony w PRT. Ten klucz sesji działa jako klucz dowód posiadania (PoP) dla kolejnych żądań z PRT. |
| F | Wtyczka CloudAP przekazuje zaszyfrowany klucz PRT i session do CloudAP. CloudAP żąda modułu TPM, aby odszyfrować klucz sesji przy użyciu klucza transportowego (tkpriv) i ponownie zaszyfrować go przy użyciu własnego klucza modułu TPM. CloudAP przechowuje zaszyfrowany klucz sesji w pamięci podręcznej wraz z PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Odnowienie PRT w kolejnych logowaniach

![Odnowienie PRT w kolejnych logowaniach](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło w interfejsie logowania logowania. LognUI przekazuje poświadczenia w buforze akcesji do LSA, który z kolei przekazuje go wewnętrznie do CloudAP. CloudAP przekazuje to żądanie do wtyczki CloudAP. |
| B | Jeśli użytkownik wcześniej zalogował się do użytkownika, system Windows inicjuje buforowane logowanie i sprawdza poprawność poświadczeń w celu zalogowania użytkownika. Co 4 godziny wtyczka CloudAP inicjuje asynchronicznie odnawianie PRT. |
| C | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru w celu zidentyfikowania dostawcy tożsamości dla użytkownika. Jeśli dzierżawa użytkownika ma konfigurację dostawcy federacji, usługa Azure AD zwraca punkt końcowy punktu końcowego programu Metadata Exchange (MEX) dostawcy federacji. Jeśli nie, usługa Azure AD zwraca, że użytkownik jest zarządzany wskazując, że użytkownik może uwierzytelnić się za pomocą usługi Azure AD. |
| D | Jeśli użytkownik jest sfederowany, wtyczka CloudAP żąda tokenu SAML od dostawcy federacji z poświadczeniami użytkownika. Po odebraniu tokenu SAML żąda nonce z usługi Azure AD. Jeśli użytkownik jest zarządzany, CloudAP będzie bezpośrednio uzyskać nonce z usługi Azure AD. |
| E | Wtyczka CloudAP konstruuje żądanie uwierzytelniania z poświadczeniami użytkownika, nonce i istniejącym PRT, podpisuje żądanie za pomocą klucza Session i wysyła je do usługi Azure AD. W środowisku federacyjnym wtyczka CloudAP używa tokenu SAML zwróconego przez dostawcę federacji zamiast poświadczeń użytkownika. |
| F | Usługa Azure AD sprawdza poprawność podpisu klucza sesji, porównując go z kluczem sesji osadzonym w PRT, sprawdza poprawność identyfikatora ściętego i sprawdza, czy urządzenie jest prawidłowe w dzierżawie i wystawia nowy PRT. Jak widać wcześniej, PRT jest ponownie towarzyszy klucz sesji zaszyfrowane przez klucz transportu (tkpub). |
| G | Wtyczka CloudAP przekazuje zaszyfrowany klucz PRT i session do CloudAP. CloudAP żąda modułu TPM do odszyfrowania klucza sesji przy użyciu klucza transportowego (tkpriv) i ponownego zaszyfrowania go przy użyciu własnego klucza modułu TPM. CloudAP przechowuje zaszyfrowany klucz sesji w pamięci podręcznej wraz z PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Użycie PRT podczas żądań tokenów aplikacji

![Użycie PRT podczas żądań tokenów aplikacji](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Opis |
| :---: | --- |
| A | Aplikacja (na przykład Outlook, OneNote itp.) inicjuje żądanie tokenu do WAM. WAM z kolei prosi wtyczkę usługi Azure AD WAM do obsługi żądania tokenu. |
| B | Jeśli token odświeżania dla aplikacji jest już dostępny, wtyczka Azure AD WAM używa go do żądania tokenu dostępu. Aby dostarczyć dowód powiązania urządzenia, wtyczka WAM podpisuje żądanie za pomocą klucza sesji. Usługa Azure AD sprawdza poprawność klucza sesji i wystawia token dostępu i nowy token odświeżania aplikacji, zaszyfrowany przez klucz sesji. Wtyczka WAM żąda wtyczki Cloud AP do odszyfrowania tokenów, co z kolei żąda odszyfrowania modułu TPM przy użyciu klucza session, w wyniku czego wtyczka WAM otrzymuje oba tokeny. Następnie wtyczka WAM udostępnia tylko token dostępu do aplikacji, podczas gdy ponownie szyfruje token odświeżania za pomocą DPAPI i przechowuje go we własnej pamięci podręcznej  |
| C |  Jeśli token odświeżania dla aplikacji nie jest dostępny, wtyczka Azure AD WAM używa PRT do żądania tokenu dostępu. Aby dostarczyć dowód posiadania, wtyczka WAM podpisuje żądanie zawierające PRT za pomocą klucza Session. Usługa Azure AD sprawdza poprawność podpisu klucza sesji, porównując go z kluczem sesji osadzonym w PRT, sprawdza, czy urządzenie jest prawidłowe i wystawia token dostępu i token odświeżania dla aplikacji. ponadto usługa Azure AD może wydać nowy PRT (na podstawie cyklu odświeżania), wszystkie z nich zaszyfrowane przez klucz session. |
| D | Wtyczka WAM żąda wtyczki Cloud AP do odszyfrowania tokenów, co z kolei żąda odszyfrowania modułu TPM przy użyciu klucza session, w wyniku czego wtyczka WAM otrzymuje oba tokeny. Następnie wtyczka WAM udostępnia tylko token dostępu do aplikacji, podczas gdy ponownie szyfruje token odświeżania za pomocą dpapi i przechowuje go we własnej pamięci podręcznej. Wtyczka WAM użyje tokenu odświeżania dla tej aplikacji. Wtyczka WAM również oddaje nową wtyczkę PRT do Cloud AP, która sprawdza poprawność PRT za pomocą usługi Azure AD przed zaktualizowaniem jej we własnej pamięci podręcznej. Wtyczka Cloud AP będzie korzystać z nowego PRT w przyszłości. |
| E | WAM udostępnia nowo wydany token dostępu do WAM, który z kolei zapewnia go z powrotem do aplikacji wywołującej|

### <a name="browser-sso-using-prt"></a>SSO przeglądarki przy użyciu PRT

![SSO przeglądarki przy użyciu PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik loguje się do systemu Windows przy użyciu poświadczeń, aby uzyskać PRT. Gdy użytkownik otworzy przeglądarkę, przeglądarka (lub rozszerzenie) ładuje adresy URL z rejestru. |
| B | Gdy użytkownik otworzy adres URL logowania usługi Azure AD, przeglądarka lub rozszerzenie sprawdza poprawność adresu URL z tymi uzyskanymi z rejestru. Jeśli są one zgodne, przeglądarka wywołuje hosta klienta macierzystego do uzyskania tokenu. |
| C | Natywny host klienta sprawdza, czy adresy URL należą do dostawców tożsamości firmy Microsoft (konto Microsoft lub usługa Azure AD), wyodrębnia ściągło wysyłane z adresu URL i wywołuje wtyczkę CloudAP, aby uzyskać plik cookie PRT. |
| D | Wtyczka CloudAP utworzy plik cookie PRT, zaloguj się przy pomocą klucza sesji powiązanego z modułem TPM i wyśle go z powrotem do macierzystego hosta klienta. Ponieważ plik cookie jest podpisany przez klucz sesji, nie można go manipulować. |
| E | Natywny host klienta zwróci ten plik cookie PRT do przeglądarki, która będzie zawierać go jako część nagłówka żądania o nazwie x-ms-RefreshTokenCredential i tokeny żądania z usługi Azure AD. |
| F | Usługa Azure AD sprawdza poprawność podpisu klucza sesji w pliku cookie PRT, sprawdza poprawność nonce, sprawdza, czy urządzenie jest prawidłowe w dzierżawie i wystawia token identyfikatora dla strony sieci web i zaszyfrowanego pliku cookie sesji dla przeglądarki. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z PRT, zobacz artykuł [Rozwiązywanie problemów z hybrydową usługą Azure Active Directory przyłączonych do urządzeń z systemem Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
