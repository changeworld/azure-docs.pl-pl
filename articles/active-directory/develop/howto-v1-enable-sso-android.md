---
title: Sposób włączenia logowania jednokrotnego dla wielu aplikacji w systemie Android za pomocą biblioteki ADAL | Dokumentacja firmy Microsoft
description: Jak korzystać z funkcji zestawu SDK biblioteki ADAL w celu umożliwienia logowania jednokrotnego dla aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb11a4a926c676d37a0bf6be456e3b831a5d8357
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962640"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Instrukcje: Włączanie logowania jednokrotnego dla wielu aplikacji, w systemie Android za pomocą biblioteki ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Logowanie jednokrotne (SSO) umożliwia użytkownikom tylko raz wprowadzić swoje poświadczenia i ma działać automatycznie w aplikacjach i platformach, które mogą korzystać z innych aplikacji (na przykład Accounts firmy Microsoft lub konta służbowego z usługi Microsoft 365) poświadczenia nie niezależnie od tego wydawcy.

Platforma tożsamości firmy Microsoft, wraz z zestawów SDK, można łatwo włączyć logowanie Jednokrotne w ramach własnego pakietu aplikacji lub z możliwości broker i aplikacji wystawcy uwierzytelnienia dla całego urządzenia.

W tym instruktażu dowiesz się, jak skonfigurować zestaw SDK w swojej aplikacji w celu zapewnienia logowania jednokrotnego dla klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Niniejszy instruktaż przyjęto założenie, że wiesz, jak:

