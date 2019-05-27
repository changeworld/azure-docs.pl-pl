---
title: Jak włączyć logowanie Jednokrotne dla wielu aplikacji, w systemie iOS za pomocą biblioteki ADAL | Dokumentacja firmy Microsoft
description: Jak włączyć logowanie jednokrotne w aplikacji za pomocą funkcji zestawu SDK biblioteki ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b010091ebd909745b272fca704bb87adf7924b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962624"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Instrukcje: Włączanie logowania jednokrotnego dla wielu aplikacji, w systemie iOS za pomocą biblioteki ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Logowanie jednokrotne (SSO) umożliwia użytkownikom tylko raz wprowadzić swoje poświadczenia i ma działać automatycznie w aplikacjach i platformach, które mogą korzystać z innych aplikacji (na przykład Accounts firmy Microsoft lub konta służbowego z usługi Microsoft 365) poświadczenia nie niezależnie od tego wydawcy.

Platforma tożsamości firmy Microsoft, wraz z zestawów SDK, można łatwo włączyć logowanie Jednokrotne w ramach własnego pakietu aplikacji lub z możliwości broker i aplikacji wystawcy uwierzytelnienia dla całego urządzenia.

W tym instruktażu dowiesz się, jak skonfigurować zestaw SDK w swojej aplikacji w celu zapewnienia logowania jednokrotnego dla klientów.

Niniejszy instruktaż mają zastosowanie do:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Dostęp warunkowy usługi Azure Active Directory

## <a name="prerequisites"></a>Wymagania wstępne

Niniejszy instruktaż przyjęto założenie, że wiesz, jak:

