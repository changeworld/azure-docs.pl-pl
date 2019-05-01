---
title: Zaznacz kontrakt strony — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zaznacz kontrakt strony w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4cd29df19179f07fd9b61a2f484b1d49cc05c4cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570580"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Zaznacz kontrakt strony w usłudze Azure Active Directory B2C, za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

W zasadach usługi Azure Active Directory (Azure AD) B2C, można włączyć kodu po stronie klienta JavaScript, czy używasz przepływy użytkownika lub niestandardowych zasad. Aby włączyć język JavaScript dla aplikacji, należy dodać element do Twojej [zasad niestandardowych](active-directory-b2c-overview-custom.md), zaznacz kontrakt strony i użyj [z usługi b2clogin.com](b2clogin.md) w żądaniach. Umowa strony jest skojarzenie elementów, które oferuje usługa Azure AD B2C i zawartości, przez Ciebie. W tym artykule omówiono sposób zaznacz kontrakt strony w usłudze Azure AD B2C, konfigurując je w zasadach niestandardowych.

> [!NOTE]
> Jeśli chcesz włączyć język JavaScript, w przypadku przepływów użytkownika, zobacz [języków JavaScript i strony umowy wersji w usłudze Azure Active Directory B2C](user-flow-javascript-overview.md).

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