- Aprowizacja aplikacji przy użyciu starszej wersji portalu usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [rejestrowanie aplikacji](quickstart-register-app.md)
- Integrowanie aplikacji za pomocą [usługi Azure AD dla systemu Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Pojęcia rejestracji jednokrotnej

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy urządzeń przenośnych, które umożliwiają łączenie poświadczeń między aplikacjami pochodzącymi od różnych dostawców oraz rozszerzone funkcje, które wymagają pojedynczego bezpiecznego miejsca w celu weryfikowania poświadczeń. Są to tak zwane **brokerów**.

W systemach iOS i Android brokerów są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, który zarządza niektóre lub wszystkie z urządzenia dla swoich pracowników. Brokerzy obsługę zarządzania zabezpieczeniami tylko niektóre aplikacje lub całego urządzenia na podstawie konfiguracji administratora IT. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.

#### <a name="broker-assisted-login"></a>Asystowana danych logowania brokera

Logowania wspierana przez brokera są procesy logowania, które występują w ramach aplikacji brokera i korzystanie z magazynu i zabezpieczeń brokera udostępnianie poświadczeń we wszystkich aplikacjach na urządzeniu, które mają zastosowanie platformy tożsamości. Domniemanie trwa aplikacji będzie zależny od broker do logowania użytkowników. W systemach iOS i Android brokerzy są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają urządzenia dla swoich użytkowników. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.
Środowisko jest zależna od platformy i czasami może być szkodliwe dla użytkowników, jeśli nie poprawnie zarządzane. Znasz prawdopodobnie najbardziej tego wzorca Jeśli masz zainstalowaną aplikacją usługi Facebook i za pomocą usługi Facebook Connect z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

W systemie Android selektora konta jest wyświetlany u góry Twojej aplikacji, które są mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Sposób wywoływania pobiera brokera

Jeśli zgodne broker jest zainstalowana na urządzeniu, takie jak aplikacja Microsoft Authenticator tożsamości zestawów SDK zostanie automatycznie wykonują pracę wywoływania broker dla Ciebie, gdy użytkownik wskazuje, że chcesz zalogować się przy użyciu dowolnego konta z platformą tożsamości.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak firma Microsoft zapewnia aplikacji jest prawidłowy

Konieczność zapewnienia tożsamości wywołanie aplikacji brokera ma podstawowe znaczenie dla zabezpieczeń udostępnianych w polu nazw logowania brokera wspierana. iOS i Android nie wymusza unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwych aplikacji może być "podszywały się pod" identyfikator autoryzowanych aplikacji i odbierać tokeny przeznaczone dla autoryzowanych aplikacji. Aby upewnić się, że Microsoft zawsze komunikuje się z prawej aplikacji w czasie wykonywania, deweloper jest podanie niestandardowego elementu redirectURI podczas rejestrowania aplikacji z firmą Microsoft. **Jak deweloperów powinien tworzyć identyfikator URI przekierowania jest szczegółowo poniżej.** Ten niestandardowy element redirectURI zawiera odcisk palca certyfikatu, aplikacji i zapewniony przez Google Play Store być unikatowa w aplikacji. Gdy aplikacja wywołuje brokera, broker pyta, czy systemu operacyjnego Android będzie podawać go za pomocą odcisku palca certyfikatu, który wywołuje brokera. Broker to odcisk palca certyfikatu do firmy Microsoft w wywołaniu systemu tożsamości. Jeśli odcisk palca certyfikatu aplikacji nie odpowiada odcisk palca certyfikatu, dostarczone przez dewelopera podczas rejestracji, odmowa dostępu do tokenów dla zasobów, których żąda aplikacja. To sprawdzenie gwarantuje, że tylko aplikacji, które zostały zarejestrowane przez dewelopera otrzyma tokenów.

Nazwy logowania SSO obsługiwanych przez brokera mają następujące korzyści:

* Usługa rejestracji Jednokrotnej podejrzewać wszystkich swoich aplikacji, niezależnie od tego dostawcy.
* Aplikację za pomocą bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy i obsługi scenariuszy usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bardziej bezpieczne logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera przy użyciu algorytmów zabezpieczeń i szyfrowania.

Poniżej przedstawiono reprezentację jak działają te zestawy SDK za pomocą aplikacji brokera, aby włączyć logowanie Jednokrotne:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera korzystającej z logowania jednokrotnego

Możliwość wykonywania aplikacja może używać każdego brokera, która jest zainstalowana na urządzeniu jest domyślnie wyłączona. Aby można było używać aplikacji przy użyciu brokera, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Dostępne są następujące czynności:

1. Włącz tryb brokera w kodzie aplikacji telefonicznej do zestawu SDK MS
2. Ustanawia nowy identyfikator URI przekierowania i przewidzieć, że do Twojej rejestracji aplikacji i aplikacji
3. Konfigurowanie odpowiednich uprawnień w manifeście systemu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji

Możliwości aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "ustawienia" lub początkowej konfiguracji wystąpienia usługi uwierzytelniania. Aby to zrobić w aplikacji:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Ustanowienie nowych przekierowania URI ze schematem Twojego adresu URL

W celu zapewnienia, że odpowiednie aplikacja otrzymuje zwracanego poświadczenie tokenów jest wymagane, aby upewnić się, wywołania zwrotnego aplikacji w taki sposób, aby sprawdzić, systemu operacyjnego Android. Systemu operacyjnego Android używa skrót certyfikatu w sklepie Google Play. Ten skrót certyfikatu nie sfałszowane przez aplikację nieautoryzowanych. Wraz z identyfikatora URI aplikacji brokera firma Microsoft zapewnia, że tokeny są zwracane do właściwej aplikacji. Przekierowanie Unikatowy identyfikator URI jest wymagany do zarejestrowania się w aplikacji.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ten identyfikator URI przekierowania możesz zarejestrować w rejestracji aplikacji przy użyciu [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Konfigurowanie odpowiednich uprawnień w aplikacji

Aplikacja brokera w systemie Android używa funkcji Menedżera kont systemu operacyjnego Android do zarządzania poświadczeniami w aplikacjach. Aby można było używać brokera w systemie Android manifest aplikacji musi mieć uprawnienia do korzystania z kont elementu AccountManager. Te uprawnienia są szczegółowo omówione w [Google dokumentację dla Menedżera konta](https://developer.android.com/reference/android/accounts/AccountManager.html)

W szczególności te uprawnienia są:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Skonfigurowano logowania jednokrotnego!

Teraz tożsamości zestawu SDK zostanie automatycznie udostępniać poświadczenia w aplikacjach i wywołania brokera, jeśli jest obecny na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [pojedynczego logowania jednokrotnego protokołu SAML](single-sign-on-saml-protocol.md)
