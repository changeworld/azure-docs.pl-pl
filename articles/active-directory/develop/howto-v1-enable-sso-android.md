---
title: Sposób włączenia logowania jednokrotnego dla wielu aplikacji w systemie Android za pomocą biblioteki ADAL | Dokumentacja firmy Microsoft
description: 'Jak włączyć logowanie jednokrotne w aplikacji za pomocą funkcji zestawu SDK biblioteki ADAL. '
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
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39602018"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Sposób włączenia logowania jednokrotnego dla wielu aplikacji w systemie Android za pomocą biblioteki ADAL
Dostarczanie pojedynczego logowania jednokrotnego (SSO), aby użytkownicy potrzebują tylko raz wprowadzić swoje poświadczenia i mieć tych poświadczeń, automatycznie działa w aplikacjach jest teraz standardem branżowym. Trudności podczas wprowadzania nazwy użytkownika i hasła na małym ekranie, często razy w połączeniu z dodatkowy czynnik (2FA), takich jak rozmowa telefoniczna lub dzwonić kodu powoduje przestoje, jeśli użytkownik ma więcej niż jeden raz logowania jednokrotnego.

Ponadto jeśli zastosujesz platforma tożsamości, która inne aplikacje mogą używać takich jak Accounts firmy Microsoft lub konta służbowego z Microsoft365, klienci oczekują, że tych poświadczeń użytkownika mają być dostępne do użycia w swoich aplikacjach niezależnie od wydawcy.

Platforma Microsoft Identity, wraz z zestawami SDK tożsamość Microsoft daje możliwość klienci będą zachwyceni za pomocą logowania jednokrotnego znajdującego się w obrębie pakietu aplikacji lub, podobnie jak w przypadku brokera funkcji i aplikacji wystawcy uwierzytelnienia, przez całą urządzenie.

W tym przewodniku opisano, jak do konfigurowania zestawu SDK w swojej aplikacji w celu zapewnienia logowania jednokrotnego dla klientów.

