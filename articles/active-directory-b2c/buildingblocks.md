---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Określ element BuildingBlocks zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189875"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**BuildingBlocks** Element jest dodawany wewnątrz [TrustFrameworkPolicy](trustframeworkpolicy.md) element.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

**BuildingBlocks** Element zawiera następujące elementy, które muszą być określone w zdefiniowanej kolejności:

- [ClaimsSchema](claimsschema.md) — definiuje typy oświadczeń, do których można się odwoływać jako część zasad. Schemat oświadczeń jest miejscem, w którym deklarujesz typy oświadczeń. Typ oświadczenia jest podobny do zmiennej w wielu językach programowych. Typ oświadczenia służy do zbierania danych od użytkownika aplikacji, otrzymywania oświadczeń od dostawców tożsamości społecznościowych, wysyłania i odbierania danych z niestandardowego interfejsu API REST lub przechowywania dowolnych danych wewnętrznych używanych przez zasady niestandardowe.

- [Predykates i PredicateValidationsInput](predicates.md) — umożliwia przeprowadzenie procesu sprawdzania poprawności, aby upewnić się, że tylko poprawnie utworzone dane są wprowadzane do oświadczenia.

- [ClaimsTransformations](claimstransformations.md) — zawiera listę przekształceń oświadczeń, które mogą być używane w zasadach.  Transformacja oświadczeń konwertuje jedno oświadczenie na inne. W transformacji oświadczeń można określić metodę przekształcania, taką jak:
  - Zmiana przypadku oświadczenia ciągu na określony. Na przykład zmiana ciągu z małych na wielkie litery.
  - Porównanie dwóch roszczeń i zwrócenie roszczenia z prawdą wskazującą, że oświadczenia są zgodne, w przeciwnym razie fałszywe.
  - Tworzenie oświadczenia ciągu z podanego parametru w zasadach.
  - Tworzenie losowego ciągu przy użyciu generatora liczb losowych.
  - Formatowanie oświadczenia zgodnie z podanym ciągiem formatu. Ta transformacja używa `String.Format` metody C#.

- InputValidation — ten element umożliwia wykonywanie agregacji logicznych, które są podobne do *i* *lub*.

- [ContentDefinitions](contentdefinitions.md) — zawiera adresy URL dla szablonów HTML5 do użycia w podróży użytkownika. W zasadach niestandardowych definicja zawartości definiuje identyfikator URI strony HTML5, który jest używany dla określonego kroku w podróży użytkownika. Na przykład strony logowania lub rejestracji, resetowania hasła lub strony błędów. Wygląd i działanie można zmodyfikować, zastępując loaduri dla pliku HTML5. Możesz też tworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać zlokalizowane odwołanie zasobów przy użyciu identyfikatora lokalizacji.

- [Lokalizacja](localization.md) — umożliwia obsługę wielu języków. Obsługa lokalizacji w zasadach umożliwia skonfigurowanie listy obsługiwanych języków w zasadach i wybranie języka domyślnego. Obsługiwane są również ciągi i kolekcje specyficzne dla języka.

- [DisplayControls](display-controls.md) — definiuje formanty, które mają być wyświetlane na stronie. Formanty wyświetlania mają specjalną funkcjonalność i współdziałają z profilami technicznymi sprawdzania poprawności zaplecza. Kontrolki wyświetlania są obecnie w **wersji zapoznawczej**.
