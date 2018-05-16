---
title: Jak włączyć logowanie Jednokrotne wielu aplikacji w systemie Android przy użyciu biblioteki ADAL | Dokumentacja firmy Microsoft
description: 'Jak używać funkcji ADAL zestawu SDK do Włącz rejestrację jednokrotną w aplikacji. '
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/07/2017
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: ee27b1d4dcc091ef570e2b0aef32273af986d3f9
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Jak włączyć logowanie Jednokrotne wielu aplikacji w systemie Android przy użyciu biblioteki ADAL
Zapewniające pojedynczego logowania jednokrotnego (SSO), aby użytkownicy potrzebują tylko może wprowadzić swoje poświadczenia raz, a te poświadczenia automatycznie działać przez aplikacje teraz jest oczekiwany przez klientów. Trudności przy wprowadzaniu swoją nazwę użytkownika i hasło na małego ekranu, często razy łączyć się przy użyciu dodatkowego składnika (2FA), takich jak rozmowa telefoniczna lub kod wysłana wiadomość SMS, powoduje niezadowolenie szybki, jeśli użytkownik ma w tym celu więcej niż jeden raz na produkt.

Ponadto w przypadku zastosowania platformą tożsamości, które inne aplikacje mogą używać takich jak Accounts Microsoft lub konta służbowego z usługi Office 365, klienci oczekują, że te poświadczenia były dostępne do użycia w swoich aplikacjach niezależnie od dostawcy.

Platformy pakietu Microsoft Identity, wraz z nasze zestawy SDK tożsamości Microsoft robi to twarde pracy i daje możliwość doskonale dopasowanych swoich klientów za pomocą rejestracji Jednokrotnej w pakietu aplikacji lub, w przypadku naszego brokera możliwości i wystawcy uwierzytelnienia aplikacji, we wszystkich danych z urządzenia.

W tym przewodniku opisano, jak skonfigurować naszego zestawu SDK w celu zapewnienia takich korzyści klientom aplikacji.

Ten przewodnik dotyczy:

* Usługa Azure Active Directory
* Azure Active Directory B2C
* B2B usługi Azure Active Directory
* Dostęp warunkowy usługi Azure Active Directory

