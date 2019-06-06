---
title: BuildingBlocks — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element BuildingBlocks zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 67770661f0660b9a84d16bbbc7d86cfcbca2cfdf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511584"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**BuildingBlocks** element zostanie dodany wewnątrz [elementu TrustFrameworkPolicy](trustframeworkpolicy.md) elementu.

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
 </BuildingBlocks>
```

**BuildingBlocks** element zawiera następujące elementy, które muszą być określone w kolejności zdefiniowanej:

- [ClaimsSchema](claimsschema.md) — definiuje typy oświadczeń, które mogą być przywoływane jako część zasad. Schemat oświadczeń jest miejscem, gdzie możesz deklarować typów oświadczeń. Typ oświadczenia jest podobny do zmiennej w wielu językach programistyczny. Typ oświadczenia służy do zbierania danych przez użytkownika aplikacji, odbierania oświadczeń od dostawców tożsamości społecznościowych, wysyłanie i odbieranie danych z niestandardowego interfejsu API REST lub przechowywać wszystkie dane wewnętrzne używane przez zasady niestandardowe. 

- [Predykaty i PredicateValidationsInput](predicates.md) — umożliwia wykonywanie procesu weryfikacji, aby upewnić się, tylko dane poprawnie sformułowany jest zawierana oświadczenia.
 
- [ClaimsTransformations](claimstransformations.md) — zawiera listę przekształcenia oświadczeń, których można użyć w zasadach.  Przekształcanie oświadczeń konwertuje jedno oświadczenie na inny. W przekształcania oświadczeń można takich jak określić metodę przekształcenia: 
    - Zmienianie wielkości liter, oświadczenia ciąg do podanej. Na przykład zmiana ciąg z małe litery na wielkie litery.
    - Porównywanie dwa oświadczenia i zwracanie oświadczenie z true wskazującym, które oświadczenia są zgodne, w przeciwnym razie wartość false.
    - Tworzenie oświadczenia ciągu z podanego parametru w zasadach.
    - Tworzenie losowy ciąg przy użyciu generator liczb losowych.
    - Formatowanie roszczenie zgodnie z podany ciąg formatu. Ta transformacja używa języka C# `String.Format` metody.

- [ContentDefinitions](contentdefinitions.md) — zawiera adresy URL dla szablonów HTML5 do użycia w Twojej podróży użytkownika. W przypadku zasad niestandardowych definicji zawartości definiuje stronę HTML5 identyfikator URI, który jest używany do określonego etapu w podróży użytkownika. Na przykład Resetowanie hasła logowania lub tworzenia konta, lub stron błędów. Możesz zmodyfikować wygląd i działanie przez zastąpienie parametr LoadUri dla pliku HTML5. Lub możesz utworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać odwołanie zlokalizowane zasoby przy użyciu identyfikatora lokalizacji.

- [Lokalizacja](localization.md) — umożliwia to obsługę wielu języków. Obsługi lokalizacji, w ramach zasad pozwala skonfigurować listę obsługiwanych języków w zasadach i wybrać język domyślny. Ciągów specyficznych dla języka i kolekcje są również obsługiwane.