Poprzedni dokument zakłada, wiesz, jak zintegrować aplikację z [zestawu Android SDK w programie Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Pojęcia dotyczące rejestracji Jednokrotnej platforma tożsamości firmy Microsoft
### <a name="microsoft-identity-brokers"></a>Brokerzy tożsamości firmy Microsoft
Firma Microsoft udostępnia aplikacje dla każdej platformy urządzeń przenośnych, które umożliwiają łączenie poświadczeń między aplikacjami pochodzącymi od różnych dostawców i umożliwia specjalnych zaawansowanych funkcji, które wymagają pojedynczego bezpiecznego miejsca w celu weryfikowania poświadczeń. Zestaw SDK wywołuje te **brokerów**. W systemach iOS i Android brokerów są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają niektóre lub wszystkie urządzenia dla swoich pracowników. Brokerzy obsługę zarządzania zabezpieczeniami tylko niektóre aplikacje lub całego urządzenia, które są oparte na co pozwalają administratorom IT. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.

#### <a name="broker-assisted-logins"></a>Asystowana logowania brokera
Logowania wspierana przez brokera są środowiska logowania, które wystąpiły w ramach aplikacji brokera i korzystanie z magazynu i zabezpieczeń brokera udostępnianie poświadczeń we wszystkich aplikacjach na urządzeniu, które są stosowane z platformą Microsoft Identity. Domniemanie trwa aplikacji będzie zależny od broker do logowania użytkowników. W systemach iOS i Android brokerzy są realizowane za pośrednictwem aplikacji do pobrania, że klienci zainstalować niezależnie lub wypychania do urządzenia przez firmę, użytkowników, którzy zarządzają urządzenia dla swoich użytkowników. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W Windows ta funkcjonalność jest dostarczana przez selektora konta wbudowanej w system operacyjny, znanego pod względem technicznym jako Broker uwierzytelniania w sieci Web.
Środowisko jest zależna od platformy i czasami może być szkodliwe dla użytkowników, jeśli nie poprawnie zarządzane. Znasz prawdopodobnie najbardziej tego wzorca Jeśli masz zainstalowaną aplikacją usługi Facebook i za pomocą usługi Facebook Connect z innej aplikacji. Platforma Microsoft Identity korzysta z tego samego wzorca.

W systemie Android selektora konta jest wyświetlany u góry Twojej aplikacji, które są mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Sposób wywoływania pobiera brokera
Jeśli zgodne broker jest zainstalowana na urządzeniu, takie jak aplikacja Microsoft Authenticator zestawami SDK Microsoft Identity automatycznie będzie wykonywać pracę wywoływania broker dla Ciebie, gdy użytkownik wskazuje, że chcą Zaloguj się przy użyciu dowolnego konta firmy Microsoft Platforma tożsamości usługi. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak firma Microsoft zapewnia aplikacji jest prawidłowy
 
 Konieczność zapewnienia tożsamości wywołanie aplikacji brokera ma podstawowe znaczenie dla zabezpieczeń udostępnianych w polu nazw logowania brokera wspierana. iOS i Android nie wymusza unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwych aplikacji może być "podszywały się pod" identyfikator autoryzowanych aplikacji i odbierać tokeny przeznaczone dla autoryzowanych aplikacji. Aby upewnić się, że Microsoft zawsze komunikuje się z prawej aplikacji w czasie wykonywania, deweloper jest podanie niestandardowego elementu redirectURI podczas rejestrowania aplikacji z firmą Microsoft. **Jak deweloperów powinien tworzyć identyfikator URI przekierowania jest szczegółowo poniżej.** Ten niestandardowy element redirectURI zawiera odcisk palca certyfikatu, aplikacji i zapewniony przez Google Play Store być unikatowa w aplikacji. Gdy aplikacja wywołuje brokera, broker pyta, czy systemu operacyjnego Android będzie podawać go za pomocą odcisku palca certyfikatu, który wywołuje brokera. Broker to odcisk palca certyfikatu do firmy Microsoft w wywołaniu systemu tożsamości. Jeśli odcisk palca certyfikatu aplikacji nie odpowiada odcisk palca certyfikatu, dostarczone przez dewelopera podczas rejestracji, odmowa dostępu do tokenów dla zasobów, których żąda aplikacja. To sprawdzenie gwarantuje, że tylko aplikacji, które zostały zarejestrowane przez dewelopera otrzyma tokenów.

Nazwy logowania SSO obsługiwanych przez brokera mają następujące korzyści:

* Usługa rejestracji Jednokrotnej podejrzewać wszystkich swoich aplikacji, niezależnie od tego dostawcy.
* Aplikację za pomocą bardziej zaawansowanych funkcji biznesowych, takich jak dostęp warunkowy i obsługi scenariuszy usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników biznesowych.
* Bardziej bezpieczne logowania jako tożsamość aplikacji i użytkownika są weryfikowane przez aplikację brokera przy użyciu algorytmów zabezpieczeń i szyfrowania.

Poniżej przedstawiono reprezentację działania zestawami SDK Microsoft tożsamości za pomocą aplikacji brokera, aby włączyć logowanie Jednokrotne:

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

Korzystając z tego informacje uzupełniające, które powinno być możliwe do lepszego zrozumienia i zaimplementowania logowania jednokrotnego w aplikacji przy użyciu zestawów SDK i platformą Microsoft Identity.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera korzystającej z logowania jednokrotnego
Dla aplikacji, aby używać każdego brokera, która jest zainstalowana na urządzeniu jest **domyślnie wyłączona**. Aby można było używać aplikacji przy użyciu brokera, należy wykonać dodatkową konfigurację i dodać kod do aplikacji.

Dostępne są następujące czynności:

1. Włącz tryb brokera w kodzie aplikacji telefonicznej do zestawu SDK MS
2. Ustanawia nowy identyfikator URI przekierowania i przewidzieć, że do Twojej rejestracji aplikacji i aplikacji
3. Konfigurowanie odpowiednich uprawnień w manifeście systemu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Włącz tryb brokera w aplikacji
Możliwości aplikacji pod kątem wykorzystania brokera jest włączona, podczas tworzenia "ustawienia" lub początkowej konfiguracji wystąpienia usługi uwierzytelniania. Aby to zrobić w aplikacji:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Ustanawiania nowego identyfikatora URI z schemat adresu URL przekierowania
Aby upewnić się, że recevies aplikacji odpowiednie zwracanego poświadczenie tokenów, jest wymagane, aby upewnić się, wywołania zwrotnego aplikacji w taki sposób, aby sprawdzić, systemu operacyjnego Android. Systemu operacyjnego Android używa skrót certyfikatu w sklepie Google Play. Ten skrót certyfikatu nie sfałszowane przez aplikację nieautoryzowanych. Wraz z identyfikatora URI aplikacji brokera firma Microsoft zapewnia, że tokeny są zwracane do właściwej aplikacji. Przekierowanie Unikatowy identyfikator URI jest wymagany do zarejestrowania się w aplikacji.

Identyfikator URI przekierowania musi być w postaci prawidłowego:

`msauth://packagename/Base64UrlencodedSignature`

przykład: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ten identyfikator URI przekierowania możesz zarejestrować w rejestracji aplikacji przy użyciu [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [integracji z usługą Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Konfigurowanie odpowiednich uprawnień w aplikacji
Aplikacja brokera w systemie Android używa funkcji Menedżera kont systemu operacyjnego Android do zarządzania poświadczeniami w aplikacjach. Aby można było używać brokera w systemie Android manifest aplikacji musi mieć uprawnienia do korzystania z kont elementu AccountManager. Te uprawnienia są szczegółowo omówione w [Google dokumentację dla Menedżera konta](http://developer.android.com/reference/android/accounts/AccountManager.html)

W szczególności te uprawnienia są:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Skonfigurowano logowania jednokrotnego!
Teraz zestaw SDK programu Microsoft Identity będzie automatycznie udostępniać poświadczenia w aplikacjach i wywołać brokera, jeśli jest obecny na urządzeniu.

