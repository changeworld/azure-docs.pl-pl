---
title: Uzyskiwanie tokenu przy użyciu aplikacji systemu Android w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia aplikacji systemu Android AppAuth z usługi Azure Active Directory B2C jest używana do zarządzania tożsamościami użytkowników i uwierzytelniania użytkowników.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c0a72e03eaa8d12c26b1bbbf6a05b4d94e72358
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709910"
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Usługa Azure AD B2C: Zaloguj się przy użyciu aplikacji systemu Android

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Standardy te umożliwiają wykorzystać wszystkie biblioteki, który chcesz zintegrować z usługi Azure Active Directory B2C. Aby korzystać z innych bibliotek umożliwia wskazówki, takiego jak to pokazano sposób konfigurowania 3 bibliotek strona nawiązać połączenia z platformą tożsamości Microsoft. Większość bibliotek, które implementują [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) może nawiązać połączenie z platformy Microsoft Identity.

> [!WARNING]
> Firma Microsoft zapewnia poprawki dla 3rd strony biblioteki i nie przeprowadził Przegląd tych bibliotek. W tym przykładzie używa 3 biblioteki firmy o nazwie AppAuth, które zostały przetestowane na zgodność w podstawowe scenariusze w usłudze Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do biblioteki projektu open source. Zobacz [w tym artykule](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) Aby uzyskać więcej informacji.  
>
>

Jeśli dopiero rozpoczynasz korzystanie ze standardu OAuth2 lub OpenID Connect, spora część tej przykładowej konfiguracji może być dla Ciebie niezrozumiała. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu do usługi Azure AD będą przekazywane informacje wymagane do bezpiecznego komunikowania się z aplikacją. Aby utworzyć aplikacji mobilnej, wykonaj [tych instrukcji](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **Native Client** w aplikacji.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie on potrzebny później.
* Konfigurowanie klienta natywnego **identyfikator URI przekierowania** (np. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). On również będzie później potrzebny.

## <a name="create-your-policies"></a>Tworzenie zasad

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ta aplikacja zawiera jeden obsługi tożsamości: połączone logowania i rejestrowania. Należy utworzyć te zasady, zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Podczas tworzenia zasad należy:

* Wybierz **Nazwa wyświetlana** jako atrybut rejestracji w zasadach.
* Wybrać oświadczenia aplikacji **Nazwa wyświetlana** oraz **Identyfikator obiektu** we wszystkich zasadach. Można również wybrać inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Powinny zawierać prefiks `b2c_1_`.  Nazwa zasad będzie potrzebna później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po utworzeniu zasad będzie można rozpocząć tworzenie aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Firma Microsoft umieściła próbki pracy, który korzysta z usługi Azure AD B2C AppAuth [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Można go pobrać i uruchom go. Możesz szybko rozpocząć pracę z własną aplikację przy użyciu konfiguracji usługi Azure AD B2C, postępując zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Próbka jest modyfikacji próbki dostarczonych przez [AppAuth](https://openid.github.io/AppAuth-Android/). Odwiedź ich strony, aby dowiedzieć się więcej o AppAuth i ich funkcje.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji do użycia usługi Azure AD B2C z AppAuth

> [!NOTE]
> AppAuth obsługuje 16 interfejsu API systemu Android (Jellybean) lub nowszym. Firma Microsoft zaleca używanie 23 interfejsu API i powyżej.
>

### <a name="configuration"></a>Konfigurowanie

Komunikację można skonfigurować z usługi Azure AD B2C, określając odnajdywania identyfikatora URI lub określając punktu końcowego autoryzacji i punktu końcowego tokena identyfikatorów URI. W obu przypadkach należy następujące informacje:

* Identyfikator dzierżawcy (np. contoso.onmicrosoft.com)
* Nazwa zasad (np. B2C\_1\_SignUpIn)

Jeśli wybierzesz opcję automatycznego wykrywania autoryzacji i tokena punktu końcowego identyfikatory URI, konieczne będzie pobranie informacji dotyczących odnajdywania identyfikatora URI. Odnajdywania, zastępując dzierżawcy można wygenerować identyfikatora URI\_identyfikator i zasady\_nazwy w następujący adres URL:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Można uzyskać autoryzacji i tokena punktu końcowego identyfikatorów URI i utworzenia obiektu AuthorizationServiceConfiguration, uruchamiając następujące:

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

Zamiast używać odnajdywania uzyskanie autoryzacji i tokena punktu końcowego identyfikatory URI, można również określić je jawnie przez zastąpienie dzierżawcy\_identyfikator i zasady\_nazwą w adresie URL poniżej:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po Konfigurowanie lub pobieranie konfiguracji usługi autoryzacji, można utworzyć żądania autoryzacji. Aby utworzyć żądanie, są potrzebne następujące informacje:

* Identyfikator klienta (np. 00000000-0000-0000-0000-000000000000)
* Identyfikator URI przekierowania z schematu niestandardowego (np. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Oba elementy powinny zostały zapisane, gdy użytkownik [rejestrowanie aplikacji](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Zapoznaj się z [przewodnik AppAuth](https://openid.github.io/AppAuth-Android/) na temat sposobu ukończenia resztę procesu. Jeśli potrzebujesz szybko rozpocząć pracę z aplikacją pracy, zapoznaj się [naszej próbki](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postępuj zgodnie z instrukcjami [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) wprowadzić konfigurację usługi Azure AD B2C.

Firma Microsoft zawsze są otwarte na opinie i sugestie! Jeśli masz trudności w tym artykule, lub mają zalecenia dotyczące poprawy tej zawartości, prosimy o wyrażenie opinii na dole strony. Funkcja żądań, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

