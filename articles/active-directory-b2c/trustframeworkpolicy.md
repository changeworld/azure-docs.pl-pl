---
title: TrustFrameworkPolicy — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Określ element TrustFrameworkPolicy zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186390"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zasady niestandardowe są reprezentowane jako jeden lub więcej plików w formacie XML, które odwołują się do siebie w łańcuchu hierarchicznym. Elementy XML definiują elementy zasad, takie jak schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawcy oświadczeń, profile techniczne, proces użytkownika i kroki aranżacji. Każdy plik zasad jest zdefiniowany w ramach najwyższego poziomu **TrustFrameworkPolicy** element pliku zasad.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**Element TrustFrameworkPolicy** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
|---------- | -------- | ----------- |
| PolitykaSchemaVersion | Tak | Wersja schematu, która ma być używana do wykonywania zasad. Wartość musi być`0.3.0.0` |
| Identyfikatorobiektobiekt dzierżawy | Nie | Unikatowy identyfikator obiektu dzierżawy usługi Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Tak | Unikatowy identyfikator dzierżawy, do której należy ta zasada. |
| PolicyId | Tak | Unikatowy identyfikator zasad. Ten identyfikator musi być poprzedzony *B2C_1A_* |
| Polityka publiczna | Tak | Identyfikator URI dla zasad, który jest kombinacją identyfikatora dzierżawy i identyfikatora zasad. |
| Tryb wdrażania | Nie | Możliwe wartości: `Production` `Development`, lub . Wartość domyślna to `Production`. Ta właściwość służy do debugowania zasad. Aby uzyskać więcej informacji, zobacz [Zbieranie dzienników](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Nie | Punkt końcowy używany, gdy **DeploymentMode** `Development`jest ustawiony na . Wartość musi `urn:journeyrecorder:applicationinsights`być . Aby uzyskać więcej informacji, zobacz [Zbieranie dzienników](troubleshoot-with-application-insights.md). |


W poniższym przykładzie pokazano, jak określić **TrustFrameworkPolicy** element:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Model dziedziczenia

Te typy plików zasad są zwykle używane w podróży użytkownika:

- Plik **podstawowy,** który zawiera większość definicji. Aby ułatwić rozwiązywanie problemów i długoterminowe utrzymanie zasad, zaleca się wprowadzenie minimalnej liczby zmian w tym pliku.
- Plik **Rozszerzeń,** który zawiera unikatowe zmiany konfiguracji dla dzierżawy. Ten plik zasad pochodzi z pliku podstawowego. Ten plik służy do dodawania nowych funkcji lub zastępowania istniejących funkcji. Na przykład użyj tego pliku do federacji z nowymi dostawcami tożsamości.
- Plik **jednostki uzależniona (RP),** który jest pojedynczym plikiem skoncentrowanym na zadaniach, który jest wywoływany bezpośrednio przez aplikację jednostki uzależniona, taką jak aplikacje sieci web, urządzenia przenośne lub klasyczne. Każde unikatowe zadanie, takie jak rejestracja lub logowanie, resetowanie hasła lub edycja profilu, wymaga własnego pliku zasad RP. Ten plik zasad pochodzi z pliku Rozszerzenia.

Aplikacja jednostki uzależniającej wywołuje plik zasad RP w celu wykonania określonego zadania. Na przykład, aby zainicjować przepływ logowania. Struktura środowiska tożsamości w usłudze Azure AD B2C dodaje wszystkie elementy najpierw z pliku podstawowego, a następnie z pliku Rozszerzeń, a na koniec z pliku zasad RP, aby zebrać bieżące zasady w efekcie. Elementy tego samego typu i nazwy w pliku RP zastępują te elementy w rozszerzeniach, a rozszerzenia zastępują base. Na poniższym diagramie przedstawiono relację między plikami zasad a aplikacjami jednostki uzależniającej.

![Diagram przedstawiający model dziedziczenia zasad struktury zaufania](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dziedziczenia jest następujący:

- Zasady nadrzędne i zasady podrzędne mają ten sam schemat.
- Zasady podrzędne na dowolnym poziomie można dziedziczyć z zasad nadrzędnych i rozszerzyć go przez dodanie nowych elementów.
- Nie ma ograniczeń co do liczby poziomów.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md).

## <a name="base-policy"></a>Zasady podstawowe

Aby dziedziczyć zasady z innej zasady, **BasePolicy** element musi być zadeklarowany w **ramach TrustFrameworkPolicy** element pliku zasad. **BasePolicy** Element jest odwołanie do zasad podstawowych, z których tej zasady jest pochodną.

**Element BasePolicy** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identyfikator dzierżawy usługi Azure AD B2C. |
| PolicyId | 1:1 | Identyfikator zasady nadrzędnej. |


W poniższym przykładzie pokazano, jak określić zasady podstawowe. Ta **polityka B2C_1A_TrustFrameworkExtensions** jest pochodną polityki **B2C_1A_TrustFrameworkBase.**

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Wykonywanie zasad

Aplikacja jednostki uzależniającej, taka jak aplikacja internetowa, mobilna lub klasyczna, wywołuje [zasady jednostki uzależniającej (RP).](relyingparty.md) Plik zasad RP wykonuje określone zadanie, takie jak logowanie się, resetowanie hasła lub edytowanie profilu. Zasady RP konfiguruje listę oświadczeń aplikacji jednostki uzależniona odbiera jako część tokenu, który jest wystawiany. Wiele aplikacji może używać tych samych zasad. Wszystkie aplikacje otrzymują ten sam token z oświadczeń, a użytkownik przechodzi przez tę samą podróż użytkownika. Pojedyncza aplikacja może używać wielu zasad.

Wewnątrz pliku zasad RP należy określić element **DefaultUserJourney,** który wskazuje [userjourney](userjourneys.md). Podróż użytkownika jest zwykle zdefiniowana w zasadach Podstawowe lub Rozszerzenia.

B2C_1A_signup_signin polityka:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase lub B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Podróż użytkownika definiuje logikę biznesową tego, przez co przechodzi użytkownik. Każda podróż użytkownika jest zestawem kroków aranżacji, który wykonuje serię akcji, w kolejności pod względem uwierzytelniania i zbierania informacji.

**SocialAndLocalAccounts** plik zasad w [pakiecie startowym](custom-policy-get-started.md#custom-policy-starter-pack) zawiera SignUpOrSignIn, ProfileEdit, PasswordReset podróży użytkownika. Możesz dodać więcej podróży użytkowników dla innych scenariuszy, takich jak zmiana adresu e-mail lub łączenie i odłączanie konta społecznościowego.

Kroki aranżacji mogą wywoływać [profil techniczny](technicalprofiles.md). Profil techniczny zapewnia strukturę z wbudowanym mechanizmem komunikacji z różnymi typami stron. Na przykład profil techniczny może wykonywać następujące działania między innymi:

- Renderowanie środowiska użytkownika.
- Zezwalaj użytkownikom na logowanie się za pomocą konta społecznościowego lub korporacyjnego, takiego jak Facebook, konto Microsoft, Google, Salesforce lub inny dostawca tożsamości.
- Skonfiguruj weryfikację telefoniczną dla usługi MFA.
- Odczytywanie i zapisywanie danych do i z magazynu tożsamości usługi Azure AD B2C.
- Wywołanie niestandardowej usługi interfejsu API Restful.

![Diagram przedstawiający przepływ wykonywania zasad](./media/trustframeworkpolicy/custom-policy-execution.png)

 **Element TrustFrameworkPolicy** zawiera następujące elementy:

- Polityka podstawowa, jak określono powyżej
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