Poprzedniego dokumentu zakłada wiesz, jak [udostępnić aplikacje w starszej wersji portalu usługi Azure Active Directory](active-directory-how-to-integrate.md) i zintegrowanych aplikacji z [zestawu SDK systemu Android tożsamości Microsoft](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Pojęcia dotyczące logowania jednokrotnego platformą tożsamości Microsoft
### <a name="microsoft-identity-brokers"></a>Brokerzy tożsamość firmy Microsoft
Firma Microsoft udostępnia aplikacji dla każdej platformy przenośne, które umożliwiają łączenie poświadczeń w aplikacjach pochodzących od różnych dostawców i umożliwia specjalne udoskonalone funkcje, które wymagają pojedynczego bezpiecznym miejscu z, w którym można sprawdzić poprawności poświadczeń. Nazywamy je **brokerzy**. W systemach iOS i Android te są realizowane za pośrednictwem aplikacji do pobrania czy klientów zainstalować niezależnie lub może zostać umieszczony na urządzeniu przez użytkowników, którzy zarządzają niektórych lub wszystkich urządzeń dla swoich pracowników firmy. Te brokerzy obsługę zarządzania zabezpieczeń tylko niektóre aplikacje lub wszystkich danych z urządzenia oparte na potrzeby administratorzy IT. W systemie Windows ta funkcja jest dostarczana przez selektora konta z wbudowanej w system operacyjny, znane pod względem technicznym jako Broker uwierzytelniania sieci Web.

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
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
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
 
 Konieczność zapewnienia tożsamości wywołanie aplikacji, które brokera odgrywa kluczową rolę zabezpieczeń, które udostępniamy w brokerze pomocy logowania. Z systemem iOS ani Android wymusza unikatowych identyfikatorów, które są prawidłowe tylko dla danej aplikacji, więc złośliwego aplikacji może "sfałszować" identyfikator uzasadnionych aplikacji i odbierać tokeny przeznaczone dla aplikacji uzasadnione. Aby upewnić się, że zawsze komunikują się firma Microsoft z prawej aplikacji w czasie wykonywania, poprosimy developer do niestandardowych redirectURI podczas rejestrowania swoich aplikacji z firmą Microsoft. **Jak deweloperzy powinien spreparować identyfikator URI przekierowania omówiono szczegółowo poniżej.** To niestandardowe redirectURI zawiera odcisk palca certyfikatu, aplikacji i zapewniony unikatowa do aplikacji w sklepie Google Play. Gdy aplikacja wywołuje brokera, broker zapyta, systemu operacyjnego Android zapewnienie o odcisk palca certyfikatu, który wywołał brokera. Broker taki odcisk palca certyfikatu do firmy Microsoft w wywołaniu naszym systemie tożsamości. Jeśli odcisk palca certyfikatu aplikacji nie odpowiada odcisk palca certyfikatu przekazanego nam przez projektanta podczas rejestracji, firma Microsoft będzie odmawiał dostępu na tokeny dla zasobu, który żąda aplikacji. Tego wyboru gwarantuje, że tylko aplikacja zarejestrowany przez dewelopera otrzyma tokenów.

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
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
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
W tym miejscu używamy zestawu SDK systemu Android biblioteki ADAL do:

* Włącz brokera z systemem innym niż asystowaną pomocą rejestracji Jednokrotnej dla pakietu aplikacji
* Włączanie obsługi wspierana brokera logowania jednokrotnego

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie rejestracji Jednokrotnej z systemem innym niż brokera asystowaną pomocą rejestracji Jednokrotnej
Dla bez brokera asystowaną logowania jednokrotnego w aplikacjach zestawów SDK tożsamości Microsoft zarządzać znacznie złożoność logowania jednokrotnego dla Ciebie. Obejmuje to wyszukiwanie prawo użytkownika w pamięci podręcznej i przechowywania listy zalogowany użytkowników możesz zbadać.

Do włączenia funkcji logowania jednokrotnego w aplikacjach jesteś właścicielem, że należy wykonać następujące czynności:

1. Upewnij się użytkownika aplikacje tego samego Identyfikatora klienta lub identyfikator aplikacji.
2. Upewnij się, że wszystkie aplikacje mają ten sam zestaw SharedUserID.
3. Upewnij się, dzięki czemu można udostępniać magazynu wszystkie aplikacje współużytkują ten sam certyfikat podpisywania z magazynu Google Play.

#### <a name="step-1-using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Krok 1: Używa tego samego Identyfikatora klienta / IDENTYFIKATORA aplikacji dla wszystkich aplikacji w pakietu aplikacji
Aby platformy pakietu Microsoft Identity dowiedzieć się, że mogą być udostępnianie tokenów w aplikacji należy poszczególnych aplikacji do udostępniania tego samego Identyfikatora klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został udostępniony po zarejestrowaniu swoją pierwszą aplikację w portalu.

Możesz się zastanawiać, jak należy określić różnych aplikacji z usługą Microsoft Identity korzysta z tego samego identyfikatora aplikacji Odpowiedź jest z **identyfikator URI przekierowania**. Każda aplikacja może mieć wiele identyfikator URI przekierowania zarejestrowany w portalu przy dołączaniu. Każdej aplikacji w zestawie ma inny identyfikator URI przekierowania. Jak wygląda przykład znajduje się poniżej:

Identyfikator URI przekierowania App1 `msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D`

Identyfikator URI przekierowania App2 `msauth://com.example.userapp1/KmB7PxIytyLkbGHuI%2UitkW%2Fejk%4E`

Identyfikator URI przekierowania App3: `msauth://com.example.userapp2/Pt85PxIyvbLkbKUtBI%2SitkW%2Fejk%9F`

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

#### <a name="step-2-configuring-shared-storage-in-android"></a>Krok 2: Konfigurowanie magazynu udostępnionego w systemie Android
Ustawienie `SharedUserID` wykracza poza zakres tego dokumentu, ale może być rozpoznawane przez odczytanie w dokumentacji systemu Google Android na [manifestu](http://developer.android.com/guide/topics/manifest/manifest-element.html). Ważne jest określenie mają sharedUserID Twojego zostanie wywołana i używać go do wszystkich aplikacji sieci.

Po utworzeniu `SharedUserID` w aplikacjach użytkownika można przystąpić do za pomocą logowania jednokrotnego.

> [!WARNING]
> Po udostępnieniu magazynu przez aplikacje dowolnej aplikacji, można usunąć użytkowników lub gorsze usunąć wszystkie tokeny w aplikacji. Jest to szczególnie Fatalne, jeśli masz aplikacje korzystające z tokenów do pracy w tle. Udostępnianie magazynu oznacza, że należy zachować ostrożność bardzo w operacji Usuń wszystkie za pomocą zestawów SDK tożsamość firmy Microsoft.
> 
> 

Gotowe. Zestaw SDK usługi Microsoft Identity teraz udostępniać poświadczeń we wszystkich aplikacjach. Lista użytkowników również będą udostępniane między wystąpieniami aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera asystowaną pomocą rejestracji Jednokrotnej
Dla aplikacji pod kątem wykorzystania brokera, wszelkie zainstalowanego na urządzeniu jest **domyślnie wyłączone**. Aby korzystać z aplikacji w brokera muszą nie dodatkowej konfiguracji i dodać kod do aplikacji.

Wykonaj kroki są:

1. Włącz tryb brokera w kodzie aplikacji wywołania MS SDK
2. Ustanów nowy identyfikator URI przekierowania i zapewnić do rejestracji aplikacji i aplikacji
3. Konfigurowanie odpowiednich uprawnień w manifestu systemu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz broker tryb w aplikacji
Możliwość aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "ustawienia" lub początkowej konfiguracji wystąpienia uwierzytelniania. Można to zrobić przez ustawienie danego typu ApplicationSettings w kodzie:

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Ustanowić nowe przekierowania identyfikatorem URI ze schematem adresu URL
Aby zapewnić, że firma Microsoft zawsze zwracają tokeny poświadczeń do właściwej aplikacji, musimy upewnij się, że firma Microsoft wywołania zwrotnego dla aplikacji w taki sposób, aby sprawdzić, systemu operacyjnego Android. System operacyjny Android używa skrót certyfikatu w sklepie Google Play. To nie może być sfałszowane przez nieautoryzowanej aplikacji. W związku z tym możemy wykorzystać to wraz z identyfikatora URI z naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do odpowiedniej aplikacji. Wymagane do nawiązania przekierowanie Unikatowy identyfikator URI zarówno w aplikacji i Ustaw jako identyfikator URI przekierowania w portalu deweloperów.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`msauth://packagename/Base64UrlencodedSignature`

przykład: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ten identyfikator URI przekierowania trzeba określić za pomocą rejestracji aplikacji [portalu Azure](https://portal.azure.com/). Aby uzyskać więcej informacji dotyczących rejestracji aplikacji usługi Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Konfigurowanie odpowiednich uprawnień w aplikacji
Broker aplikacji w systemie Android korzysta z funkcji Menedżera kont systemu operacyjnego Android do zarządzania poświadczeniami w aplikacjach. Aby można było używać brokera w systemie Android manifest aplikacji musi mieć uprawnienia do korzystania z kont elementu AccountManager. To jest omówiona szczegółowo w [Google dokumentację Menedżera kont w tym miejscu](http://developer.android.com/reference/android/accounts/AccountManager.html)

W szczególności te uprawnienia są:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Skonfigurowaniu logowania jednokrotnego!
Teraz Microsoft tożsamość SDK automatycznie zarówno udostępnianie poświadczeń w aplikacji i wywołać brokera, jeśli jest obecny na urządzeniu.

