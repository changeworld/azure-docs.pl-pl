---
title: Elementu TrustFrameworkPolicy — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element elementu TrustFrameworkPolicy zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 558e9c3a3bfd43f6ceb958bc3be55d58e1eb7f91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360490"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Niestandardowe zasady jest przedstawiana jako jeden lub więcej plików w formacie XML, które odnoszą się do siebie nawzajem w łańcuchu hierarchicznej. Elementy XML, Zdefiniuj elementy zasad, takich jak schemat oświadczenia, przekształcenia oświadczeń, definicji zawartości, dostawców oświadczeń, profile techniczne, podróży użytkownika i kroki aranżacji. Każdy plik zasad jest zdefiniowany w obrębie najwyższego poziomu **elementu TrustFrameworkPolicy** element pliku zasad. 

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


**Elementu TrustFrameworkPolicy** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Yes | Wersja schematu, który ma być używany do wykonywania zasad. Wartość musi być `0.3.0.0` |
| TenantObjectId | Nie | Unikatowy identyfikator dzierżawy usługi Azure Active Directory (Azure AD) B2C. |
| TenantId | Yes | Unikatowy identyfikator dzierżawy, do której należy ta zasada. |
| PolicyId | Yes | Unikatowy identyfikator zasad. Ten identyfikator musi być poprzedzony *B2C_1A_* |
| PublicPolicyUri | Yes | Identyfikator URI dla zasad, które jest kombinacja Identyfikatora dzierżawy oraz identyfikatora zasad. |
| DeploymentMode | Nie | Możliwe wartości: `Production`, `Debugging`, lub `Development`. Wartość domyślna to `Production`. Ta właściwość służy do debugowania zasady. Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Nie | Punkt końcowy, który jest używany podczas **DeploymentMode** ustawiono `Development`. Wartość musi być `urn:journeyrecorder:applicationinsights`. Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md). |


Poniższy przykład pokazuje, jak określić **elementu TrustFrameworkPolicy** elementu:

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

Te typy plików zasad są zazwyczaj używane w podróży użytkownika:

- A **Base** pliku, który zawiera większość definicje. Aby ułatwić rozwiązywanie problemów i długoterminowe konserwacji zasad, zalecane jest, wprowadzić minimalną liczbę zmian do tego pliku.
- **Rozszerzenia** pliku, który zawiera zmiany konfiguracji unikatowy dla Twojej dzierżawy. Ten plik zasad jest tworzony na podstawie pliku podstawowego. Użyj tego pliku, aby dodać nowe funkcje lub zastąpienie istniejących funkcji. Na przykład można użyć tego pliku federowania z Nowi dostawcy tożsamości.
- A **jednostki uzależnionej strona (RP)** pliku pojedynczy plik skoncentrowane na zadanie, który jest wywoływany bezpośrednio przez aplikację jednostki uzależnionej, takich jak aplikacje sieci web, mobilnych i klasycznych. Każde zadanie unikatowy, takie jak resetowanie haseł rejestracji lub logowania, lub edytowania profilu, wymaga swój własny plik zasad jednostki Uzależnionej. Ten plik zasad jest tworzony na podstawie pliku rozszerzenia. 

Aplikację jednostki uzależnionej wywołuje plik zasad RP do wykonania konkretnego zadania. Na przykład, aby zainicjować przepływ logowania. Struktura środowiska tożsamości w usłudze Azure AD B2C dodaje wszystkie elementy w najpierw z plikiem podstawowym, a następnie rozszerzenia pliku, a na koniec z pliku zasad jednostki Uzależnionej można złożyć obowiązuje bieżące zasady. Elementy tego samego typu i nazwy w pliku RP zastąpić te elementy w rozszerzeniach, a rozszerzenia zastępuje Base. Na poniższym diagramie przedstawiono relację między plikami zasad i aplikacji jednostki uzależnionej strona.

![Model dziedziczenia](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dziedziczenia jest następująca:

- Zasady nadrzędne i podrzędne zasad mają ten sam schemat.
- Zasady podrzędne na dowolnym poziomie można dziedziczyć zasady nadrzędne i ją rozszerzyć, dodając nowe elementy.
- Nie ma żadnego limitu liczby poziomów.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Zasadach podstawowych

Dziedziczenie zasad z innych zasad **BasePolicy** element musi być zadeklarowany w obszarze **elementu TrustFrameworkPolicy** element pliku zasad. **BasePolicy** element jest odwołaniem do zasadach podstawowych, z którego pochodzi tych zasad.  

**BasePolicy** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identyfikator dzierżawy usługi Azure AD B2C. |
| PolicyId | 1:1 | Identyfikator zasady nadrzędne. |


Poniższy przykład pokazuje, jak określić zasadach podstawowych. To **B2C_1A_TrustFrameworkExtensions** zasad jest tworzony na podstawie **B2C_1A_TrustFrameworkBase** zasad. 

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

## <a name="policy-execution"></a>Zasady wykonywania

Wywołuje aplikację jednostki uzależnionej, np. aplikacji sieci web, mobilnych i klasycznych, [jednostki uzależnionej zasad firmy (RP)](relyingparty.md). Plik zasad RP wykonuje określonych zadań, takich jak logowanie, resetowanie hasła lub edycji profilu. Zasady RP umożliwiają skonfigurowanie listy oświadczenia, które aplikacji jednostki uzależnionej odbiera token wystawiony w ramach. Wiele aplikacji można użyć tych samych zasad. Wszystkie aplikacje otrzymywać tego samego tokenu przy użyciu oświadczeń i użytkownik przechodzi przez ten sam podróży użytkownika. Pojedynczej aplikacji można użyć wielu zasad.

Wewnątrz pliku zasad jednostki Uzależnionej, należy określić **DefaultUserJourney** element, który wskazuje na [UserJourney](userjourneys.md). Podróż użytkownika zwykle jest zdefiniowane w zasadach Base lub rozszerzenia.

Zasady B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase lub B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Podróż użytkownika definiuje użytkownik przechodzi przez logiki biznesowej. Każdy podróż użytkownika jest zestaw kroków aranżacji, który wykonuje szereg akcji w sekwencji w zakresie uwierzytelniania i informacje o pamięci. 

**SocialAndLocalAccounts** plik zasad w [pakiet startowy](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) zawiera SignUpOrSignIn, ProfileEdit, podróży użytkownika PasswordReset. Można dodać więcej podróży użytkownika dla innego scenariusze, takie jak zmiana adresu e-mail łączenie i rozłączanie kont społecznościowych lub resetowania hasła. 

Kroki orchestration może wywołać [profilu technicznego](technicalprofiles.md). Profil techniczny zapewnia platforma wbudowany mechanizm do komunikowania się z różnymi typami stron. Na przykład profilu technicznego mogą wykonywać te akcje, między innymi:

- Renderuj środowisko użytkownika.
- Zezwalaj użytkownikom na logowanie za pomocą społecznościowej lub konta organizacji, takich jak Facebook, konta Microsoft, Google, Salesforce lub innego dostawcy tożsamości.
- Skonfiguruj weryfikację telefoniczną dla usługi MFA.
- Odczyt i zapis danych do i z magazynu tożsamości usługi Azure AD B2C.
- Wywoływanie niestandardowych usługi interfejsu API Restful.

![Zasady wykonywania](./media/trustframeworkpolicy/custom-policy-execution.png)

 **Elementu TrustFrameworkPolicy** element zawiera następujące elementy:

- BasePolicy jak określono powyżej
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [Podróży użytkowników](userjourneys.md)
- [RelyingParty](relyingparty.md)

