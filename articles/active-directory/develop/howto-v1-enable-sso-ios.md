---
title: Jak włączyć logowanie Jednokrotne dla wielu aplikacji, w systemie iOS za pomocą biblioteki ADAL | Dokumentacja firmy Microsoft
description: 'Jak włączyć logowanie jednokrotne w aplikacji za pomocą funkcji zestawu SDK biblioteki ADAL. '
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
ms.openlocfilehash: 203cb4f57cfa50a17b66a9b70a44568e57ec4835
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601976"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Jak włączyć logowanie Jednokrotne dla wielu aplikacji, w systemie iOS za pomocą biblioteki ADAL

Realizacji pojedynczego logowania jednokrotnego (SSO), aby użytkownicy potrzebują tylko raz wprowadzić swoje poświadczenia i te poświadczenia automatycznie pracy w aplikacji teraz jest oczekiwany przez klientów. Trudności podczas wprowadzania nazwy użytkownika i hasła na małym ekranie, często razy w połączeniu z dodatkowy czynnik (2FA), takich jak rozmowa telefoniczna lub dzwonić kodu powoduje przestoje szybki, jeśli użytkownik ma w tym celu więcej niż jeden raz dla swojego produktu.

Ponadto jeśli zastosujesz platforma tożsamości, która może używać innych aplikacji, takich jak Accounts firmy Microsoft lub konta służbowego z usługi Office 365, klienci oczekują, że tych poświadczeń użytkownika mają być dostępne do użycia w swoich aplikacjach niezależnie od tego dostawcy.

Platforma Microsoft Identity, wraz z naszych zestawów SDK tożsamości firmy Microsoft zrobi to trudną pracę za Ciebie i daje możliwość klienci będą zachwyceni za pomocą logowania jednokrotnego znajdującego się w obrębie pakietu aplikacji lub, podobnie jak w przypadku naszej możliwości broker i wystawcy uwierzytelnienia aplikacje i całego urządzenia.

W tym przewodniku opisano, jak skonfigurować nasz zestaw SDK w ramach aplikacji w taki sposób, aby zapewnić te korzyści klientom.

W tym przewodniku mają zastosowanie do:

* Usługa Azure Active Directory
* Azure Active Directory B2C
* Usługa Azure Active Directory B2B
* Dostęp warunkowy usługi Azure Active Directory

