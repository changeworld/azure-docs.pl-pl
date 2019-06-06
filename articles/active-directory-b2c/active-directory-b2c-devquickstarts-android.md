---
title: Pobieranie tokenu przy użyciu aplikacji systemu Android w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule pokazano sposób tworzenia aplikacji dla systemu Android, która używa AppAuth za pomocą usługi Azure Active Directory B2C do zarządzania tożsamościami użytkowników i ich uwierzytelniać.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4db4806b6be018bfc53a155627de825bf62d8395
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510107"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Zaloguj się przy użyciu aplikacji systemu Android w usłudze Azure Active Directory B2C

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Standardy te umożliwiają korzystanie z każdej biblioteki, którą chcesz zintegrować z usługi Azure Active Directory B2C. Aby ułatwić Ci korzystać z innych bibliotek, umożliwia wskazówki podobny do poniższego pokazują, jak skonfigurować 3 biblioteki innej firmy, aby nawiązać połączenie z platformą Microsoft identity. Większość bibliotek implementujących [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) może nawiązać połączenia z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft udostępnia poprawki dla 3 innych firm, biblioteki i nie zrobił przeglądu tych bibliotek. W tym przykładzie używa 3 biblioteki innej firmy o nazwie AppAuth, który został przetestowany na zgodność w podstawowych scenariuszy z usługą Azure AD B2C. Problemy i sugestie funkcji powinny być kierowane do biblioteki typu open-source projektu. Zobacz [w tym artykule](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) Aby uzyskać więcej informacji.  
>
>

Jeśli dopiero rozpoczynasz korzystanie ze standardu OAuth2 lub OpenID Connect, spora część tej przykładowej konfiguracji może być dla Ciebie niezrozumiała. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu do usługi Azure AD będą przekazywane informacje wymagane do bezpiecznego komunikowania się z aplikacją. Aby utworzyć aplikację mobilną, postępuj zgodnie z [w instrukcjach](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **Native Client** w aplikacji.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie on potrzebny później.
* Konfigurowanie natywnego klienta **identyfikator URI przekierowania** (np. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). On również będzie później potrzebny.

## <a name="create-your-user-flows"></a>Tworzyć przepływy użytkownika

W usłudze Azure AD B2C każde działanie użytkownika jest definiowany przez [przepływ użytkownika](active-directory-b2c-reference-policies.md), czyli zestaw zasad, które kontrolują zachowanie usługi Azure AD. Ta aplikacja wymaga przepływ logowania i rejestracji użytkownika. Po utworzeniu przepływu użytkownika, należy koniecznie:

* Wybierz **nazwę wyświetlaną** jako atrybut rejestracji przepływu użytkownika.
* Wybierz **nazwę wyświetlaną** i **obiektu o identyfikatorze** oświadczeniami aplikacji w każdym przepływu użytkownika. Można również wybrać inne oświadczenia.
* Kopiuj **nazwa** każdego przepływu użytkownika po jego utworzeniu. Powinny zawierać prefiks `b2c_1_`.  Przepływ użytkownika będą potrzebne później.

Po utworzeniu przepływów użytkownika możesz przystąpić do kompilowania aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Udostępniliśmy próbkę pracy z zastosowaniem AppAuth za pomocą usługi Azure AD B2C [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Możesz pobrać kod i uruchom go. Możesz szybko rozpocząć pracę z własną aplikację przy użyciu konfiguracji usługi Azure AD B2C, postępując zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Próbka jest modyfikacja przykładowym przez [AppAuth](https://openid.github.io/AppAuth-Android/). Odwiedź ich stronę, aby dowiedzieć się więcej na temat AppAuth i jego funkcji.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji za pomocą usług Azure AD B2C AppAuth

> [!NOTE]
> AppAuth obsługuje Android API 16 (Jellybean) i nowszych. Firma Microsoft zaleca używanie interfejsu API 23 i nowsze wersje.
>

### <a name="configuration"></a>Konfigurowanie

Komunikację można skonfigurować w usłudze Azure AD B2C, określając odnajdywanie identyfikatora URI lub określając punkt końcowy autoryzacji i tokena identyfikatory URI punktów końcowych. W obu przypadkach są potrzebne następujące informacje:

* Identyfikator dzierżawy (np. contoso.onmicrosoft.com)
* Przepływ użytkownika (np. usługi B2C\_1\_SignUpIn)

Jeśli zostanie wybrana opcja automatycznego wykrywania autoryzacji i tokena identyfikatory URI punktów końcowych, należy pobrać informacje z identyfikatora URI odnajdywania. Odnajdywanie identyfikatora URI mogą być generowane przez zastąpienie dzierżawy\_identyfikator i zasady\_nazwy w następującym adresem URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Następnie można nabyć autoryzacji i tokena identyfikatory URI punktów końcowych i Utwórz obiekt AuthorizationServiceConfiguration, uruchamiając następujące:

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

Zamiast używania odnajdywania, aby uzyskać autoryzacji i tokena identyfikatory URI punktów końcowych, można również określić je jawnie, zastępując dzierżawy\_identyfikator i zasady\_nazwę w adresie URL poniżej:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po Konfigurowanie lub pobieranie konfiguracji usługi autoryzacji, można konstruować żądanie autoryzacji. Aby utworzyć żądanie, są potrzebne następujące informacje:

* Identyfikator klienta (np. 00000000-0000-0000-0000-000000000000)
* Identyfikator URI przekierowania ze schematem niestandardowym (np. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Oba elementy powinny zostały zapisane po użytkownik zostały [rejestrując aplikację](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Zapoznaj się [przewodnik AppAuth](https://openid.github.io/AppAuth-Android/) na temat sposobu ukończenia pozostałej części procesu. Jeśli potrzebujesz szybko rozpocząć pracę z działającą aplikację, zapoznaj się z [naszego przykładu](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postępuj zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) wprowadzić konfigurację usługi Azure AD B2C.

