---
title: ContentDefinitions — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element ContentDefinitions zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82785a0f833afb6a9c675fc7022ed19e96c7fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511314"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Można dostosować wygląd i działanie dowolnego [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md). Usługa Azure Active Directory (Azure AD) B2C uruchamia kod w przeglądarce klienta i używa się nowoczesnym podejściem o nazwie udostępniania zasobów między źródłami (CORS). 

Aby dostosować interfejs użytkownika, należy określić adres URL w **ContentDefinition** element z dostosowaną zawartość HTML. W profilu technicznym samodzielnie lub **OrchestrationStep**, wskaż identyfikatora definicji zawartości. Może zawierać definicję zawartości **LocalizedResourcesReferences** element, który określa listę zlokalizowane zasoby do załadowania. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę użytkownikowi.

**ContentDefinitions** element zawiera adresy URL do szablonów HTML5, które mogą być używane w podróży użytkownika. Identyfikator URI strony HTML5 jest używany dla kroku interfejs określonego użytkownika. Na przykład Resetowanie hasła logowania lub tworzenia konta, lub stron błędów. Możesz zmodyfikować wygląd i działanie przez zastąpienie parametr LoadUri dla pliku HTML5. Możesz utworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać odwołanie zlokalizowane zasoby na identyfikator lokalizacji określone w [lokalizacji](localization.md) elementu.

Poniższy przykład przedstawia, identyfikator definicji zawartości, jak i definicja zlokalizowane zasoby:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadane **LocalAccountSignUpWithLogonEmail** własnym potwierdzone profil techniczny zawiera identyfikator definicji zawartości **ContentDefinitionReferenceId** równa `api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```


## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator definicji zawartości. Wartość jest określona w **zawartości identyfikatorów definicji** sekcję w dalszej części tej strony. |

**ContentDefinition** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Ciąg, który zawiera adres URL strony HTML5 do definicji zawartości. |
| RecoveryUri | 0:1 | Ciąg, który zawiera adres URL strony HTML do wyświetlania wystąpił błąd związany z definicji zawartości. | 
| DataUri | 1:1 | Ciąg, który zawiera względny adres URL pliku HTML, który zapewnia środowisko użytkownika, aby wywołać ten krok. |  
| Metadane | 1:1 | Kolekcja par klucz wartość zawiera metadane wykorzystywane przez definicję zawartości. | 
| LocalizedResourcesReferences | 0:1 | Kolekcja odwołań do zlokalizowanych zasobów. Użyj tego elementu, aby dostosować lokalizację atrybutu interfejsu i oświadczenia użytkownika. |

### <a name="datauri"></a>DataUri

**Identyfikator URI** element jest używany do określania identyfikator strony. Usługa Azure AD B2C używa identyfikatora strony do ładowania i Inicjowanie elementów interfejsu użytkownika i języka JavaScript po stronie klienta. Format wartości jest `urn:com:microsoft:aad:b2c:elements:page-name:version`.  W poniższej tabeli przedstawiono identyfikatorów stron, których można użyć.

| Wartość |   Opis |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Wyświetla stronę błędu, gdy występuje wyjątek lub komunikat o błędzie. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Wyświetla listę dostawców tożsamości, które użytkownicy mogą wybierać podczas logowania. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Przedstawia formularz logujesz się przy użyciu konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Ta wartość zapewnia również "Chcę funkcji logowania" i "Nie pamiętasz hasła?" link. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Przedstawia formularz logujesz się przy użyciu konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Sprawdza numery telefonów, przy użyciu tekstowych lub głosowych podczas tworzenia konta lub logowania. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Zostanie wyświetlony formularz, który umożliwia użytkownikom, można utworzyć lub zaktualizować swój profil. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Lista odwołania do zlokalizowanych zasobów dla definicji zawartości. | 

**LocalizedResourcesReferences** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Język | Tak | Ciąg, który zawiera obsługiwanego języka dla zasad zgodnie z RFC 5646 - znaczniki identyfikacji języków. |
| LocalizedResourcesReferenceId | Tak | Identyfikator **LocalizedResources** elementu. |

Poniższy przykład przedstawia definicję zawartości rejestracji lub logowania:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Poniższy przykład przedstawia definicję zawartości rejestracji lub logowania z odwołaniem do lokalizacji na angielski, francuski i hiszpański:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Aby dowiedzieć się, jak dodać obsługę lokalizacji do definicji zawartości, zobacz [lokalizacji](localization.md).

## <a name="content-definition-ids"></a>Identyfikatorów definicji zawartości

Atrybut ID **ContentDefinition** element określa typ strony, które odnoszą się do definicji zawartości. Element definiuje kontekst, który zamierza stosowanie niestandardowy szablon HTML5/CSS. W poniższej tabeli opisano zestaw definicji zawartości identyfikatorów, który jest rozpoznawany przez platformy środowiska tożsamości i typy stron, które odnoszą się do nich. Można utworzyć definicji zawartości przy użyciu dowolnego identyfikatora.

| ID | Szablon domyślny | Opis | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu** — wyświetla błąd strony, gdy wyjątek lub wystąpi błąd. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości** — Wyświetla listę dostawców tożsamości, które użytkownicy mogą wybierać podczas logowania. Opcje są zazwyczaj enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości dla rejestracji** — Wyświetla listę dostawców tożsamości, które użytkownicy mogą wybierać podczas rejestracji. Opcje są zazwyczaj enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona obsługi zapomnianego hasła** -Wyświetla formularz, który użytkownicy muszą wykonać, aby zainicjować resetowania hasła. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania dla kont lokalnych** — przedstawia formularz logujesz się przy użyciu konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola wprowadzania tekstu, a pole wprowadzania hasła. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji dla kont lokalnych** -Wyświetla formularz za utworzenie konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz mogą zawierać różne kontrolki wejściowe, takie jak: tekst wejściowy pola, pole wprowadzania hasła, przycisk radiowy, pola listy rozwijanej wybierz jedną, a następnie zaznacz pola wyboru. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego** — sprawdza, numerów telefonów, przy użyciu tekstowych lub głosowych, podczas tworzenia konta lub logowania. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji dla kont społecznościowych** -Wyświetla formularz, który użytkownicy muszą wykonać po utworzeniu konta przy użyciu istniejącego konta z dostawcy tożsamości społecznościowych. Ta strona jest podobny do poprzedniego nowego konta społecznościowego stronę z wyjątkiem pól wprowadzania hasła. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu** -Wyświetla formularz, który użytkownicy mogą uzyskać dostęp, aby zaktualizować swój profil. Ta strona jest podobne do nowego konta społecznościowego stronę z wyjątkiem pól wprowadzania hasła. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona strona rejestracji lub logowania** — obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą korzystać przedsiębiorstwa dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych. |
 
