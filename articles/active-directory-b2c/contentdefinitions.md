---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Określ element ContentDefinitions zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051489"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Możesz dostosować wygląd każdego samodzielnie [potwierdzonego profilu technicznego.](self-asserted-technical-profile.md) Usługa Azure Active Directory B2C (Azure AD B2C) uruchamia kod w przeglądarce klienta i używa nowoczesnego podejścia o nazwie Udostępnianie zasobów cross-origin (CORS).

Aby dostosować interfejs użytkownika, należy określić adres URL w **elemencie ContentDefinition** z dostosowaną zawartością HTML. W samodzielnie potwierdzonym profilu technicznym lub **aranżacjiStep**wskazujesz ten identyfikator definicji zawartości. Definicja zawartości może zawierać **LocalizedResourcesReferences** element, który określa listę zlokalizowanych zasobów do załadowania. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę użytkownikowi.

**Element ContentDefinitions** zawiera adresy URL do szablonów HTML5, które mogą być używane w podróży użytkownika. Identyfikator URI strony HTML5 jest używany dla określonego kroku interfejsu użytkownika. Na przykład strony logowania lub rejestracji, resetowania hasła lub strony błędów. Wygląd i działanie można zmodyfikować, zastępując loaduri dla pliku HTML5. Możesz tworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać odwołanie zlokalizowanych zasobów do identyfikatora lokalizacji określonego w [localization](localization.md) elementu.

W poniższym przykładzie przedstawiono identyfikator definicji zawartości i definicję zlokalizowanych zasobów:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadane **localaccountSignUpWithLogonEmail** samodzielnie potwierdzone profil techniczny zawiera identyfikator definicji zawartości **ContentDefinitionReferenceId** ustawiony na`api.localaccountsignup`

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

## <a name="contentdefinition"></a>Definicja zawartości

**Element ContentDefinition** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator definicji zawartości. Wartość jest określona w sekcji **Identyfikatory definicji zawartości** w dalszej części tej strony. |

**Element ContentDefinition** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LoadUri ( LoadUri ) | 1:1 | Ciąg zawierający adres URL strony HTML5 dla definicji zawartości. |
| RecoveryUri (OdzyskiwanieUri) | 1:1 | Ciąg zawierający adres URL strony HTML do wyświetlania błędu związanego z definicją zawartości. Nie jest obecnie używana, `~/common/default_page_error.html`wartość musi być . |
| DataUri ( DataUri ) | 1:1 | Ciąg zawierający względny adres URL pliku HTML, który zapewnia użytkownikowi środowisko do wywołania dla kroku. |
| Metadane | 0:1 | Kolekcja par klucz/wartość, która zawiera metadane używane przez definicję zawartości. |
| ZlokalizowaneResourcesReferences | 0:1 | Kolekcja zlokalizowanych odwołań do zasobów. Ten element służy do dostosowywania lokalizacji interfejsu użytkownika i atrybutu oświadczeń. |

### <a name="datauri"></a>DataUri ( DataUri )

Element **DataUri** służy do określania identyfikatora strony. Usługa Azure AD B2C używa identyfikatora strony do ładowania i inicjowania elementów interfejsu użytkownika i javascript po stronie klienta. Formatem wartości jest `urn:com:microsoft:aad:b2c:elements:page-name:version`. W poniższej tabeli wymieniono identyfikatory stron, których można użyć.

| Identyfikator strony | Opis |
| ----- | ----------- |
| `globalexception` | Wyświetla stronę błędu po napotkaniu wyjątku lub błędu. |
| `providerselection`, `idpselection` | Wyświetla listę dostawców tożsamości, z których użytkownicy mogą wybierać podczas logowania.  |
| `unifiedssp` | Wyświetla formularz do logowania się przy za pomocą konta lokalnego, który jest oparty na adresie e-mail lub nazwie użytkownika. Ta wartość zapewnia również "zachowaj mi funkcję logowania" i "Zapomniałeś hasła?" Link. |
| `unifiedssd` | Wyświetla formularz do logowania się przy za pomocą konta lokalnego, który jest oparty na adresie e-mail lub nazwie użytkownika. |
| `multifactor` | Weryfikuje numery telefonów za pomocą tekstu lub głosu podczas rejestracji lub logowania. |
| `selfasserted` | Wyświetla formularz do zbierania danych od użytkownika. Na przykład umożliwia użytkownikom tworzenie lub aktualizowanie ich profilu. |

### <a name="select-a-page-layout"></a>Wybieranie układu strony

