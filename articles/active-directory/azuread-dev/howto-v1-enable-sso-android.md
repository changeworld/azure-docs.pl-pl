---
title: Jak włączyć cross-app SSO na Androida za pomocą ADAL | Dokumenty firmy Microsoft
description: Jak korzystać z funkcji SDK ADAL, aby włączyć logowanie jednokrotne w aplikacjach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 0b87a9cd0ae29281faad4209f4449d547921835d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154818"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Jak: Włącz wielonauczowe sso na Androida za pomocą usługi ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Logowanie jednokrotne (Logowanie jednokrotne) umożliwia użytkownikom wprowadzanie poświadczeń tylko raz i automatyczne uruchamianie tych poświadczeń między aplikacjami i na różnych platformach, z których mogą korzystać inne aplikacje (takie jak konta Microsoft lub konto służbowe z usługi Microsoft 365) nie wydawcy.

Platforma tożsamości firmy Microsoft wraz z zestawami SDK ułatwia włączenie usługi SSO w ramach własnego pakietu aplikacji lub z możliwością brokera i aplikacjami Authenticator na całym urządzeniu.

W tym instrukcje dowiesz się, jak skonfigurować SDK w aplikacji, aby zapewnić klientom, aby zapewnić klientom.

## <a name="prerequisites"></a>Wymagania wstępne

W tym sposób założono, że wiesz, jak:

