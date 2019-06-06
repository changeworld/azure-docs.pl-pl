---
title: Wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta | Dokumentacja firmy Microsoft
description: Temat pokazująca, jak wykonać wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b50b59b6a1f99787b842923cd6ec77ee6500f0a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509529"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta 
Po włączeniu usługi Azure Active Directory (Azure AD) B2C umożliwia odbiorcy w celu uzyskania aplikacji, zapewniając adresu e-mail i tworzenia konta lokalnego. Usługa Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od użytkowników sprawdzić, w procesie rejestracji. Go zapobiega także złośliwego procesu automatycznego generowania fałszywych kont dla aplikacji.

Niektórzy deweloperzy aplikacji wolą pominięcia weryfikacji wiadomości e-mail w procesie rejestracji i zamiast tego konsumentów weryfikacji adresu e-mail później. Aby to umożliwić, aby wyłączyć weryfikację poczty e-mail można skonfigurować usługi Azure AD B2C. Należy utworzyć gładsze procesu rejestracji i zapewnia elastyczność do odróżnienia odbiorców, którzy mają zweryfikować swój adres e-mail z tych klientów, które nie mają.

Domyślnie przepływy rejestracji użytkownika mają Weryfikacja adresu e-mail włączona. Aby je wyłączyć, wykonaj następujące kroki:

1. Kliknij przycisk **przepływy użytkownika**.
2. Kliknij przycisk przepływu użytkownika (na przykład "B2C_1_SiUp"), aby go otworzyć. 
3. Kliknij przycisk **strony układów**.
4. Kliknij przycisk **strona rejestracji dla kont lokalnych**.
5. Kliknij przycisk **adres E-mail** w **nazwa** kolumnie, **atrybutów użytkownika** sekcji.
6. W obszarze **wymaga weryfikacji**, wybierz opcję **nie**.
7. Kliknij pozycję **Zapisz** w górnej części bloku. Gotowe!

> [!NOTE]
> Wyłączanie weryfikacji e-mail w procesie rejestracji może prowadzić do wysyłania spamu. Jeśli wyłączysz domyślny, zaleca się dodawania systemu weryfikacji.
> 
>