* Aprowizacja aplikacji przy użyciu starszej wersji portalu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [rejestrowanie aplikacji](quickstart-register-app.md)
* Integrowanie aplikacji za pomocą [zestaw SDK systemu iOS usługi Azure AD](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Pojęcia rejestracji jednokrotnej

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy urządzeń przenośnych, które umożliwiają łączenie poświadczeń między aplikacjami pochodzącymi od różnych dostawców oraz rozszerzone funkcje, które wymagają pojedynczego bezpiecznego miejsca w celu weryfikowania poświadczeń. Są to tak zwane **brokerów**.

W systemach iOS i Android brokerów są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, który zarządza niektóre lub wszystkie z urządzenia dla swoich pracowników. Brokerzy obsługę zarządzania zabezpieczeniami tylko niektóre aplikacje lub całego urządzenia na podstawie konfiguracji administratora IT. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Wzorce do zalogowania się na urządzeniach przenośnych

Dostęp do poświadczeń na urządzeniach, wykonaj dwa podstawowe wzorce:

* Broker inne niż asystowana logowania
* Asystowana logowania brokera

#### <a name="non-broker-assisted-logins"></a>Broker inne niż asystowana logowania

Broker inne niż asystowana logowania są środowiska logowania, które się tak zdarzyć tekście razem z aplikacją i korzystania z magazynów lokalnych na urządzeniu dla tej aplikacji. Ten magazyn może być współużytkowane przez aplikacje, ale poświadczenia są ściśle powiązane z aplikacji lub pakietu aplikacji przy użyciu tego poświadczenia. W przypadku najprawdopodobniej wystąpienia to w wielu aplikacjach mobilnych po wprowadzeniu nazwy użytkownika i hasła w samej aplikacji.

Te nazwy logowania mają następujące korzyści:

* Środowisko użytkownika istnieje wyłącznie w ramach aplikacji.
* Poświadczenia mogą być współużytkowane przez aplikacje, które są podpisane przez ten sam certyfikat, zapewniając funkcji logowania jednokrotnego do pakietu aplikacji.
* Kontrolki wokół środowisko logowania znajduje się w aplikacji przed i po zalogowaniu.

Te nazwy logowania mają następujące wady:

* Użytkownicy nie mogą środowiska logowania jednokrotnego na wśród wszystkich aplikacji korzystających z tożsamości firmy Microsoft, tylko w tych tożsamości firmy Microsoft, skonfigurowane przez aplikację.
* Aplikacja nie można używać z bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy lub użyj produktów pakietu usługi Intune.
* Twoja aplikacja nie obsługuje uwierzytelniania opartego na certyfikatach dla użytkowników biznesowych.

Poniżej przedstawiono reprezentację jak działają te zestawy SDK z magazynem udostępnionym aplikacji w taki sposób, aby włączyć logowanie Jednokrotne:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Asystowana logowania brokera

Logowania wspierana przez brokera są procesy logowania, które występują w ramach aplikacji brokera i korzystanie z magazynu i zabezpieczeń brokera udostępnianie poświadczeń we wszystkich aplikacjach na urządzeniu, które mają zastosowanie platformy tożsamości. Oznacza to, że aplikacji zależą od broker do logowania użytkowników. W systemach iOS i Android brokerzy są realizowane za pośrednictwem aplikacji do pobrania, klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają urządzenia dla swoich użytkowników. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.

Środowisko jest zależna od platformy i czasami może być szkodliwe dla użytkowników, jeśli nie poprawnie zarządzane. Znasz prawdopodobnie najbardziej tego wzorca Jeśli masz zainstalowaną aplikacją usługi Facebook i za pomocą usługi Facebook Connect z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

Dla systemu iOS, który prowadzi to do "przejście" animacji wysyłania aplikacji do tła podczas aplikacji Microsoft Authenticator jest dostarczany do pierwszego planu dla użytkownika wybrać konto, które chcieliby do zalogowania.

Dla systemów Android i Windows selektora konta jest wyświetlany u góry aplikacji, który jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Sposób wywoływania pobiera brokera

Jeśli zgodne broker jest zainstalowana na urządzeniu, takie jak aplikacja Microsoft Authenticator zestawy SDK zostanie automatycznie wykonywać pracę wywoływania broker dla Ciebie, gdy użytkownik wskazuje, czy chcą Zaloguj się przy użyciu dowolnego konta z platformą tożsamości. To konto może być Account osobiste Microsoft, pracy lub konta służbowego lub konta, które należy podać i host na platformie Azure za pomocą naszych produktów B2C i B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak firma Microsoft zabezpiecza aplikacji jest prawidłowy

Konieczność zapewnienia tożsamości wywołanie aplikacji, które broker ma kluczowe znaczenie dla zabezpieczeń, które udostępniamy w brokerze pomocy logowania. Z systemem iOS ani systemu Android nie wymusza unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwych aplikacji może być "podszywały się pod" identyfikator autoryzowanych aplikacji i odbierać tokeny przeznaczone dla autoryzowanych aplikacji. Aby upewnić się, że firma Microsoft zawsze komunikują się z prawej aplikacji w czasie wykonywania, poprosimy dla deweloperów do niestandardowego elementu redirectURI podczas rejestrowania swoich aplikacji z firmą Microsoft. Jak deweloperów powinien tworzyć identyfikator URI przekierowania jest szczegółowo poniżej. To niestandardowe redirectURI zawiera identyfikator pakietu aplikacji i jest zapewniana być unikatowa w aplikacji przez firmy Apple App Store. Gdy aplikacja wywołuje brokera, broker pyta, czy systemu operacyjnego iOS, aby udostępnić identyfikator pakietu, który wywołuje brokera. Broker ten identyfikator pakietu do firmy Microsoft w wywołaniu do naszego systemu tożsamości. Jeśli identyfikator pakietu aplikacji nie odpowiada identyfikator pakietu, dostarczone przez dewelopera podczas rejestracji, firma Microsoft będzie odmawiał żąda dostępu do tokenów dla zasobów aplikacji. To sprawdzenie gwarantuje, że tylko aplikacji, które zostały zarejestrowane przez dewelopera otrzyma tokenów.

**Deweloper może wybrać, czy zestaw SDK wymaga brokera korzysta z asystą usługi flow bez brokera.** Jednak jeśli deweloper zdecyduje nie należy używać przepływu korzystającej z brokera utracą zaletą przy użyciu logowania jednokrotnego poświadczenia, czy użytkownik może zostały już dodane na urządzeniu i uniemożliwia ich stosowania używany przy użyciu funkcji biznesowych, firma Microsoft udostępnia jego Klienci, takich jak dostęp warunkowy, możliwości zarządzania usługi Intune i uwierzytelniania opartego na certyfikatach.

Te nazwy logowania mają następujące korzyści:

* Usługa rejestracji Jednokrotnej podejrzewać wszystkich swoich aplikacji, niezależnie od tego dostawcy.
* Aplikacja może użyć bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy lub produktów pakietu usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bezpieczniejszego logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera przy użyciu algorytmów zabezpieczeń i szyfrowania.

Te nazwy logowania mają następujące wady:

* W systemie iOS użytkownika jest przenoszone poza środowisko użytkownika aplikacji, podczas gdy poświadczenia są wybierane.
* Utrata możliwości zarządzania logowania dla klientów w ramach aplikacji.

Poniżej przedstawiono reprezentację jak działają te zestawy SDK za pomocą aplikacji brokera, aby włączyć logowanie Jednokrotne:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Włączanie logowania jednokrotnego dla wielu aplikacji za pomocą biblioteki ADAL

W tym miejscu użyjemy biblioteki ADAL zestawu SDK dla systemu iOS do:

* Włącz bez brokera korzystającej z logowania jednokrotnego dla pakietu aplikacji
* Włączanie obsługi korzystającej z brokera logowania jednokrotnego

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla innych brokera korzystającej z logowania jednokrotnego

Bez brokera asystowanej logowania jednokrotnego w aplikacjach zestawy SDK Zarządzanie wiele trudności, ponieważ usługa rejestracji Jednokrotnej dla Ciebie. Obejmuje to znalezienie użytkownika odpowiedni w pamięci podręcznej i przechowywanie listy zalogowanych użytkowników służących do wykonywania zapytań.

Aby włączyć logowanie Jednokrotne w aplikacjach jesteś właścicielem, że należy wykonać następujące czynności:

1. Upewnij się, że wszystkie Twoje aplikacje używają tego samego Identyfikatora klienta lub identyfikator aplikacji.
2. Upewnij się, wszystkie aplikacje udostępnianie tego samego certyfikatu podpisywania firmy Apple, dzięki czemu możesz udostępniać pęki kluczy.
3. Żądanie tego samego uprawnienia pęku kluczy dla poszczególnych aplikacji.
4. Poinformuj o wspólnym łańcuchu kluczy zestawy SDK, że chcesz użyć.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Przy użyciu tego samego Identyfikatora klienta / aplikacji, identyfikator dla wszystkich aplikacji w określonym pakiecie aplikacji

Aby platforma tożsamości dowiedzieć się, że mogą być udostępnianie tokenów w aplikacjach każdej aplikacji muszą udostępniać tego samego Identyfikatora klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został podany dla Ciebie, po zarejestrowaniu swoją pierwszą aplikację w portalu.

Identyfikatory URI przekierowania umożliwia określenie różnych aplikacji do usługi Microsoft identity korzysta z tego samego identyfikatora aplikacji Każda aplikacja może mieć wiele identyfikatory URI przekierowań zarejestrowany w portalu dołączania. Każdej aplikacji w zestawie ma inny identyfikator URI przekierowania. Jak to wygląda przykład znajduje się poniżej:

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

Format tych przekierowania URI zostało wyjaśnione poniżej. Możesz użyć identyfikatora URI przekierowania, chyba że chcesz obsługiwać brokera, w którym to przypadku one musi wyglądać mniej więcej tak powyżej *

#### <a name="create-keychain-sharing-between-applications"></a>Tworzenie łańcucha kluczy, udostępniać między aplikacjami

Włączenie udostępniania pęku kluczy wykracza poza zakres tego dokumentu i są objęte przez firmę Apple w swoich dokumentach [Dodawanie możliwości](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Ważne jest zdecydować, co chcesz łańcucha kluczy do wywoływania i dodania tej możliwości we wszystkich aplikacjach.

W przypadku uprawnień skonfiguruj poprawnie powinien zostać wyświetlony plik w katalogu projektu uprawnionych `entitlements.plist` zawierający element, który wygląda podobnie do następującej:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

Po użytkownik ma uprawnienie pęku kluczy włączone we wszystkich aplikacjach i wszystko będzie gotowe do użycia logowania jednokrotnego, opisz tożsamość SDK pęku kluczy przy użyciu następujących ustawień swojej `ADAuthenticationSettings` z następującymi ustawieniami:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Podczas udostępniania łańcucha kluczy w aplikacjach dowolnej aplikacji można usunąć użytkowników lub co gorsza Usuń wszystkie tokeny wglądem w aplikację. Jest to szczególnie katastrofalne, jeśli masz aplikacje, które zależą od tokenów do pracy w tle. Udostępnianie łańcucha kluczy oznacza, że użytkownik musi być dużą ostrożność podczas wszystkie Usuń operacji za pomocą tożsamości zestawów SDK.

To wszystko! Zestaw SDK będzie teraz udostępnić poświadczeń we wszystkich aplikacjach. Lista użytkowników będzie również udostępniane między wystąpieniami aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera korzystającej z logowania jednokrotnego

Dla aplikacji, aby używać każdego brokera, która jest zainstalowana na urządzeniu jest **domyślnie wyłączona**. Aby można było używać aplikacji przy użyciu brokera, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Dostępne są następujące czynności:

1. Włącz tryb brokera w kodzie aplikacji wywołania MS SDK.
2. Ustanawia nowy identyfikator URI przekierowania i przewidzieć, że do Twojej rejestracji aplikacji i aplikacji.
3. Rejestrowanie schemat adresu URL.
4. Dodaj uprawnienia do pliku info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji

Możliwości aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "kontekstu" lub początkowej konfiguracji obiekt uwierzytelniania. Możesz to zrobić, ustawiając typu poświadczeń w kodzie:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` Ustawienie umożliwia zestawu SDK w celu wyróżnienia do brokera, `AD_CREDENTIALS_EMBEDDED` uniemożliwi zestawu SDK wywołania do brokera.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Rejestrowanie schemat adresu URL

Platforma tożsamości używa adresów URL do wywołania elementu broker, a następnie wróć kontrolki do aplikacji. Na zakończenie tej komunikacji dwustronnej należy zarejestrowany dla twojej aplikacji, która platforma tożsamości będzie wiedzieć o schemat adresów URL. Może to być oprócz innych systemów aplikacji, które mogą zostały wcześniej zarejestrowane z aplikacją.

> [!WARNING]
> Zalecamy utworzenie schemat adresu URL dość unikatowy, aby zminimalizować prawdopodobieństwo innej aplikacji przy użyciu tego samego schematu URL. Firmy Apple nie wymusza unikatowości Schematy adresów URL, które są zarejestrowane w sklepie z aplikacjami.

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Ustanowienie nowych przekierowania URI ze schematem Twojego adresu URL

Aby zapewnić, że firma Microsoft zawsze zwracają tokenów poświadczeń do właściwej aplikacji, musimy upewnić się, że firma Microsoft wywołania zwrotnego do aplikacji w taki sposób, aby sprawdzić, systemu operacyjnego iOS. Systemu operacyjnego iOS raportów do aplikacji brokera firmy Microsoft, identyfikator pakietu aplikacji, wywołując ją. To nie może być sfałszowane przez aplikacji. W związku z tym możemy wykorzystać to wraz z identyfikatora URI naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do właściwej aplikacji. Wymagane do nawiązania to unikatowy identyfikator URI zarówno przekierowania do aplikacji i ustawić jako identyfikator URI przekierowania w portalu dla deweloperów.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`<app-scheme>://<your.bundle.id>`

przykład: *msauth-x-mytestiosapp://com.myapp.mytestapp*

Przekierowanie to identyfikator URI musi być określona w rejestracji aplikacji przy użyciu [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Dodaj identyfikator URI przekierowania w swojej aplikacji i portalu dla deweloperów do obsługi uwierzytelniania opartego na certyfikatach

Drugi "msauth" muszą być zarejestrowane w Twojej aplikacji uwierzytelnianie oparte na certyfikatach pomocy technicznej i [witryny Azure portal](https://portal.azure.com/) do obsługi uwierzytelniania certyfikatu, jeśli chcesz dodać obsługę w aplikacji.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

przykład: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Krok 4: Dodawanie parametru konfiguracji do aplikacji

— CanOpenURL korzysta z biblioteki ADAL: aby Sprawdź, czy broker jest zainstalowana na urządzeniu. W systemie iOS 9 na Apple zablokowany co można wyszukiwać schematów aplikacji. Należy dodać "msauth" w sekcji LSApplicationQueriesSchemes swoje `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Skonfigurowano logowania jednokrotnego!

Teraz tożsamości zestawu SDK zostanie automatycznie udostępniać poświadczenia w aplikacjach i wywołania brokera, jeśli jest obecny na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [pojedynczego logowania jednokrotnego protokołu SAML](single-sign-on-saml-protocol.md)