- Aprowizuj aplikację przy użyciu starszego portalu dla usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Zintegruj aplikację z zestawem [SDK usługi Azure AD Android](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Pojęcia logowania jednokrotnego

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy mobilnej, które umożliwiają łączenie poświadczeń między aplikacjami od różnych dostawców i rozszerzone funkcje, które wymagają jednego bezpiecznego miejsca, z którego można sprawdzić poprawność poświadczeń. Są to tak zwane **brokerzy**.

W systemach iOS i Android brokerzy są udostępniani za pośrednictwem aplikacji do pobrania, które klienci instalują niezależnie lub są wypychani do urządzenia przez firmę, która zarządza niektórymi lub wszystkimi urządzeniami dla swoich pracowników. Brokerzy obsługują zarządzanie zabezpieczeniami tylko dla niektórych aplikacji lub całego urządzenia na podstawie konfiguracji administratora IT. W systemie Windows ta funkcja jest dostarczana przez wybieracz kont wbudowany w system operacyjny, znany technicznie jako Broker uwierzytelniania sieci Web.

#### <a name="broker-assisted-login"></a>Logowanie wspomagane brokerem

Logowania wspomagane przez brokera są środowiska logowania, które występują w aplikacji brokera i używać magazynu i zabezpieczeń brokera do udostępniania poświadczeń we wszystkich aplikacjach na urządzeniu, które stosują platformę tożsamości. Implikacja jest aplikacje będą polegać na brokera do logowania użytkowników. W systemach iOS i Android brokerzy ci są dostarczane za pośrednictwem aplikacji do pobrania, które klienci instalują niezależnie lub mogą być wypychani do urządzenia przez firmę, która zarządza urządzeniem dla swojego użytkownika. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W systemie Windows ta funkcja jest dostarczana przez wybieracz kont wbudowany w system operacyjny, znany technicznie jako Broker uwierzytelniania sieci Web.
Środowisko różni się w zależności od platformy i czasami może być uciążliwe dla użytkowników, jeśli nie jest poprawnie zarządzane. Prawdopodobnie najbardziej znasz ten wzorzec, jeśli masz zainstalowaną aplikację Facebook i korzystasz z Facebook Connect z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

W systemie Android, wybór konta jest wyświetlany na górze aplikacji, co jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Jak broker jest wywoływany

Jeśli na urządzeniu jest zainstalowany zgodny broker, taki jak aplikacja Microsoft Authenticator, szesdła tożsamości automatycznie wykonają pracę wywoływania brokera, gdy użytkownik wskaże, że chce zalogować się przy użyciu dowolnego konta z platformy tożsamości.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak firma Microsoft zapewnia prawidłową prawidłową aplikację

Konieczność zapewnienia tożsamości aplikacji wywołującej brokera ma kluczowe znaczenie dla zabezpieczeń zapewnianych w logowaniach wspomaganych brokerem. IOS i Android nie wymuszają unikatowych identyfikatorów, które są prawidłowe tylko dla danej aplikacji, więc złośliwe aplikacje mogą "sfałszować" identyfikator legalnej aplikacji i odbierać tokeny przeznaczone dla legalnej aplikacji. Aby upewnić się, że firma Microsoft zawsze komunikuje się z właściwą aplikacją w czasie wykonywania, deweloper jest proszony o podanie niestandardowego identyfikatora redirectURI podczas rejestrowania aplikacji w firmie Microsoft. **Jak deweloperzy powinni spreparować ten identyfikator URI przekierowania jest szczegółowo omówione poniżej.** Ten niestandardowy redirectURI zawiera odcisk palca certyfikatu aplikacji i jest gwarantowany jako unikatowy dla aplikacji przez Sklep Google Play. Gdy aplikacja wywołuje brokera, broker prosi system operacyjny Android, aby zapewnić mu odcisk palca certyfikatu, który zadzwonił do brokera. Broker udostępnia ten odcisk palca certyfikatu firmie Microsoft w wywołaniu systemu tożsamości. Jeśli odcisk palca certyfikatu aplikacji nie jest zgodny z odciskiem palca certyfikatu dostarczonym nam przez dewelopera podczas rejestracji, odmowa dostępu do tokenów zasobu, o który żąda aplikacja. Ten czek gwarantuje, że tylko aplikacja zarejestrowana przez dewelopera odbiera tokeny.

Logowanie logowania logowania typu "Pośrednicy- logowania logowania logowania do logowania bez logowania" mają następujące zalety:

* Użytkownik ma doświadczenie SSO we wszystkich swoich aplikacjach, niezależnie od dostawcy.
* Aplikacja może używać bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy i obsługiwać scenariusze usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bezpieczniejsze środowisko logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera za pomocą dodatkowych algorytmów zabezpieczeń i szyfrowania.

Oto reprezentacja sposobu pracy SDK z aplikacjami brokera, aby włączyć sygosk SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie syto dla brokera wspomaganego sytemiewu

Możliwość użycia przez aplikację dowolnego brokera zainstalowanego na urządzeniu jest domyślnie wyłączona. Aby korzystać z aplikacji z brokerem, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Kroki, które należy wykonać, to:

1. Włącz tryb brokera w wywoływaniu kodu aplikacji do sdk MS SDK
2. Ustanawianie nowego identyfikatora URI przekierowania i zapewnianie go zarówno do aplikacji, jak i do rejestracji aplikacji
3. Konfigurowanie prawidłowych uprawnień w manifeście systemu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji

Możliwość korzystania z brokera przez aplikację jest włączona podczas tworzenia "ustawień" lub początkowej konfiguracji wystąpienia uwierzytelniania. Aby to zrobić w aplikacji:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Ustanowienie nowego identyfikatora URI przekierowania za pomocą schematu adresu URL

Aby upewnić się, że właściwa aplikacja odbiera zwrócone tokeny poświadczeń, należy upewnić się, że połączenie z powrotem do aplikacji w sposób, który system operacyjny Android można zweryfikować. System operacyjny Android używa skrótu certyfikatu w sklepie Google Play. Ten skrót certyfikatu nie może być sfałszowany przez aplikację nieuczciwych. Wraz z identyfikatorem URI aplikacji brokera firma Microsoft zapewnia, że tokeny są zwracane do poprawnej aplikacji. Unikatowy identyfikator URI przekierowania musi być zarejestrowany w aplikacji.

Identyfikator URI przekierowania musi mieć właściwą formę:

`msauth://packagename/Base64UrlencodedSignature`

przykład: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ten identyfikator URI przekierowania można zarejestrować w rejestracji aplikacji za pomocą [witryny Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji usługi Azure AD, zobacz [Integrowanie z usługą Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Konfigurowanie prawidłowych uprawnień w aplikacji

Aplikacja brokera w systemie Android używa funkcji Menedżer kont systemu operacyjnego Android do zarządzania poświadczeniami w aplikacjach. Aby korzystać z brokera w systemie Android, manifest aplikacji musi mieć uprawnienia do korzystania z kont AccountManager. Uprawnienia te zostały szczegółowo omówione w [dokumentacji Google dla Menedżera kont tutaj](https://developer.android.com/reference/android/accounts/AccountManager.html)

W szczególności uprawnienia te są następujące:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Skonfigurowano sytą konfigurację!

Teraz sdk tożsamości automatycznie będzie udostępniać poświadczenia w aplikacjach i wywołać brokera, jeśli jest obecny na ich urządzeniu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojedynczym loguchajniu SAML](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
