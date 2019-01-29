---
title: Zaznacz kontrakt strony w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zaznacz kontrakt strony w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 637cdb338496764e64c18a887673808ef4e8415a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203457"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Zaznacz kontrakt strony w usłudze Azure Active Directory B2C, za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Kontrakt strony można wybrać w usłudze Azure Active Directory (Azure AD) B2C, przez skonfigurowanie go w [zasady niestandardowe](active-directory-b2c-overview-custom.md). Kontrakt strony jest skojarzenie elementów, które oferuje usługa Azure AD B2C i zawartości, przez Ciebie. Jeśli zamierzasz używać [Javascript](javascript-samples.md), należy zdefiniować wersja kontraktu strony dla wszystkich definicji zawartości w zasadach niestandardowych.

## <a name="replace-datauri-values"></a>Zastąp wartości Identyfikator URI

W przypadku zasad niestandardowych, konieczne może być [ContentDefinitions](contentdefinitions.md) definiują szablony HTML używane w podróży użytkownika. **ContentDefinition** zawiera **identyfikator URI** odwołujący się do elementów strony udostępniane przez usługę Azure AD B2C. **Parametr LoadUri** jest ścieżką względną do zawartości HTML i CSS, która należy podać.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Zaznacz kontrakt strony, możesz zmienić **identyfikator URI** wartości w swojej [ContentDefinitions](contentdefinitions.md) w zasadach. Przełączając się ze starego **identyfikator URI** wartości z nowymi wartościami miar, w przypadku zaznaczenia niemodyfikowalny pakiet. Zaletą używania tego pakietu jest wiadomo nie zmienić i spowodować nieoczekiwane zachowanie na stronie. 

Aby skonfigurować stronę umowy, skorzystaj z poniższej tabeli, aby znaleźć **identyfikator URI** wartości. 

| Stara wartość identyfikator URI | Nowa wartość identyfikator URI |
| ----------------- | ----------------- |
| Nazwa urn: com:microsoft:aad:b2c:elements:idpselection:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| Nazwa urn: com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:multifactor:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:multifactor:1.1.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| Nazwa urn: com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:globalexception:1.0.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| Nazwa urn: com:microsoft:aad:b2c:elements:globalexception:1.1.0 | Nazwa urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej informacji na temat w jaki sposób dostosować interfejs użytkownika aplikacji w [Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



