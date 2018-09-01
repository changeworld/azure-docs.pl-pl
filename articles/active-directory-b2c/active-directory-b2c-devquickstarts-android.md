---
title: Pobieranie tokenu przy użyciu aplikacji systemu Android w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule pokazano sposób tworzenia aplikacji dla systemu Android, która używa AppAuth za pomocą usługi Azure Active Directory B2C do zarządzania tożsamościami użytkowników i ich uwierzytelniać.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 716cf9e47cd71d003513066d390f9dccb5c83dcb
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344130"
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Usługa Azure AD B2C: Zaloguj się przy użyciu aplikacji systemu Android

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Standardy te umożliwiają korzystanie z każdej biblioteki, którą chcesz zintegrować z usługi Azure Active Directory B2C. Aby ułatwić Ci korzystać z innych bibliotek, umożliwia wskazówki podobny do poniższego pokazują, jak skonfigurować 3 biblioteki innej firmy, aby nawiązać połączenie z platformą Microsoft identity. Większość bibliotek implementujących [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) może nawiązać połączenia z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft udostępnia poprawki dla 3 innych firm, biblioteki i nie zrobił przeglądu tych bibliotek. W tym przykładzie używa 3 biblioteki innej firmy o nazwie AppAuth, który został przetestowany na zgodność w podstawowych scenariuszy z usługą Azure AD B2C. Problemy i sugestie funkcji powinny być kierowane do biblioteki typu open-source projektu. Zobacz [w tym artykule](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) Aby uzyskać więcej informacji.  
>
>

Jeśli dopiero rozpoczynasz korzystanie ze standardu OAuth2 lub OpenID Connect, spora część tej przykładowej konfiguracji może być dla Ciebie niezrozumiała. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu do usługi Azure AD będą przekazywane informacje wymagane do bezpiecznego komunikowania się z aplikacją. Aby utworzyć aplikację mobilną, postępuj zgodnie z [w instrukcjach](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **Native Client** w aplikacji.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie on potrzebny później.
* Konfigurowanie natywnego klienta **identyfikator URI przekierowania** (np. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). On również będzie później potrzebny.

## <a name="create-your-policies"></a>Tworzenie zasad

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ta aplikacja zawiera jedno rozwiązanie tożsamości: połączonego logowania i rejestracji. Należy utworzyć te zasady, zgodnie z opisem w [artykule dotyczącym struktury zasad](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Podczas tworzenia zasad należy:

* Wybierz **nazwę wyświetlaną** jako atrybut rejestracji w zasadach.
* Wybrać oświadczenia aplikacji **Nazwa wyświetlana** oraz **Identyfikator obiektu** we wszystkich zasadach. Można również wybrać inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Powinny zawierać prefiks `b2c_1_`.  Nazwa zasad będzie potrzebna później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po utworzeniu zasad będzie można rozpocząć tworzenie aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Udostępniliśmy próbkę pracy z zastosowaniem AppAuth za pomocą usługi Azure AD B2C [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Możesz pobrać kod i uruchom go. Możesz szybko rozpocząć pracę z własną aplikację przy użyciu konfiguracji usługi Azure AD B2C, postępując zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Próbka jest modyfikacja przykładowym przez [AppAuth](https://openid.github.io/AppAuth-Android/). Odwiedź ich stronę, aby dowiedzieć się więcej na temat AppAuth i jego funkcji.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji za pomocą usług Azure AD B2C AppAuth

> [!NOTE]
> AppAuth obsługuje Android API 16 (Jellybean) i nowszych. Firma Microsoft zaleca używanie interfejsu API 23 i nowsze wersje.
>

### <a name="configuration"></a>Konfiguracja

Komunikację można skonfigurować w usłudze Azure AD B2C, określając odnajdywanie identyfikatora URI lub określając punkt końcowy autoryzacji i tokena identyfikatory URI punktów końcowych. W obu przypadkach są potrzebne następujące informacje:

* Identyfikator dzierżawy (np. contoso.onmicrosoft.com)
* Nazwa zasad (np. usługi B2C\_1\_SignUpIn)

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

Firma Microsoft zawsze są otwarte opinie i sugestie! Jeśli mają trudności z tym artykułem lub poprawić tę zawartość, prosimy o wyrażenie opinii na dole strony. Żądania funkcji, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