Poprzedni dokument zakłada, wiesz, jak [aprowizowanie aplikacji w starszej wersji portalu usługi Azure Active Directory](active-directory-how-to-integrate.md) i zintegrowanych aplikacji za pomocą [zestaw SDK systemu iOS Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Pojęcia dotyczące rejestracji Jednokrotnej platforma tożsamości firmy Microsoft

### <a name="microsoft-identity-brokers"></a>Brokerzy tożsamości firmy Microsoft

Firma Microsoft udostępnia aplikacje dla każdej platformy urządzeń przenośnych, które umożliwiają łączenie poświadczeń między aplikacjami pochodzącymi od różnych dostawców i umożliwia specjalnych zaawansowanych funkcji, które wymagają pojedynczego bezpiecznego miejsca w celu weryfikowania poświadczeń. Nazywamy je **brokerów**. W systemach iOS i Android brokerzy są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają niektóre lub wszystkie urządzenia dla swoich pracowników. Brokerzy obsługę zarządzania zabezpieczeniami tylko niektóre aplikacje lub całego urządzenia, które są oparte na co pozwalają administratorom IT. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.

Czytaj dalej, aby uzyskać więcej informacji o jak używamy brokerzy i jak klienci mogą zobaczyć je w ich przepływu logowania dla platformy Microsoft Identity.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Wzorce do zalogowania się na urządzeniach przenośnych

Dostęp do poświadczeń na urządzeniach, wykonaj dwa podstawowe wzorce dla platformy Microsoft Identity:

* Broker inne niż asystowana logowania
* Asystowana logowania brokera

#### <a name="non-broker-assisted-logins"></a>Broker inne niż asystowana logowania

Broker inne niż asystowana logowania są środowiska logowania, które się tak zdarzyć tekście razem z aplikacją i korzystania z magazynów lokalnych na urządzeniu dla tej aplikacji. Ten magazyn może być współużytkowane przez aplikacje, ale poświadczenia są ściśle powiązane z aplikacji lub pakietu aplikacji przy użyciu tego poświadczenia. W przypadku najprawdopodobniej wystąpienia to w wielu aplikacjach mobilnych po wprowadzeniu nazwy użytkownika i hasła w samej aplikacji.

Te nazwy logowania mają następujące korzyści:

* Środowisko użytkownika istnieje wyłącznie w ramach aplikacji.
* Poświadczenia mogą być współużytkowane przez aplikacje, które są podpisane przez ten sam certyfikat, zapewniając funkcji logowania jednokrotnego do pakietu aplikacji.
* Kontrolki wokół środowisko logowania znajduje się w aplikacji przed i po zalogowaniu.

Te nazwy logowania mają następujące wady:

* Logowania jednokrotnego dla wszystkich aplikacji korzystających z Microsoft Identity, tylko w tych Microsoft Identities skonfigurowanych aplikacji nie środowiska użytkownika.
* Aplikacja nie można używać z bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy lub użyj produktów pakietu usługi Intune.
* Twoja aplikacja nie obsługuje uwierzytelniania opartego na certyfikatach dla użytkowników biznesowych.

Poniżej przedstawiono reprezentację działania zestawów SDK tożsamości firmy Microsoft z magazynem udostępnionym aplikacji w taki sposób, aby włączyć logowanie Jednokrotne:

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

#### <a name="broker-assisted-logins"></a>Asystowana logowania brokera

Logowania wspierana przez brokera są środowiska logowania, które wystąpiły w ramach aplikacji brokera i korzystanie z magazynu i zabezpieczeń brokera udostępnianie poświadczeń we wszystkich aplikacjach na urządzeniu, które są stosowane z platformą Microsoft Identity. Oznacza to, że aplikacji zależą od broker do logowania użytkowników. W systemach iOS i Android brokerzy są realizowane za pośrednictwem aplikacji do pobrania, klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają urządzenia dla swoich użytkowników. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.
Środowisko jest zależna od platformy i czasami może być szkodliwe dla użytkowników, jeśli nie poprawnie zarządzane. Znasz prawdopodobnie najbardziej tego wzorca Jeśli masz zainstalowaną aplikacją usługi Facebook i za pomocą usługi Facebook Connect z innej aplikacji. Platforma Microsoft Identity korzysta z tego samego wzorca.

Dla systemu iOS, który prowadzi to do "przejście" animacji wysyłania aplikacji do tła podczas aplikacji Microsoft Authenticator jest dostarczany do pierwszego planu dla użytkownika wybrać konto, które chcieliby do zalogowania. 

Dla systemów Android i Windows selektora konta jest wyświetlany u góry aplikacji, który jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Sposób wywoływania pobiera brokera

Jeśli zgodne broker jest zainstalowana na urządzeniu, takie jak aplikacja Microsoft Authenticator zestawami SDK Microsoft Identity automatycznie będzie wykonywać pracę wywoływania broker dla Ciebie, gdy użytkownik wskazuje, że chcą Zaloguj się przy użyciu dowolnego konta firmy Microsoft Platforma tożsamości usługi. To konto może być Account osobiste Microsoft, pracy lub konta służbowego lub konta, które należy podać i host na platformie Azure za pomocą naszych produktów B2C i B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak firma Microsoft zabezpiecza aplikacji jest prawidłowy
 
 Konieczność zapewnienia tożsamości wywołanie aplikacji, które broker ma kluczowe znaczenie dla zabezpieczeń, które udostępniamy w brokerze pomocy logowania. Z systemem iOS ani systemu Android nie wymusza unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwych aplikacji może być "podszywały się pod" identyfikator autoryzowanych aplikacji i odbierać tokeny przeznaczone dla autoryzowanych aplikacji. Aby upewnić się, że firma Microsoft zawsze komunikują się z prawej aplikacji w czasie wykonywania, poprosimy dla deweloperów do niestandardowego elementu redirectURI podczas rejestrowania swoich aplikacji z firmą Microsoft. **Jak deweloperów powinien tworzyć identyfikator URI przekierowania jest szczegółowo poniżej.** To niestandardowe redirectURI zawiera identyfikator pakietu aplikacji i jest zapewniana być unikatowa w aplikacji przez firmy Apple App Store. Gdy aplikacja wywołuje brokera, broker pyta, czy systemu operacyjnego iOS, aby udostępnić identyfikator pakietu, który wywołuje brokera. Broker ten identyfikator pakietu do firmy Microsoft w wywołaniu do naszego systemu tożsamości. Jeśli identyfikator pakietu aplikacji nie odpowiada identyfikator pakietu, dostarczone przez dewelopera podczas rejestracji, firma Microsoft będzie odmawiał żąda dostępu do tokenów dla zasobów aplikacji. To sprawdzenie gwarantuje, że tylko aplikacji, które zostały zarejestrowane przez dewelopera otrzyma tokenów.

**Deweloper może wybrać opcję, jeśli zestaw SDK programu Microsoft Identity wywołuje brokera lub korzysta z asystą usługi flow bez brokera.** Jednak jeśli deweloper zdecyduje nie należy używać przepływu korzystającej z brokera utracą zaletą przy użyciu logowania jednokrotnego poświadczenia, czy użytkownik może zostały już dodane na urządzeniu i uniemożliwia ich stosowania używany przy użyciu funkcji biznesowych, firma Microsoft udostępnia jego Klienci, takich jak dostęp warunkowy, możliwości zarządzania w usłudze Intune i uwierzytelniania opartego na certyfikatach.

Te nazwy logowania mają następujące korzyści:

* Usługa rejestracji Jednokrotnej podejrzewać wszystkich swoich aplikacji, niezależnie od tego dostawcy.
* Aplikacja może użyć bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy lub produktów pakietu usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bezpieczniejszego logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera przy użyciu algorytmów zabezpieczeń i szyfrowania.

Te nazwy logowania mają następujące wady:

* W systemie iOS użytkownika jest przenoszone poza środowisko użytkownika aplikacji, podczas gdy poświadczenia są wybierane.
* Utrata możliwości zarządzania logowania dla klientów w ramach aplikacji.

Poniżej przedstawiono reprezentację działania zestawami SDK Microsoft tożsamości za pomocą aplikacji brokera, aby włączyć logowanie Jednokrotne:

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

Korzystając z tego informacje uzupełniające, które powinno być możliwe do lepszego zrozumienia i zaimplementowania logowania jednokrotnego w aplikacji przy użyciu zestawów SDK i platformą Microsoft Identity.

## <a name="enabling-cross-app-sso-using-adal"></a>Włączanie logowania jednokrotnego dla wielu aplikacji za pomocą biblioteki ADAL

W tym miejscu użyjemy biblioteki ADAL zestawu SDK dla systemu iOS do:

* Włącz bez brokera korzystającej z logowania jednokrotnego dla pakietu aplikacji
* Włączanie obsługi korzystającej z brokera logowania jednokrotnego

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla innych brokera korzystającej z logowania jednokrotnego

Bez brokera asystowanej logowania jednokrotnego w aplikacjach zestawami SDK Microsoft Identity Zarządzanie wiele trudności, ponieważ usługa rejestracji Jednokrotnej dla Ciebie. Obejmuje to znalezienie użytkownika odpowiedni w pamięci podręcznej i przechowywanie listy zalogowanych użytkowników służących do wykonywania zapytań.

Aby włączyć logowanie Jednokrotne w aplikacjach jesteś właścicielem, że należy wykonać następujące czynności:

1. Upewnij się, użytkownik aplikacji tego samego Identyfikatora klienta lub identyfikator aplikacji.
2. Upewnij się, wszystkie aplikacje udostępnianie tego samego certyfikatu podpisywania firmy Apple, dzięki czemu możesz udostępniać pęki kluczy
3. Żądanie tego samego uprawnienia pęku kluczy dla poszczególnych aplikacji.
4. Poinformuj o wspólnym łańcuchu kluczy zestawami SDK Microsoft Identity, czy chcesz użyć.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Przy użyciu tego samego Identyfikatora klienta / aplikacji, identyfikator dla wszystkich aplikacji w określonym pakiecie aplikacji

Aby platformą Microsoft Identity dowiedzieć się, że mogą być udostępnianie tokenów w aplikacjach każdej aplikacji należy udostępniać tego samego Identyfikatora klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został podany dla Ciebie, po zarejestrowaniu swoją pierwszą aplikację w portalu.

Możesz się zastanawiać, jak należy określić różne aplikacje do usługi Microsoft Identity korzysta z tego samego identyfikatora aplikacji Odpowiedź brzmi z **identyfikatory URI przekierowań**. Każda aplikacja może mieć wiele identyfikatory URI przekierowań zarejestrowany w portalu dołączania. Każdej aplikacji w zestawie ma inny identyfikator URI przekierowania. Jak to wygląda przykład znajduje się poniżej:

Identyfikator URI przekierowania komputera App1 `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identyfikator URI przekierowania App2 `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identyfikator URI przekierowania App3 `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Te zostały zagnieżdżone w tym samym identyfikatorze klienta / identyfikator aplikacji i wyszukiwać oparciu o przekierowania URI powrócisz do nas w konfiguracji zestawu SDK.

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


*Należy pamiętać, że format te identyfikatory URI przekierowań zostało wyjaśnione poniżej. Możesz użyć identyfikatora URI przekierowania, chyba że chcesz obsługiwać brokera, w którym to przypadku one musi wyglądać mniej więcej tak powyżej*

#### <a name="create-keychain-sharing-between-applications"></a>Tworzenie łańcucha kluczy, udostępniać między aplikacjami

Włączenie udostępniania pęku kluczy wykracza poza zakres tego dokumentu i są objęte przez firmę Apple w swoich dokumentach [Dodawanie możliwości](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Ważne jest zdecydować, co chcesz łańcucha kluczy do wywoływania i dodania tej możliwości we wszystkich aplikacjach.

W przypadku uprawnień skonfiguruj poprawnie powinien zostać wyświetlony plik w katalogu projektu uprawnionych `entitlements.plist` zawierający element, który wygląda podobnie do następującej:

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

Gdy masz uprawnienie pęku kluczy włączone we wszystkich aplikacjach i wszystko będzie gotowe do użycia logowania jednokrotnego, opisz zestaw SDK programu Microsoft Identity pęku kluczy przy użyciu następujących ustawień w swojej `ADAuthenticationSettings` z następującymi ustawieniami:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Podczas udostępniania łańcucha kluczy w aplikacjach dowolnej aplikacji można usunąć użytkowników lub co gorsza Usuń wszystkie tokeny wglądem w aplikację. Jest to szczególnie katastrofalne, jeśli masz aplikacje, które zależą od tokenów do pracy w tle. Udostępnianie łańcucha kluczy oznacza, że użytkownik musi być dużą ostrożność podczas wszystkie Usuń operacji za pomocą zestawów SDK tożsamości firmy Microsoft.

Gotowe. Zestaw SDK programu Microsoft Identity będzie teraz udostępnić poświadczeń we wszystkich aplikacjach. Lista użytkowników będzie również udostępniane między wystąpieniami aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera korzystającej z logowania jednokrotnego

Dla aplikacji, aby używać każdego brokera, która jest zainstalowana na urządzeniu jest **domyślnie wyłączona**. Aby można było używać aplikacji przy użyciu brokera, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Dostępne są następujące czynności:

1. Włącz tryb brokera w kodzie aplikacji wywołania MS SDK.
2. Ustanawia nowy identyfikator URI przekierowania i przewidzieć, że do Twojej rejestracji aplikacji i aplikacji.
3. Rejestrowanie schemat adresu URL.
4. Obsługa iOS9: Dodaj uprawnienia do pliku info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji

Możliwości aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "kontekstu" lub początkowej konfiguracji obiekt uwierzytelniania. Możesz to zrobić, ustawiając typu poświadczeń w kodzie:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` Ustawienie umożliwia SDK tożsamości firmy Microsoft w celu wyróżnienia do brokera, `AD_CREDENTIALS_EMBEDDED` uniemożliwi zestaw SDK programu Microsoft Identity wywołania do brokera.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Zarejestrowanie schemat adresu URL

Platforma Microsoft Identity korzysta adresy URL wywołuje broker i następnie wrócić kontrolki do aplikacji. Na zakończenie tej komunikacji dwustronnej należy zarejestrować aplikacji z platformą Microsoft Identity będzie wiedzieć o schemat adresów URL. Może to być oprócz innych systemów aplikacji, które mogą zostały wcześniej zarejestrowane z aplikacją.

> [!WARNING]
> Zalecamy utworzenie schemat adresu URL dość unikatowy, aby zminimalizować prawdopodobieństwo innej aplikacji przy użyciu tego samego schematu URL. Firmy Apple nie wymusza unikatowości Schematy adresów URL, które są zarejestrowane w sklepie z aplikacjami.
> 
> 

Poniżej przedstawiono przykładowy sposób wyświetlania w konfiguracji projektu. Można również wykonać to w środowisku XCode także:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Ustanawiania nowego identyfikatora URI z schemat adresu URL przekierowania

Aby zapewnić, że firma Microsoft zawsze zwracają tokenów poświadczeń do właściwej aplikacji, musimy upewnić się, że firma Microsoft wywołania zwrotnego do aplikacji w taki sposób, aby sprawdzić, systemu operacyjnego iOS. Systemu operacyjnego iOS raportów do aplikacji brokera firmy Microsoft, identyfikator pakietu aplikacji, wywołując ją. To nie może być sfałszowane przez aplikacji. W związku z tym możemy wykorzystać to wraz z identyfikatora URI naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do właściwej aplikacji. Wymagane do nawiązania to unikatowy identyfikator URI zarówno przekierowania do aplikacji i ustawić jako identyfikator URI przekierowania w portalu dla deweloperów.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`<app-scheme>://<your.bundle.id>`

przykład: *msauth-x-mytestiosapp://com.myapp.mytestapp*

Ten identyfikator URI przekierowania musi być określona w rejestracji aplikacji przy użyciu [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Dodaj identyfikator URI przekierowania w swojej aplikacji i portalu dla deweloperów do obsługi uwierzytelniania opartego na certyfikatach

Drugi "msauth" muszą być zarejestrowane w Twojej aplikacji uwierzytelnianie oparte na certyfikatach pomocy technicznej i [witryny Azure portal](https://portal.azure.com/) do obsługi uwierzytelniania certyfikatu, jeśli chcesz dodać obsługę w aplikacji.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

przykład: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Krok 4: iOS9: Dodawanie parametru konfiguracji do aplikacji

— CanOpenURL korzysta z biblioteki ADAL: aby Sprawdź, czy broker jest zainstalowana na urządzeniu. W systemie iOS 9 firmy Apple zablokowany co można wyszukiwać schematów aplikacji. Należy dodać "msauth" w sekcji LSApplicationQueriesSchemes swoje `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Skonfigurowano logowania jednokrotnego!

Teraz zestaw SDK programu Microsoft Identity będzie automatycznie udostępniać poświadczenia w aplikacjach i wywołać brokera, jeśli jest obecny na urządzeniu.