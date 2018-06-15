---
title: Jak włączyć logowanie Jednokrotne wielu aplikacji w systemie iOS przy użyciu biblioteki ADAL | Dokumentacja firmy Microsoft
description: 'Jak używać funkcji ADAL zestawu SDK do Włącz rejestrację jednokrotną w aplikacji. '
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: b0179e217a23d612ddcdc219f589a5d1fbca644e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157252"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Jak włączyć logowanie Jednokrotne wielu aplikacji w systemie iOS przy użyciu biblioteki ADAL
Zapewniające pojedynczego logowania jednokrotnego (SSO), aby użytkownicy potrzebują tylko może wprowadzić swoje poświadczenia raz, a te poświadczenia automatycznie działać przez aplikacje teraz jest oczekiwany przez klientów. Trudności przy wprowadzaniu swoją nazwę użytkownika i hasło na małego ekranu, często razy łączyć się przy użyciu dodatkowego składnika (2FA), takich jak rozmowa telefoniczna lub kod wysłana wiadomość SMS, powoduje niezadowolenie szybki, jeśli użytkownik ma w tym celu więcej niż jeden raz na produkt.

Ponadto w przypadku zastosowania platformą tożsamości, które inne aplikacje mogą używać takich jak Accounts Microsoft lub konta służbowego z usługi Office 365, klienci oczekują, że te poświadczenia były dostępne do użycia w swoich aplikacjach niezależnie od dostawcy.

Platformy pakietu Microsoft Identity, wraz z nasze zestawy SDK tożsamości Microsoft robi to twarde pracy i daje możliwość doskonale dopasowanych swoich klientów za pomocą rejestracji Jednokrotnej w pakietu aplikacji lub, w przypadku naszego brokera możliwości i wystawcy uwierzytelnienia aplikacji, we wszystkich danych z urządzenia.

W tym przewodniku opisano, jak skonfigurować naszego zestawu SDK w celu zapewnienia takich korzyści klientom aplikacji.

Ten przewodnik dotyczy:

* Usługa Azure Active Directory
* Azure Active Directory B2C
* B2B usługi Azure Active Directory
* Dostęp warunkowy usługi Azure Active Directory

