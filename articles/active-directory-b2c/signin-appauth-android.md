---
title: Uzyskiwanie tokenu w aplikacji systemu Android
titleSuffix: Azure AD B2C
description: Jak utworzyć aplikację dla systemu Android, która używa appauth z usługi Azure Active Directory B2C do zarządzania tożsamościami użytkowników i uwierzytelniania użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183787"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Logowanie się przy użyciu aplikacji dla systemu Android w usłudze Azure Active Directory B2C

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Te standardy umożliwiają korzystanie z dowolnej biblioteki, którą chcesz zintegrować z usługą Azure Active Directory B2C. Aby ułatwić korzystanie z innych bibliotek, można użyć przewodnika, takiego jak ten, aby zademonstrować sposób konfigurowania bibliotek innych firm do łączenia się z platformą tożsamości firmy Microsoft. Większość bibliotek, które implementują [specyfikację OAuth2 RFC6749,](https://tools.ietf.org/html/rfc6749) może łączyć się z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft nie udostępnia poprawek dla bibliotek innych firm i nie dokonała przeglądu tych bibliotek. W tym przykładzie jest przy użyciu biblioteki innej firmy o nazwie AppAuth, który został przetestowany pod kątem zgodności w podstawowych scenariuszach z usługi Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do projektu open source biblioteki. Zobacz [ten artykuł,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) aby uzyskać więcej informacji.
>
>

Jeśli dopiero rozpoczynasz korzystanie ze standardu OAuth2 lub OpenID Connect, spora część tej przykładowej konfiguracji może być dla Ciebie niezrozumiała. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie zarejestruj aplikację w dzierżawie usługi Azure AD B2C. Dzięki temu usługa Azure AD informacje potrzebne do bezpiecznej komunikacji z aplikacją.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Również nagrać niestandardowy identyfikator URI przekierowania do użycia w późniejszym kroku. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Tworzenie przepływów użytkownika

W usłudze Azure AD B2C każde środowisko użytkownika jest definiowane przez [przepływ użytkownika,](user-flow-overview.md)który jest zestawem zasad, które kontrolują zachowanie usługi Azure AD. Ta aplikacja wymaga przepływu użytkownika logowania i rejestracji. Podczas tworzenia przepływu użytkownika należy pamiętać o:

* Wybierz **nazwę wyświetlaną** jako atrybut rejestracji w przepływie użytkownika.
* Wybierz **nazwy wyświetlane** i oświadczenia aplikacji **identyfikatora obiektu** w każdym przepływie użytkownika. Można również wybrać inne oświadczenia.
* Skopiuj **nazwę** każdego przepływu użytkownika po jego utworzeniu. Powinny zawierać prefiks `b2c_1_`.  Później będzie potrzebna nazwa przepływu użytkownika.

Po utworzeniu przepływów użytkownika możesz przystąpić do tworzenia aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Udostępniliśmy próbkę roboczą, która używa appauth z usługą Azure AD B2C [w usłudze GitHub.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Możesz pobrać kod i uruchomić go. Możesz szybko rozpocząć pracę z własną aplikacją przy użyciu własnej konfiguracji usługi Azure AD B2C, postępując zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Próbka jest modyfikacją próbki dostarczonej przez [AppAuth](https://openid.github.io/AppAuth-Android/). Odwiedź ich stronę, aby dowiedzieć się więcej o AppAuth i jej funkcjach.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji w celu używania usługi Azure AD B2C z aplikacją AppAuth

> [!NOTE]
> AppAuth obsługuje interfejs API systemu Android 16 (Jellybean) i powyżej. Zalecamy korzystanie z interfejsu API 23 lub powyżej.
>

### <a name="configuration"></a>Konfigurowanie

Można skonfigurować komunikację z usługą Azure AD B2C, określając identyfikator URI odnajdywania lub określając zarówno identyfikatory identyfikatorów URI punktu końcowego autoryzacji, jak i tokenu końcowego. W obu przypadkach potrzebne będą następujące informacje:

* Identyfikator najemcy (np. contoso.onmicrosoft.com)
* Nazwa przepływu użytkownika (np.\_\_

Jeśli zdecydujesz się automatycznie odnajdywać identyfikatory identyfikatorów URI autoryzacji i tokenu końcowego, należy pobrać informacje z identyfikatora URI odnajdywania. Identyfikator URI odnajdywania\_można wygenerować, zastępując identyfikator dzierżawy i nazwę zasad\_w następującym adresie URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Następnie można uzyskać autoryzacji i identyfikatorów URI punktu końcowego tokenu i utworzyć obiekt autoryzacji Usługi konfiguracyjne, uruchamiając następujące:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Zamiast używać odnajdywania w celu uzyskania identyfikatorów URI autoryzacyjnych\_i tokenów\_końcowych, można również określić je jawnie, zastępując identyfikator dzierżawy i nazwę zasad w poniższym adresie URL:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Uruchom następujący kod, aby utworzyć obiekt autoryzacji Usługi konfiguracyjne:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autoryzowania

Po skonfigurowaniu lub pobraniu konfiguracji usługi autoryzacji można utworzyć żądanie autoryzacji. Aby utworzyć żądanie, potrzebne będą następujące informacje:

* Identyfikator klienta (application id), który został nagrany wcześniej. Na przykład `00000000-0000-0000-0000-000000000000`.
* Niestandardowy identyfikator URI przekierowania, który został nagrany wcześniej. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Oba elementy powinny zostać zapisane podczas [rejestracji aplikacji.](#create-an-application)

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Zapoznaj się z [przewodnikiem AppAuth,](https://openid.github.io/AppAuth-Android/) jak zakończyć resztę procesu. Jeśli chcesz szybko rozpocząć pracę z działającą aplikacją, zapoznaj się z [naszą próbką](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Wykonaj kroki opisane w [README.md,](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) aby wprowadzić własną konfigurację usługi Azure AD B2C.
