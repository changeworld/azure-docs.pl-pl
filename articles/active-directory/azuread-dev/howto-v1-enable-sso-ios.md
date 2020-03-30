---
title: Jak włączyć wielokomonika SSO w uos przy użyciu ADAL | Dokumenty firmy Microsoft
description: Jak korzystać z funkcji SDK ADAL, aby włączyć logowanie jednokrotne w aplikacjach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154784"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Jak: Włączanie wieloznakowego identyfikatora SSO w uos przy użyciu usługi ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Logowanie jednokrotne (Logowanie jednokrotne) umożliwia użytkownikom wprowadzanie poświadczeń tylko raz i automatyczne uruchamianie tych poświadczeń między aplikacjami i na różnych platformach, z których mogą korzystać inne aplikacje (takie jak konta Microsoft lub konto służbowe z usługi Microsoft 365) nie wydawcy.

Platforma tożsamości firmy Microsoft wraz z zestawami SDK ułatwia włączenie usługi SSO w ramach własnego pakietu aplikacji lub z możliwością brokera i aplikacjami Authenticator na całym urządzeniu.

W tym instrukcje dowiesz się, jak skonfigurować SDK w aplikacji, aby zapewnić klientom, aby zapewnić klientom.

Ten sposób dotyczy:

* Usługa Azure Active Directory (usługa Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory dostępu warunkowego

## <a name="prerequisites"></a>Wymagania wstępne

W tym sposób założono, że wiesz, jak:

* Aprowizuj aplikację przy użyciu starszego portalu dla usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Zintegruj aplikację z zestawem [SDK usługi Azure AD iOS.](https://github.com/AzureAD/azure-activedirectory-library-for-objc)

## <a name="single-sign-on-concepts"></a>Pojęcia logowania jednokrotnego

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy mobilnej, które umożliwiają łączenie poświadczeń między aplikacjami od różnych dostawców i rozszerzone funkcje, które wymagają jednego bezpiecznego miejsca, z którego można sprawdzić poprawność poświadczeń. Są to tak zwane **brokerzy**.

W systemach iOS i Android brokerzy są udostępniani za pośrednictwem aplikacji do pobrania, które klienci instalują niezależnie lub są wypychani do urządzenia przez firmę, która zarządza niektórymi lub wszystkimi urządzeniami dla swoich pracowników. Brokerzy obsługują zarządzanie zabezpieczeniami tylko dla niektórych aplikacji lub całego urządzenia na podstawie konfiguracji administratora IT. W systemie Windows ta funkcja jest dostarczana przez wybieracz kont wbudowany w system operacyjny, znany technicznie jako Broker uwierzytelniania sieci Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Wzorce logowania na urządzeniach mobilnych

Dostęp do poświadczeń na urządzeniach przebiega według dwóch podstawowych wzorców:

* Logowania ze wspomaganym pomocą non-broker
* Logowania wspomagane brokerem

#### <a name="non-broker-assisted-logins"></a>Logowania ze wspomaganym pomocą non-broker

Nie-broker wspomagane logowania są środowiska logowania, które zdarzają się w linii z aplikacją i używać magazynu lokalnego na urządzeniu dla tej aplikacji. Ten magazyn może być współużytkowane przez aplikacje, ale poświadczenia są ściśle powiązane z aplikacją lub pakietem aplikacji przy użyciu tego poświadczenia. Najprawdopodobniej doświadczyłeś tego w wielu aplikacjach mobilnych po wprowadzeniu nazwy użytkownika i hasła w samej aplikacji.

Te logowania mają następujące zalety:

* Środowisko użytkownika istnieje całkowicie w aplikacji.
* Poświadczenia mogą być współużytkowane przez aplikacje, które są podpisane przez ten sam certyfikat, zapewniając środowisko logowania jednokrotnego do pakietu aplikacji.
* Kontrola wokół doświadczenia logowania jest dostarczana do aplikacji przed i po zalogowaniu.

Te logowania mają następujące wady:

* Użytkownicy nie mogą wystąpić logowanie jednokrotne we wszystkich aplikacjach, które używają tożsamości firmy Microsoft, tylko w tych tożsamościach firmy Microsoft, które aplikacja skonfigurowała.
* Aplikacji nie można używać z bardziej zaawansowanymi funkcjami biznesowymi, takimi jak dostęp warunkowy, ani z pakietem produktów usługi Intune.
* Aplikacja nie obsługuje uwierzytelniania opartego na certyfikatach dla użytkowników biznesowych.

Oto reprezentacja sposobu pracy sdków z udostępnionym magazynem aplikacji w celu włączenia współużytkowania:

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

#### <a name="broker-assisted-logins"></a>Logowania wspomagane brokerem

Logowania wspomagane przez brokera są środowiska logowania, które występują w aplikacji brokera i używać magazynu i zabezpieczeń brokera do udostępniania poświadczeń we wszystkich aplikacjach na urządzeniu, które stosują platformę tożsamości. Oznacza to, że aplikacje polegają na brokerze, aby zalogować się do użytkownika. W systemach iOS i Android brokerzy ci są dostarczane za pośrednictwem aplikacji do pobrania, które klienci instalują niezależnie lub wypychani do urządzenia przez firmę, która zarządza urządzeniem dla swojego użytkownika. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W systemie Windows ta funkcja jest dostarczana przez wybór konta wbudowany w system operacyjny, znany technicznie jako Broker uwierzytelniania sieci Web.

Środowisko różni się w zależności od platformy i czasami może być uciążliwe dla użytkowników, jeśli nie jest poprawnie zarządzane. Prawdopodobnie najbardziej znasz ten wzorzec, jeśli masz zainstalowaną aplikację Facebook i korzystasz z Facebook Connect z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

W przypadku systemu iOS prowadzi to do animacji "przejścia", w której aplikacja jest wysyłana w tle, podczas gdy aplikacje Microsoft Authenticator są dostępne na pierwszym planie dla użytkownika, aby wybrać konto, za pomocą którego chcesz się zalogować.

W systemie Android i Windows wybieracz kont jest wyświetlany na górze aplikacji, co jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Jak broker jest wywoływany

Jeśli na urządzeniu jest zainstalowany zgodny broker, taki jak aplikacja Microsoft Authenticator, pakiety SDK automatycznie wykonają pracę wywoływania brokera, gdy użytkownik wskaże, że chce zalogować się przy użyciu dowolnego konta z platformy tożsamości. To konto może być osobistym kontem Microsoft, kontem służbowym lub kontem, które udostępniasz i hostujesz na platformie Azure przy użyciu naszych produktów B2C i B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>W jaki sposób zapewniamy prawidłowe stosowanie wniosku

Konieczność zapewnienia tożsamości aplikacji, która wywołuje brokera ma kluczowe znaczenie dla bezpieczeństwa, które zapewniamy w brokera wspomaganych logowania. Ani system iOS, ani Android wymusza unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwe aplikacje mogą "sfałszować" identyfikator legalnej aplikacji i odbierać tokeny przeznaczone dla legalnej aplikacji. Aby upewnić się, że zawsze komunikujemy się z właściwą aplikacją w czasie wykonywania, prosimy dewelopera o podanie niestandardowego identyfikatora redirectURI podczas rejestrowania aplikacji w firmie Microsoft. Jak deweloperzy powinni spreparować ten identyfikator URI przekierowania jest szczegółowo omówione poniżej. Ten niestandardowy redirectURI zawiera identyfikator pakietu aplikacji i jest gwarantowane, aby być unikatowe dla aplikacji przez Apple App Store. Gdy aplikacja wywołuje brokera, broker prosi system operacyjny iOS, aby zapewnić go z identyfikatorem pakietu, który zadzwonił do brokera. Broker udostępnia ten identyfikator pakietu do firmy Microsoft w wywołaniu do naszego systemu tożsamości. Jeśli identyfikator pakietu aplikacji nie jest zgodny z identyfikatorem pakietu dostarczonym nam przez dewelopera podczas rejestracji, odmówimy dostępu do tokenów dla zasobu, o który żąda aplikacja. Ten czek gwarantuje, że tylko aplikacja zarejestrowana przez dewelopera odbiera tokeny.

**Deweloper ma wybór, czy zestaw SDK wywołuje brokera lub używa przepływu wspomaganego non-broker.** Jeśli jednak deweloper zdecyduje się nie korzystać z przepływu wspomaganego przez brokera, traci korzyści wynikające z używania poświadczeń logowania przyuczajania, które użytkownik mógł już dodać na urządzeniu i uniemożliwia korzystanie z aplikacji z funkcjami biznesowymi firmy Microsoft zapewnia jej klientów, takich jak dostęp warunkowy, funkcje zarządzania usługą Intune i uwierzytelnianie oparte na certyfikatach.

Te logowania mają następujące zalety:

* Użytkownik ma doświadczenie SSO we wszystkich swoich aplikacjach, niezależnie od dostawcy.
* Aplikacja może korzystać z bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy, lub korzystać z pakietu produktów usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Znacznie bezpieczniejsze środowisko logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera z dodatkowych algorytmów zabezpieczeń i szyfrowania.

Te logowania mają następujące wady:

* W iOS użytkownik jest przesunięty z doświadczenia aplikacji, gdy poświadczenia są wybierane.
* Utrata możliwości zarządzania środowiskiem logowania dla klientów w aplikacji.

Oto reprezentacja sposobu pracy SDK z aplikacjami brokera, aby włączyć sygosk SSO:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Włączanie przystawki SSO między aplikacjami przy użyciu usługi ADAL

W tym miejscu używamy SDK ADAL iOS do:

* Włączanie asystenta SSO ze wspomaganiem nie-brokera dla pakietu aplikacji
* Włącz obsługę sytego syfonu obsługującego brokera

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie sytografii SSO dla asystenta SSO bez brokera

W przypadku asystentów SSO typu "nie-brokera" w różnych aplikacjach skomuniętnie SDK zarządzają dużą złożonością aplikacji SSO. Obejmuje to znalezienie odpowiedniego użytkownika w pamięci podręcznej i utrzymywanie listy zalogowanych użytkowników, aby można było dowiedzieć się więcej.

Aby włączyć funkcję SSO w aplikacjach, które posiadasz, należy wykonać następujące czynności:

1. Upewnij się, że wszystkie aplikacje używają tego samego identyfikatora klienta lub identyfikatora aplikacji.
2. Upewnij się, że wszystkie aplikacje mają ten sam certyfikat podpisywania firmy Apple, dzięki czemu można udostępniać breloki kluczy.
3. Poproś o te same uprawnienie do pęku kluczy dla każdej aplikacji.
4. Poinformuj sdk o udostępnionym pęku kluczy, którego chcesz używać.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Używanie tego samego identyfikatora klienta / identyfikatora aplikacji dla wszystkich aplikacji w pakiecie aplikacji

Aby platforma tożsamości wiedziała, że może udostępniać tokeny w aplikacjach, każda z aplikacji będzie musiała współużytkować ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został podany podczas rejestracji pierwszej aplikacji w portalu.

Przekierowanie identyfikatorów URI umożliwia identyfikowanie różnych aplikacji do usługi tożsamości firmy Microsoft, jeśli używa tego samego identyfikatora aplikacji. Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w portalu dołączania. Każda aplikacja w pakiecie będzie miała inny identyfikator URI przekierowania. Poniżej znajduje się przykład tego wyglądu:

Przekierowanie identyfikatora URI aplikacji1:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Przekierowanie URI aplikacji2:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

Przekierowanie identyfikatora URI aplikacji3:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Są one zagnieżdżone pod tym samym identyfikatorem klienta / identyfikatorem aplikacji i wyszukane na podstawie identyfikatora URI przekierowania, który zwracasz do nas w konfiguracji SDK.

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

Format tych przekierowań identyfikatorów URI wyjaśniono poniżej. Możesz użyć dowolnego identyfikatora URI przekierowania, chyba że chcesz wesprzeć brokera, w którym to przypadku musi wyglądać mniej więcej tak jak powyżej*

#### <a name="create-keychain-sharing-between-applications"></a>Tworzenie współużytkowania pęku kluczy między aplikacjami

Włączenie udostępniania pęku kluczy wykracza poza zakres tego dokumentu i jest objęte dokumentem Firmy Apple [Dodawanie możliwości.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) Ważne jest, aby zdecydować, co ma być wywoływane pęku kluczy i dodać tę funkcję we wszystkich aplikacjach.

Po prawidłowym skonfigurowaniu uprawnień powinien zostać wyświetlony plik w `entitlements.plist` katalogu projektu, który zawiera coś, co wygląda następująco:

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

Po włączeniu uprawnienia do pęku kluczy w każdej z aplikacji i gotowości do użycia funkcji SSO należy poinformować sdk tożsamości o pęku kluczy, używając następującego ustawienia w następującym `ADAuthenticationSettings` ustawieniu:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Po udostępnieniu pęku kluczy w aplikacjach każda aplikacja może usunąć użytkowników lub co gorsza usunąć wszystkie tokeny w aplikacji. Jest to szczególnie katastrofalne, jeśli masz aplikacje, które opierają się na tokeny do pracy w tle. Udostępnianie pęku kluczy oznacza, że należy być bardzo ostrożnym w każdej operacji usuwania za pośrednictwem skomuny sdk tożsamości.

Gotowe. SDK będzie teraz udostępniać poświadczenia we wszystkich aplikacjach. Lista użytkowników będzie również współużytkowana przez wystąpienia aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie syto dla brokera wspomaganego sytemiewu

Możliwość korzystania przez aplikację z dowolnego brokera zainstalowanego na urządzeniu jest **domyślnie wyłączona.** Aby korzystać z aplikacji z brokerem, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Kroki, które należy wykonać, to:

1. Włącz tryb brokera w wywołaniu kodu aplikacji do ms SDK.
2. Ustal nowy identyfikator URI przekierowania i podaj go zarówno do aplikacji, jak i do rejestracji aplikacji.
3. Rejestrowanie schematu adresów URL.
4. Dodaj uprawnienie do pliku info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji

Możliwość korzystania z brokera przez aplikację jest włączona podczas tworzenia "kontekstu" lub początkowej konfiguracji obiektu uwierzytelniania. Można to zrobić, ustawiając typ poświadczeń w kodzie:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Ustawienie `AD_CREDENTIALS_AUTO` pozwoli SDK, aby spróbować wywołać brokera, `AD_CREDENTIALS_EMBEDDED` uniemożliwi SDK wywołanie do brokera.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Rejestrowanie schematu adresu URL

Platforma tożsamości używa adresów URL do wywoływania brokera, a następnie zwracania kontroli z powrotem do aplikacji. Aby zakończyć tę podróż w obie strony, potrzebujesz schematu adresu URL zarejestrowanego dla aplikacji, o której będzie wiedział platforma tożsamości. Może to być dodatek do innych schematów aplikacji, które zostały wcześniej zarejestrowane w aplikacji.

> [!WARNING]
> Zalecamy, aby schemat adresu URL był dość unikalny, aby zminimalizować szanse innej aplikacji przy użyciu tego samego schematu adresu URL. Firma Apple nie wymusza unikatowości schematów adresów URL zarejestrowanych w sklepie z aplikacjami.

Poniżej znajduje się przykład, jak to pojawia się w konfiguracji projektu. Można to również zrobić w XCode, jak również:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Ustanowienie nowego identyfikatora URI przekierowania za pomocą schematu adresu URL

Aby upewnić się, że zawsze zwracamy tokeny poświadczeń do poprawnej aplikacji, musimy upewnić się, że oddzwonimy do aplikacji w sposób, który system operacyjny iOS może zweryfikować. System operacyjny iOS zgłasza do aplikacji brokera firmy Microsoft identyfikator pakietu aplikacji wywołującej go. Nie może to być sfałszowane przez fałszywą aplikację. W związku z tym możemy wykorzystać to wraz z identyfikatorem URI naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do poprawnej aplikacji. Wymagamy, aby ustanowić ten unikatowy identyfikator URI przekierowania zarówno w aplikacji i ustawić jako identyfikator URI przekierowania w naszym portalu dla deweloperów.

Identyfikator URI przekierowania musi mieć właściwą formę:

`<app-scheme>://<your.bundle.id>`

przykład: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Ten identyfikator URI przekierowania musi być określony w rejestracji aplikacji przy użyciu [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji usługi Azure AD, zobacz [Integrowanie z usługą Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Dodawanie identyfikatora URI przekierowania w aplikacji i portalu deweloperów w celu obsługi uwierzytelniania opartego na certyfikatach

Aby obsługiwać uwierzytelnianie oparte na cert, należy zarejestrować w aplikacji i [witrynie Azure portal](https://portal.azure.com/) drugi "msauth", aby obsłużyć uwierzytelnianie certyfikatów, jeśli chcesz dodać tę obsługę w aplikacji.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

przykład: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Krok 4: Dodawanie parametru konfiguracji do aplikacji

ADAL używa –canOpenURL: aby sprawdzić, czy broker jest zainstalowany na urządzeniu. W systemie iOS 9 włączone apple zablokował, jakie schematy aplikacja może wysyłać zapytania. Musisz dodać "msauth" do sekcji LSApplicationQueriesSchemes `info.plist file`w sekcji .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Skonfigurowano sytą konfigurację!

Teraz sdk tożsamości automatycznie będzie udostępniać poświadczenia w aplikacjach i wywołać brokera, jeśli jest obecny na ich urządzeniu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojedynczym loguchajniu SAML](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
