---
title: REST API twierdzi, że wymiany w zasadach niestandardowych B2C
titleSuffix: Azure AD B2C
description: Wprowadzenie do tworzenia usługi Azure AD B2C podróży użytkownika, który współdziała z usług RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337417"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrowanie wymiany oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Struktura środowiska tożsamości, która stanowi podstawę usługi Azure Active Directory B2C (Azure AD B2C), można zintegrować z interfejsami API RESTful w ramach podróży użytkownika. W tym artykule pokazano, jak utworzyć proces użytkownika, który współdziała z usługą RESTful przy użyciu [profilu technicznego RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Korzystając z usługi Azure AD B2C, można dodać własną logikę biznesową do podróży użytkownika, wywołując własną usługę RESTful. Struktura środowiska tożsamości może wysyłać i odbierać dane z usługi RESTful w celu wymiany oświadczeń. Można na przykład:

- **Sprawdzanie poprawności danych wejściowych użytkownika**. Na przykład można sprawdzić, czy adres e-mail podany przez użytkownika istnieje w bazie danych klienta, a jeśli nie, przedstawić błąd.
- **Przetwarzanie oświadczeń**. Jeśli użytkownik wprowadzi swoje imię wielkimi literami lub wielkimi literami, interfejs API REST może sformatować nazwę tylko pierwszą literą wielką literą i zwrócić ją do usługi Azure AD B2C.
- **Wzbogać dane użytkowników, integrując się z aplikacjami biznesowymi.** Usługa RESTful może odbierać adres e-mail użytkownika, wysyłać zapytania do bazy danych klienta i zwracać numer lojalnościowy użytkownika do usługi Azure AD B2C. Następnie oświadczenia zwrotne mogą być przechowywane na koncie usługi Azure AD użytkownika, oceniane w następnych krokach aranżacji lub uwzględnione w tokenie dostępu.
- **Uruchamianie niestandardowej logiki biznesowej**. Można wysyłać powiadomienia wypychane, aktualizować firmowe bazy danych, uruchamiać proces migracji użytkowników, zarządzać uprawnieniami, przeprowadzać inspekcje baz danych i wykonywać inne przepływy pracy.

