---
title: Wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta | Dokumentacja firmy Microsoft
description: Temat pokazująca, jak wykonać wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004602"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Wyłączanie weryfikacji e-mail podczas rejestracji w usłudze Azure Active Directory B2C konsumenta 
Po włączeniu usługi Azure Active Directory (Azure AD) B2C umożliwia odbiorcy w celu uzyskania aplikacji, zapewniając adresu e-mail i tworzenia konta lokalnego. Usługa Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od użytkowników sprawdzić, w procesie rejestracji. Go zapobiega także złośliwego procesu automatycznego generowania fałszywych kont dla aplikacji.

Niektórzy deweloperzy aplikacji wolą pominięcia weryfikacji wiadomości e-mail w procesie rejestracji i zamiast tego konsumentów weryfikacji adresu e-mail później. Aby to umożliwić, aby wyłączyć weryfikację poczty e-mail można skonfigurować usługi Azure AD B2C. Należy utworzyć gładsze procesu rejestracji i zapewnia elastyczność do odróżnienia odbiorców, którzy mają zweryfikować swój adres e-mail z tych klientów, które nie mają.

Domyślnie zasady tworzenia konta mają Weryfikacja adresu e-mail włączona. Aby je wyłączyć, wykonaj następujące kroki:

1. Kliknij przycisk **zasady tworzenia konta** lub **zasady rejestracji lub logowania** zależności od skonfigurowanych dla rejestracji.
2. Kliknij zasady (na przykład "B2C_1_SiUp"), aby go otworzyć. 
3. Kliknij przycisk **Edytuj** w górnej części bloku.
4. Kliknij przycisk **dostosowanie interfejsu użytkownika strony**.
5. Kliknij przycisk **strona rejestracji dla kont lokalnych**.
6. Kliknij przycisk **adres E-mail** w **nazwa** kolumnie, **atrybuty tworzenia konta** sekcji.
7. Przełącz **wymaga weryfikacji** opcję **nie**.
8. Kliknij przycisk **OK** u dołu, aż osiągniesz **Edytuj zasady** bloku.
9. Kliknij pozycję **Zapisz** w górnej części bloku. Gotowe!

> [!NOTE]
> Wyłączanie weryfikacji e-mail w procesie rejestracji może prowadzić do wysyłania spamu. Jeśli wyłączysz domyślny, zaleca się dodawania systemu weryfikacji.
> 
>