[Kod po stronie klienta JavaScript](javascript-samples.md) można `contract` `elements` włączyć, wstawiając między i typem strony. Na przykład `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Część [wersji](page-layout.md) `DataUri` określa pakiet zawartości zawierającej HTML, CSS i JavaScript dla elementów interfejsu użytkownika w zasadach. Jeśli zamierzasz włączyć kod po stronie klienta JavaScript, elementy, na których opierasz javascript, muszą być niezmienne. Jeśli nie są one niezmienne, wszelkie zmiany mogą spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby zapobiec tym problemom, wymuś użycie układu strony i określ wersję układu strony. Dzięki temu wszystkie definicje zawartości, na których opierasz javascript, są niezmienne. Nawet jeśli nie zamierzasz włączać języka JavaScript, nadal musisz określić wersję układu strony dla stron.

W poniższym przykładzie `selfasserted` przedstawiono **DataUri** wersji: `1.2.0`

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrowanie do układu strony

Format wartości musi zawierać słowo `contract`: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Aby określić układ strony w zasadach niestandardowych, które używają starej wartości **DataUri,** użyj poniższej tabeli, aby przeprowadzić migrację do nowego formatu.

| Stara wartość DataUri | Nowa wartość DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metadane

A **Metadata** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0:n | Metadane, które odnoszą się do definicji zawartości. |

**Element elementu** **metadata** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Klucz | Tak | Klucz metadanych.  |

#### <a name="metadata-keys"></a>Klucze metadanych

Definicja zawartości obsługuje następujące elementy metadanych:

| Klucz | Wymagany | Opis |
| --------- | -------- | ----------- |
| DisplayName | Nie | Ciąg zawierający nazwę definicji zawartości. |

### <a name="localizedresourcesreferences"></a>ZlokalizowaneResourcesReferences

Element **LocalizedResourcesReferences** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ZlokalizowaneResourcesReference | 1:n | Lista zlokalizowanych odwołań do zasobów dla definicji zawartości. |

Element **LocalizedResourcesReference** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Język | Tak | Ciąg, który zawiera obsługiwany język dla zasad na RFC 5646 — tagi dla języków identyfikujących. |
| ZlokalizowaneResourcesReferenceId | Tak | Identyfikator **localizedResources** elementu. |

W poniższym przykładzie przedstawiono definicję zawartości rejestracji lub logowania z odwołaniem do lokalizacji w języku angielskim, francuskim i hiszpańskim:

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

Aby dowiedzieć się, jak dodać obsługę lokalizacji do definicji zawartości, zobacz [Lokalizacja](localization.md).

## <a name="content-definition-ids"></a>Identyfikatory definicji zawartości

Atrybut ID elementu **ContentDefinition** określa typ strony, która odnosi się do definicji zawartości. Element definiuje kontekst, który ma być stosowany niestandardowy szablon HTML5/CSS. W poniższej tabeli opisano zestaw identyfikatorów definicji zawartości, który jest rozpoznawany przez platformę środowiska tożsamości i typy stron, które się do nich odnoszą. Można utworzyć własne definicje zawartości z dowolnym identyfikatorem.

| ID | Szablon domyślny | Opis |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu** — wyświetla stronę błędu po napotkaniu wyjątku lub błędu. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości** — wyświetla listę dostawców tożsamości, z których użytkownicy mogą wybierać podczas logowania. Dostępne są zazwyczaj dostawcy tożsamości przedsiębiorstwa, dostawcy tożsamości społecznościowych, tacy jak Facebook i Google+, lub konta lokalne. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości do rejestracji** — wyświetla listę dostawców tożsamości, z których użytkownicy mogą wybierać podczas rejestracji. Dostępne są zazwyczaj dostawcy tożsamości przedsiębiorstwa, dostawcy tożsamości społecznościowych, tacy jak Facebook i Google+, lub konta lokalne. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona Nie pamiętasz hasła** — wyświetla formularz, który użytkownicy muszą wypełnić, aby zainicjować resetowanie hasła. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania do konta lokalnego** — wyświetla formularz do logowania się przy za pomocą konta lokalnego opartego na adresie e-mail lub nazwie użytkownika. Formularz może zawierać pole wprowadzania tekstu i pole wprowadzania hasła. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji konta lokalnego** — wyświetla formularz do skonfigurowania konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak: pole wprowadzania tekstu, pole wprowadzania hasła, przycisk opcji, pola rozwijane z pojedynczym zaznaczeniem i pola wyboru z wieloma zaznaczeniami. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego** — weryfikuje numery telefonów za pomocą tekstu lub głosu podczas rejestracji lub logowania. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji konta społecznościowego** — wyświetla formularz, który użytkownicy muszą wypełnić podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu** — wyświetla formularz, do który użytkownicy mogą uzyskać dostęp do aktualizacji swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona strona rejestracji lub logowania** — obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google+, lub kont lokalnych. |

## <a name="next-steps"></a>Następne kroki

Na przykład dostosowywania interfejsu użytkownika przy użyciu definicji zawartości zobacz:

[Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](custom-policy-ui-customization.md)
