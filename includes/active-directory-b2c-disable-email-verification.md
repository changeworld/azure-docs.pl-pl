---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126735"
---
## <a name="disable-email-verification"></a>Wyłączanie weryfikacji e-mail

Domyślnie usługa Azure Active Directory B2C (Azure AD B2C) weryfikuje adres e-mail klienta pod kątem kont lokalnych (kont dla użytkowników, którzy zarejestrują się przy użyciu adresu e-mail lub nazwy użytkownika). Usługa Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od klientów weryfikacji ich podczas procesu rejestracji. Zapobiega to również złośliwym podmiotom w generowaniu fałszywych kont w aplikacjach.

Niektórzy deweloperzy aplikacji wolą pominąć weryfikację poczty e-mail podczas procesu rejestracji, a zamiast tego klienci weryfikują swój adres e-mail później. Aby to poprzeć, usługę Azure AD B2C można skonfigurować w taki sposób, aby wyłączyć weryfikację poczty e-mail. W ten sposób tworzy płynniejszy proces rejestracji i daje deweloperom elastyczność, aby odróżnić klientów, którzy zweryfikowali swój adres e-mail od klientów, którzy nie.

> [!WARNING]
> Wyłączenie weryfikacji wiadomości e-mail w procesie rejestracji może prowadzić do spamu. Jeśli wyłączysz domyślną weryfikację poczty e-mail dostarczoną przez usługę Azure AD B2C, zalecamy wdrożenie zastępczego systemu weryfikacji.