Poprzedniego dokumentu zakłada wiesz, jak [udostępnić aplikacje w starszej wersji portalu usługi Azure Active Directory](active-directory-how-to-integrate.md) i zintegrowanych aplikacji z [pakietu Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Pojęcia dotyczące logowania jednokrotnego platformą tożsamości Microsoft
### <a name="microsoft-identity-brokers"></a>Brokerzy tożsamość firmy Microsoft
Firma Microsoft udostępnia aplikacji dla każdej platformy przenośne, które umożliwiają łączenie poświadczeń w aplikacjach pochodzących od różnych dostawców i umożliwia specjalne udoskonalone funkcje, które wymagają pojedynczego bezpiecznym miejscu z, w którym można sprawdzić poprawności poświadczeń. Nazywamy je **brokerzy**. W systemach iOS i Android te brokerzy są realizowane za pośrednictwem aplikacji do pobrania, czy klienci zainstalować niezależnie lub może zostać umieszczony na urządzeniu przez użytkowników, którzy zarządzają niektórych lub wszystkich urządzeń dla swoich pracowników firmy. Te brokerzy obsługę zarządzania zabezpieczeń tylko niektóre aplikacje lub wszystkich danych z urządzenia oparte na potrzeby administratorzy IT. W systemie Windows ta funkcja jest dostarczana przez selektora konta z wbudowanej w system operacyjny, znane pod względem technicznym jako Broker uwierzytelniania sieci Web.

Aby uzyskać więcej informacji na temat używamy tych brokerów i jak klienci mogą je wyświetlać w ich przepływu logowania na platformy pakietu Microsoft Identity.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Wzorce do logowania na urządzeniach przenośnych
Dostęp do poświadczeń na urządzeniach wykonaj dwa podstawowe wzorce platformy pakietu Microsoft Identity:

* Logowania asystowaną bez brokera
* Broker asystowaną logowania

#### <a name="non-broker-assisted-logins"></a>Logowania asystowaną bez brokera
Inne niż brokera asystowaną logowania są środowiska logowania, być wbudowane w aplikacji, które obsługują Magazyn lokalny na urządzeniu dla tej aplikacji. Ten magazyn może być współużytkowany przez aplikacje, ale poświadczenia są ściśle powiązane z aplikacji lub pakietu aplikacji przy użyciu tego poświadczenia. W przypadku najprawdopodobniej wystąpienia to w wielu aplikacjach mobilnych podczas wprowadzania nazwy użytkownika i hasła samej aplikacji.

Te nazwy logowania są następujące korzyści:

* Środowisko użytkownika istnieje całkowicie w aplikacji.
* Poświadczenia mogą być udostępniane między aplikacjami, które są podpisane przez ten sam certyfikat, zapewniając pojedynczego środowisko logowania do pakietu aplikacji.
* Formant wokół środowisko logowania jest dostarczany do aplikacji przed i po zalogowaniu.

Te nazwy logowania ma następujące wady:

* Użytkownika nie może występować jednokrotnego we wszystkich aplikacji, które używają pakietu Microsoft Identity przez te firmy Microsoft Identities skonfigurowane w aplikacji.
* Aplikacji nie można używać z bardziej zaawansowane funkcje biznesowych, takich jak dostęp warunkowy, lub użyj produktów pakietu usługi InTune.
* Używana aplikacja nie obsługuje uwierzytelniania opartego na certyfikatach dla użytkowników biznesowych.

Oto reprezentację działanie zestawów SDK tożsamość firmy Microsoft z magazynem udostępnionym aplikacji do włączenia funkcji logowania jednokrotnego:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Broker asystowaną logowania
Asystowane brokera logowania są funkcji logowania, które występują w aplikacji brokera i korzystania z magazynu i zabezpieczeń brokera udostępnianie poświadczeń we wszystkich aplikacjach na urządzeniu, mające zastosowanie platformy pakietu Microsoft Identity. Oznacza to, że aplikacje zależne od brokera do logowania użytkowników. W systemach iOS i Android brokerzy te są realizowane za pośrednictwem aplikacji do pobrania, czy klientów zainstalować niezależnie lub może zostać umieszczony na urządzeniu przez firmę, użytkowników, którzy zarządzają urządzenia dla ich użytkownika. Przykładowe aplikacje tego typu jest aplikacja Microsoft Authenticator w systemie iOS. W systemie Windows ta funkcja jest dostarczana przez selektora konta z wbudowanej w system operacyjny, znane pod względem technicznym jako Broker uwierzytelniania sieci Web.
Środowisko jest zależna od platformy i czasami mogą być uciążliwe dla użytkowników w przeciwnym razie zarządzane prawidłowo. Znasz prawdopodobnie najbardziej tego wzorca Jeśli z zainstalowaną aplikacją Facebook i używać funkcji usługi Facebook połączenie z innej aplikacji. Platforma Microsoft Identity używa tego samego wzorca.

Dla systemu iOS, który prowadzi to do "przejścia" animacji, w którym aplikacji są wysyłane do tła podczas aplikacji Microsoft Authenticator zawiera pierwszego planu dla użytkownika wybrać konto, które chce się zalogować. 

Dla systemów Android i Windows selektora konta jest wyświetlany u góry aplikację, która jest prostszy sposób użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Sposób wywoływania pobiera brokera
Zgodne brokera jest zainstalowany na urządzeniu, takie jak aplikacja Microsoft Authenticator zestawów SDK tożsamości Microsoft będzie automatycznie wykonywać pracy wywoływania brokera automatycznie, gdy użytkownik wskazuje chcą zalogowanie się przy użyciu dowolnego konta z platformy Microsoft Identity. To konto może być Account firmy Microsoft, służbowy lub konta służbowego lub konta, które należy podać i hosta na platformie Azure przy użyciu naszych produktów B2C i B2B. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Jak firma Microsoft zapewnia aplikacji jest prawidłowy
 
 Konieczność zapewnienia tożsamości wywołanie aplikacji, które brokera odgrywa kluczową rolę zabezpieczeń, które udostępniamy w brokerze pomocy logowania. Z systemem iOS ani Android wymusza unikatowych identyfikatorów, które są prawidłowe tylko dla danej aplikacji, więc złośliwego aplikacji może "sfałszować" identyfikator uzasadnionych aplikacji i odbierać tokeny przeznaczone dla aplikacji uzasadnione. Aby upewnić się, że zawsze komunikują się firma Microsoft z prawej aplikacji w czasie wykonywania, poprosimy developer do niestandardowych redirectURI podczas rejestrowania swoich aplikacji z firmą Microsoft. **Jak deweloperzy powinien spreparować identyfikator URI przekierowania omówiono szczegółowo poniżej.** To niestandardowe redirectURI zawiera identyfikator pakietu aplikacji i zapewniony unikatowa do aplikacji w sklepie Apple App. Gdy aplikacja wywołuje brokera, broker zapyta, systemu operacyjnego z systemem iOS do tego celu o identyfikatorze pakietu, który wywołał brokera. Broker ten identyfikator pakietu do firmy Microsoft w wywołaniu naszym systemie tożsamości. Jeśli identyfikator pakietu aplikacji nie odpowiada identyfikator pakietu przekazanego nam przez projektanta podczas rejestracji, firma Microsoft będzie odmawiał żąda dostępu do tokenów dla zasobów aplikacji. Tego wyboru gwarantuje, że tylko aplikacja zarejestrowany przez dewelopera otrzyma tokenów.

**Deweloper może wybrać opcję Microsoft tożsamość SDK wywołuje brokera lub korzysta z przepływu asystowaną bez brokera.** Jednak jeśli wybierze projektanta nie należy używać przepływu wspierana brokera utracą zaletą używania poświadczeń logowania jednokrotnego, czy użytkownik może już dodane na urządzeniu i uniemożliwia ich aplikacji z używane z funkcjami firm, które firma Microsoft udostępnia jej klientów, takie jak dostęp warunkowy, możliwości zarządzania usługi Intune i uwierzytelnianie oparte na certyfikatach.

Te nazwy logowania są następujące korzyści:

* Użytkownik napotyka logowania jednokrotnego w swoich aplikacjach niezależnie od dostawcy.
* Aplikacja może użyć bardziej zaawansowane funkcje biznesowych, takich jak dostęp warunkowy lub produktów pakietu usługi InTune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bezpieczniejszym logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację broker z algorytmów zabezpieczeń i szyfrowania.

Te nazwy logowania ma następujące wady:

* W systemie iOS użytkownika jest są przenoszone poza środowisko aplikacji, a poświadczenia zostały wybrane.
* Utrata możliwości zarządzania mogą logować się klienci w aplikacji.

Oto reprezentację działanie zestawów SDK tożsamość firmy Microsoft z aplikacji brokera do włączenia funkcji logowania jednokrotnego:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Dzięki tej ogólne informacje, które powinno być możliwe do lepszego zrozumienia i implementowanie logowania jednokrotnego w aplikacji przy użyciu pakietu Microsoft Identity platformy i zestawy SDK.

## <a name="enabling-cross-app-sso-using-adal"></a>Włączanie logowania jednokrotnego wielu aplikacji za pomocą biblioteki ADAL
W tym miejscu używamy ADAL zestaw SDK dla systemu iOS do:

* Włącz brokera z systemem innym niż asystowaną pomocą rejestracji Jednokrotnej dla pakietu aplikacji
* Włączanie obsługi wspierana brokera logowania jednokrotnego

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie rejestracji Jednokrotnej z systemem innym niż brokera asystowaną pomocą rejestracji Jednokrotnej
Dla bez brokera asystowaną logowania jednokrotnego w aplikacjach zestawów SDK tożsamości Microsoft zarządzać znacznie złożoność logowania jednokrotnego dla Ciebie. Obejmuje to wyszukiwanie prawo użytkownika w pamięci podręcznej i przechowywania listy zalogowany użytkowników możesz zbadać.

Do włączenia funkcji logowania jednokrotnego w aplikacjach jesteś właścicielem, że należy wykonać następujące czynności:

1. Upewnij się użytkownika aplikacje tego samego Identyfikatora klienta lub identyfikator aplikacji.
2. Upewnij się, dzięki czemu można udostępniać pęki kluczy wszystkie aplikacje współużytkują ten sam certyfikat podpisywania firmy Apple
3. Żądanie uprawnienie łańcucha kluczy dla poszczególnych aplikacji.
4. Opisz zestawów SDK tożsamości Microsoft udostępnionego łańcucha kluczy, które chcesz nam do użycia.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Za pomocą tego samego Identyfikatora klienta / IDENTYFIKATORA aplikacji dla wszystkich aplikacji w pakietu aplikacji
Aby platformy pakietu Microsoft Identity dowiedzieć się, że mogą być udostępnianie tokenów w aplikacji należy poszczególnych aplikacji do udostępniania tego samego Identyfikatora klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został udostępniony po zarejestrowaniu swoją pierwszą aplikację w portalu.

Możesz się zastanawiać, jak należy określić różnych aplikacji z usługą Microsoft Identity korzysta z tego samego identyfikatora aplikacji Odpowiedź jest z **identyfikator URI przekierowania**. Każda aplikacja może mieć wiele identyfikator URI przekierowania zarejestrowany w portalu przy dołączaniu. Każdej aplikacji w zestawie ma inny identyfikator URI przekierowania. Jak wygląda przykład znajduje się poniżej:

Identyfikator URI przekierowania App1 `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identyfikator URI przekierowania App2 `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identyfikator URI przekierowania App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Te są zagnieżdżone w tym samym identyfikatorze klienta / identyfikator aplikacji i wyszukiwać oparte na przekierowanie URI powrócisz do nas w konfiguracji zestawu SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Należy pamiętać, że format tych identyfikator URI przekierowania opisano szczegółowo poniżej. Można użyć identyfikatora URI przekierowania, chyba że chcesz wspierać brokera, w takim przypadku one musi wyglądać powyższych*

#### <a name="create-keychain-sharing-between-applications"></a>Tworzenie łańcucha kluczy udostępnianie między aplikacjami
Włączenie udostępniania łańcucha kluczy wykracza poza zakres tego dokumentu i objęte ich dokumentu przez firmę Apple [Dodawanie możliwości](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Ważne jest zdecyduj, jakie chcesz z łańcucha kluczy ma być wywoływana i dodania tej możliwości we wszystkich aplikacjach.

Jeśli masz plik w katalogu projektu pod tytułem powinna zostać wyświetlona poprawnie można skonfigurować uprawnienia do `entitlements.plist` zawierający element, który wygląda podobnie do następującej:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Po masz uprawnienie łańcucha kluczy włączone w każdej aplikacji i wszystko jest gotowe do za pomocą logowania jednokrotnego, opisz Microsoft tożsamość SDK z łańcucha kluczy za pomocą następującego ustawienia w Twojej `ADAuthenticationSettings` z następującymi ustawieniami:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Jeśli udostępnianie łańcucha kluczy w aplikacji dowolnej aplikacji, można usunąć użytkowników lub gorsze usunąć wszystkie tokeny w aplikacji. Jest to szczególnie Fatalne, jeśli masz aplikacje korzystające z tokenów do pracy w tle. Udostępnianie łańcucha kluczy oznacza, że należy zachować ostrożność bardzo w wszystkie Usuń operacji za pomocą zestawów SDK tożsamość firmy Microsoft.
> 
> 

Gotowe. Zestaw SDK usługi Microsoft Identity teraz udostępniać poświadczeń we wszystkich aplikacjach. Lista użytkowników również będą udostępniane między wystąpieniami aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera asystowaną pomocą rejestracji Jednokrotnej
Dla aplikacji pod kątem wykorzystania brokera, wszelkie zainstalowanego na urządzeniu jest **domyślnie wyłączone**. Aby korzystać z aplikacji w brokera muszą nie dodatkowej konfiguracji i dodać kod do aplikacji.

Wykonaj kroki są:

1. Włącz tryb brokera w kodzie aplikacji wywołania MS SDK.
2. Ustanów nowy identyfikator URI przekierowania i zapewnić do rejestracji aplikacji i aplikacji.
3. Rejestrowanie schemat adresu URL.
4. Obsługa iOS9: Dodaj uprawnienia do pliku info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz broker tryb w aplikacji
Możliwość aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "context" lub początkowej konfiguracji obiektu uwierzytelniania. Można to zrobić przez ustawienie danego typu poświadczeń w kodzie:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` Ustawienie umożliwia SDK tożsamości firmy Microsoft w celu wyróżnienia do brokera, `AD_CREDENTIALS_EMBEDDED` uniemożliwi wywołania do brokera SDK tożsamość firmy Microsoft.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Zarejestrowanie schemat adresu URL
Platformy pakietu Microsoft Identity używa adresów URL wywołania brokera, a następnie wróć formantu do aplikacji. Do zakończenia tego obiegu należy zarejestrowane dla aplikacji platformy Microsoft Identity będzie wiedzieć o schemat adresów URL. Może to być oprócz innych systemów aplikacji, które może zostały wcześniej zarejestrowane z aplikacją.

> [!WARNING]
> Zalecamy utworzenie dość unikatowy, aby zminimalizować prawdopodobieństwo innej aplikacji przy użyciu tego samego schematu URL schemat adresu URL. Apple nie wymusza unikatowości Schematy adresów URL, które są zarejestrowane w sklepie z aplikacjami.
> 
> 

Poniżej przedstawiono przykład sposobu wyświetlania w konfiguracji projektu. Można również wykonać to w środowisku XCode również:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Ustalanie przekierowania nowy identyfikator URI ze schematem adresu URL
Aby zapewnić, że firma Microsoft zawsze zwracają tokeny poświadczeń do właściwej aplikacji, musimy upewnij się, że firma Microsoft wywołania zwrotnego dla aplikacji w taki sposób, aby sprawdzić systemu operacyjnego z systemem iOS. System operacyjny iOS raportów do aplikacji firmy Microsoft brokera identyfikator pakietu aplikacji podczas wywoływania go. To nie może być sfałszowane przez nieautoryzowanej aplikacji. W związku z tym możemy wykorzystać to wraz z identyfikatora URI z naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do odpowiedniej aplikacji. Wymagane do nawiązania przekierowanie Unikatowy identyfikator URI zarówno w aplikacji i Ustaw jako identyfikator URI przekierowania w portalu deweloperów.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`<app-scheme>://<your.bundle.id>`

przykład: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Ten identyfikator URI przekierowania trzeba określić za pomocą rejestracji aplikacji [portalu Azure](https://portal.azure.com/). Aby uzyskać więcej informacji dotyczących rejestracji aplikacji usługi Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Dodaj identyfikator URI przekierowania w portalu i deweloperów aplikacji do obsługi uwierzytelniania opartego na certyfikatach
Obsługa uwierzytelniania certyfikatu na podstawie drugi "msauth" musi zostać zarejestrowany w aplikacji i [portalu Azure](https://portal.azure.com/) do obsługi uwierzytelniania certyfikatów, jeśli chcesz dodać obsługę w aplikacji.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

przykład: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Krok 4: iOS9: Dodawanie parametru konfiguracji do aplikacji
— CanOpenURL używa biblioteki ADAL: Aby sprawdzić, czy broker jest zainstalowane na urządzeniu. W systemie iOS 9 Apple zablokowany co można wyszukać schematy aplikacji. Należy dodać do sekcji LSApplicationQueriesSchemes "msauth" z `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Skonfigurowaniu logowania jednokrotnego!
Teraz Microsoft tożsamość SDK automatycznie zarówno udostępnianie poświadczeń w aplikacji i wywołać brokera, jeśli jest obecny na urządzeniu.