![Diagram wymiany oświadczeń o świadczenie usług RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Wywoływanie usługi RESTful

Interakcja obejmuje wymianę oświadczeń informacji między oświadczeniami interfejsu API REST i usługą Azure AD B2C. Integrację z usługami RESTful można zaprojektować w następujący sposób:

- **Potwierdzenie profilu technicznego**. Wywołanie usługi RESTful odbywa się w [ramach profilu technicznego sprawdzania poprawności](validation-technical-profile.md) określonego samodzielnie [potwierdzonego profilu technicznego](self-asserted-technical-profile.md)lub kontroli [wyświetlania weryfikacyjnego](display-control-verification.md) [sterowania wyświetlaniem.](display-controls.md) Profil techniczny sprawdzania poprawności sprawdza poprawność danych dostarczonych przez użytkownika, zanim podróż użytkownika zostanie przesuniętą do przodu. Dzięki profilowi techniczneemu sprawdzania poprawności można:

  - Wyślij oświadczenia do interfejsu API REST.
  - Sprawdź poprawność oświadczeń i zgłaszaj niestandardowe komunikaty o błędach, które są wyświetlane użytkownikowi.
  - Wyślij oświadczenia z interfejsu API REST do kolejnych kroków aranżacji.

- **Wymiana roszczeń**. Bezpośrednią wymianę oświadczeń można skonfigurować, wywołując profil techniczny interfejsu API REST bezpośrednio z etapu aranżacji [podróży użytkownika.](userjourneys.md) Definicja ta jest ograniczona do:

  - Wyślij oświadczenia do interfejsu API REST.
  - Sprawdź poprawność oświadczeń i zgłaszaj niestandardowe komunikaty o błędach, które są zwracane do aplikacji.
  - Wyślij oświadczenia z interfejsu API REST do kolejnych kroków aranżacji.

Wywołanie interfejsu API REST można dodać na dowolnym etapie podróży użytkownika zdefiniowanego przez zasady niestandardowe. Na przykład można wywołać interfejs API REST:

- Podczas logowania, tuż przed usłudze Azure AD B2C sprawdza poprawność poświadczeń.
- Natychmiast po zalogowaniu.
- Zanim usługa Azure AD B2C utworzy nowe konto w katalogu.
- Po usłudze Azure AD B2C tworzy nowe konto w katalogu.
- Zanim usługa Azure AD B2C wyda token dostępu.

![Sprawdzanie poprawności kolekcji profilów technicznych](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Wysyłanie danych

W [profilu technicznym RESTful](restful-technical-profile.md) `InputClaims` element zawiera listę oświadczeń do wysłania do usługi RESTful. Nazwę oświadczenia można mapować na nazwę zdefiniowaną w usłudze RESTful, ustawić wartość domyślną i użyć [programów rozpoznawania oświadczeń](claim-resolver-overview.md).

Można skonfigurować sposób wysyłania oświadczeń wejściowych do dostawcy oświadczeń RESTful przy użyciu atrybutu SendClaimsIn. Możliwe wartości są następujące:

- **Treść**, wysłana w treści żądania HTTP POST w formacie JSON.
- **Formularz**, wysłany w treści żądania HTTP POST w formacie & "&" oddzielony.
- **Nagłówek**, wysłany w nagłówku żądania HTTP GET.
- **QueryString**, wysłane w ciągu zapytania żądania HTTP GET.

Po skonfigurowaniu opcji **Body** profil techniczny interfejsu API REST umożliwia wysłanie złożonego ładunku JSON do punktu końcowego. Aby uzyskać więcej informacji, zobacz [Wysyłanie ładunku JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Odbieranie danych

Element `OutputClaims` profilu [technicznego RESTful](restful-technical-profile.md) zawiera listę oświadczeń zwróconych przez interfejs API REST. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w interfejsie API REST. Można również dołączyć oświadczenia, które nie są zwracane przez dostawcę tożsamości interfejsu API REST, tak długo, jak ustawić DefaultValue atrybut.

Oświadczenia wyjściowe analizowane przez dostawcę oświadczeń RESTful zawsze oczekują, aby przeanalizować płaską odpowiedź obiektu JSON, takich jak:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Oświadczenia wyjściowe powinny wyglądać następująco:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Aby przeanalizować zagnieżdżoną odpowiedź obiektu JSON, ustaw metadane ResolveJsonPathsInJsonTokens na true. W żądaniu danych wyjściowych ustaw Element PartnerClaimType na element ścieżki JSON, który chcesz wyprowadzić.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Oświadczenia wyjściowe powinny wyglądać następująco:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy chronić punkt końcowy interfejsu API REST, aby tylko uwierzytelnieni klienci mogli się z nim komunikować. Interfejs API REST musi używać punktu końcowego HTTPS. Ustaw metadane AuthenticationType na jedną z następujących metod uwierzytelniania:

- **Certyfikat klienta** ogranicza dostęp przy użyciu uwierzytelniania certyfikatu klienta. Tylko usługi, które mają odpowiednie certyfikaty, mogą uzyskiwać dostęp do interfejsu API. Certyfikat klienta jest przechowywany w kluczu zasad usługi Azure AD B2C. Dowiedz się więcej o [zabezpieczaniu usługi RESTful przy użyciu certyfikatów klientów.](secure-rest-api.md#https-client-certificate-authentication)
- **Basic** zabezpiecza interfejs API REST za pomocą uwierzytelniania podstawowego HTTP. Tylko zweryfikowani użytkownicy, w tym usługa Azure AD B2C, mogą uzyskać dostęp do interfejsu API. Nazwa użytkownika i hasło są przechowywane w kluczach zasad usługi Azure AD B2C. Dowiedz się, jak [zabezpieczyć usługi RESTful przy użyciu uwierzytelniania podstawowego HTTP.](secure-rest-api.md#http-basic-authentication)
- **Nośnik** ogranicza dostęp przy użyciu tokenu dostępu klienta OAuth2. Token dostępu jest przechowywany w kluczu zasad usługi Azure AD B2C. Dowiedz się więcej o tym, jak [zabezpieczyć swoją usługę RESTful za pomocą tokena na okaziciela.](secure-rest-api.md#oauth2-bearer-authentication)

## <a name="rest-api-platform"></a>Platforma API REST
Interfejs API REST może być oparty na dowolnej platformie i napisany w dowolnym języku programowania, o ile jest bezpieczny i może wysyłać i odbierać roszczenia określone w [profilu technicznym RESTful.](restful-technical-profile.md)

## <a name="localize-the-rest-api"></a>Lokalizuj interfejs API REST
W profilu technicznym RESTful można wysłać język/ustawienia regionalne bieżącej sesji, a w razie potrzeby podnieść zlokalizowany komunikat o błędzie. Za pomocą [rozpoznawania oświadczeń,](claim-resolver-overview.md)można wysłać oświadczenie kontekstowe, takie jak język użytkownika. W poniższym przykładzie przedstawiono profil techniczny RESTful demonstrujący ten scenariusz.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Obsługa komunikatów o błędach

Interfejs API REST może wymagać zwrócenia komunikatu o błędzie, takiego jak "Użytkownik nie został znaleziony w systemie CRM". Jeśli wystąpi błąd, interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409 (kod stanu odpowiedzi na konflikt). Aby uzyskać więcej informacji, zobacz [profil techniczny RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Można to osiągnąć tylko poprzez wywołanie profilu technicznego interfejsu API REST z profilu technicznego sprawdzania poprawności. Dzięki temu użytkownik może poprawić dane na stronie i uruchomić sprawdzanie poprawności ponownie po przesłaniu strony.

Odpowiedź HTTP 409 jest wymagana, aby zapobiec przetwarzaniu kolejnych profilów technicznych sprawdzania poprawności w tym kroku aranżacji.

Jeśli odwołujesz się do profilu technicznego interfejsu API REST bezpośrednio z podróży użytkownika, użytkownik zostanie przekierowany z powrotem do aplikacji jednostki uzależniona z odpowiednim komunikatem o błędzie.

## <a name="publishing-your-rest-api"></a>Publikowanie interfejsu API REST

Żądanie do usługi interfejsu API REST pochodzi z serwerów usługi Azure AD B2C. Usługa interfejsu API REST musi zostać opublikowana w publicznie dostępnym punkcie końcowym HTTPS. Wywołania interfejsu API REST będą odbierane z adresu IP centrum danych platformy Azure.

Zaprojektuj usługę interfejsu API REST i jej podstawowe składniki (takie jak baza danych i system plików), aby były wysoce dostępne.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami korzystania z profilu technicznego RESTful:

- [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md)
- [Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpieczanie usług INTERFEJSU API REST](secure-rest-api.md)
- [Referencje: RESTful profil techniczny](restful-technical-profile.md)