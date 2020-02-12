---
title: ContentDefinitions — Azure Active Directory B2C | Microsoft Docs
description: Określ element ContentDefinitions zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a940c7676a59ed85d5cf16c76f72a12ce0026d5
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136268"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Możesz dostosować wygląd i działanie dowolnego [profilu technicznego](self-asserted-technical-profile.md), który został potwierdzony. Azure Active Directory B2C (Azure AD B2C) uruchamia kod w przeglądarce klienta i korzysta z nowoczesnego podejścia zwanego współużytkowaniem zasobów między źródłami (CORS).

Aby dostosować interfejs użytkownika, należy określić adres URL w elemencie **ContentDefinition** z dostosowaną zawartością HTML. W profilu technicznym z własnym potwierdzeń lub **OrchestrationStep**należy wskazać ten identyfikator definicji zawartości. Definicja zawartości może zawierać element **LocalizedResourcesReferences** , który określa listę zlokalizowanych zasobów do załadowania. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę użytkownikowi.

Element **ContentDefinitions** zawiera adresy URL do szablonów HTML5, które mogą być używane w podróży użytkownika. Identyfikator URI strony HTML5 jest używany przez określony krok interfejsu użytkownika. Na przykład strony logowania lub rejestracji, resetowania haseł lub błędów. Możesz zmodyfikować wygląd i działanie, zastępując LoadUri pliku HTML5. Można tworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać odwołanie do zlokalizowanych zasobów w identyfikatorze lokalizacji określonym w elemencie [lokalizacji](localization.md) .

Poniższy przykład pokazuje identyfikator definicji zawartości i definicję zlokalizowanych zasobów:

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

Metadane **LocalAccountSignUpWithLogonEmail** z własnym profilem technicznym zawiera identyfikator definicji zawartości **ContentDefinitionReferenceId** ustawiony na `api.localaccountsignup`

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

Element **ContentDefinition** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator definicji zawartości. Wartość jest określona w sekcji **identyfikatorów definicji zawartości** w dalszej części tej strony. |

Element **ContentDefinition** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Ciąg, który zawiera adres URL strony HTML5 dla definicji zawartości. |
| RecoveryUri | 0:1 | Ciąg, który zawiera adres URL strony HTML służącej do wyświetlania błędu związanego z definicją zawartości. |
| DataUri | 1:1 | Ciąg zawierający względny adres URL pliku HTML, który udostępnia środowisko użytkownika do wywołania dla kroku. |
| Metadane | 1:1 | Kolekcja par klucz/wartość, które zawierają metadane wykorzystane w definicji zawartości. |
| LocalizedResourcesReferences | 0:1 | Kolekcja zlokalizowanych zasobów. Użyj tego elementu, aby dostosować lokalizację interfejsu użytkownika i atrybutu oświadczeń. |

### <a name="datauri"></a>DataUri

Element **DataUri** jest używany do określania identyfikatora strony. Azure AD B2C używa identyfikatora strony do ładowania i inicjowania elementów interfejsu użytkownika oraz języka JavaScript po stronie klienta. Format wartości jest `urn:com:microsoft:aad:b2c:elements:page-name:version`.  W poniższej tabeli wymieniono wartości i opisy identyfikatorów stron, których można użyć.

| Wartość |   Opis |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Wyświetla stronę błędu w przypadku napotkania wyjątku lub błędu. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Wyświetla listę dostawców tożsamości, spośród których użytkownicy mogą wybierać podczas logowania. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Wyświetla formularz służący do logowania się przy użyciu konta lokalnego, które jest oparte na adresie e-mail lub nazwie użytkownika. Ta wartość udostępnia również funkcję "Przechowuj mnie" i "nie pamiętasz hasła?". link. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Wyświetla formularz służący do logowania się przy użyciu konta lokalnego, które jest oparte na adresie e-mail lub nazwie użytkownika. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Weryfikuje numery telefonów przy użyciu tekstu lub głosu podczas rejestracji lub logowania. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Wyświetla formularz, który umożliwia użytkownikom tworzenie lub aktualizowanie profilu. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Element **LocalizedResourcesReferences** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Lista zlokalizowanych odwołań do zasobów dla definicji zawartości. |

Element **LocalizedResourcesReferences** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Język | Yes | Ciąg, który zawiera obsługiwany język dla zasad zgodnie ze specyfikacją RFC 5646-Tagi dla identyfikacji języków. |
| LocalizedResourcesReferenceId | Yes | Identyfikator elementu **LocalizedResources** . |

W poniższym przykładzie przedstawiono definicję rejestracji lub zawartości logowania:

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

W poniższym przykładzie przedstawiono definicję rejestracji lub zawartości logowania z odwołaniem do lokalizacji w języku angielskim, francuskim i hiszpańskim:

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

Atrybut ID elementu **ContentDefinition** określa typ strony, która odnosi się do definicji zawartości. Element definiuje kontekst, który ma zostać zastosowany do niestandardowego szablonu HTML5/CSS. W poniższej tabeli opisano zbiór identyfikatorów definicji zawartości rozpoznawanych przez platformę obsługi tożsamości oraz typy stron, które odnoszą się do nich. Możesz tworzyć własne definicje zawartości przy użyciu dowolnego identyfikatora.

| ID | Szablon domyślny | Opis |
| -- | ---------------- | ----------- |
| **Interfejs API. błąd** | [Exception. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu** — wyświetla stronę błędu w przypadku napotkania wyjątku lub błędu. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości** — wyświetla listę dostawców tożsamości, spośród których użytkownicy mogą wybierać podczas logowania. Te opcje są zwykle dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi. |
| **API. idpselections. signup** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości dla tworzenia konta** — wyświetla listę dostawców tożsamości, z których użytkownicy mogą wybierać podczas rejestracji. Te opcje są zwykle dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona zapomnianego hasła** — wyświetla formularz, który użytkownicy muszą ukończyć, aby zainicjować Resetowanie hasła. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania do konta lokalnego** — wyświetla formularz służący do logowania się przy użyciu konta lokalnego, na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać pole wprowadzania tekstu i pole wprowadzania hasła. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona tworzenia nowego konta lokalnego** — wyświetla formularz służący do tworzenia konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki danych wejściowych, na przykład: pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z wieloma zaznaczeniami. |
| **API. PhoneFactor** | [wieloskładnikowe — 1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego** — weryfikuje numery telefonów przy użyciu tekstu lub głosu podczas rejestracji lub logowania. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona tworzenia konta społecznościowego** — wyświetla formularz, który użytkownicy muszą ukończyć podczas rejestrowania się przy użyciu istniejącego konta u dostawcy tożsamości społecznościowej. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| **API. selfasserted. profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu** — wyświetla formularz, do którego użytkownicy mogą uzyskać dostęp w celu zaktualizowania swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| **API. signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Zunifikowana Rejestracja lub strona logowania** — obsługuje proces tworzenia konta i logowania użytkownika. Użytkownicy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google + lub konta lokalnego. |

