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
ms.openlocfilehash: e008fb87b57b92f8f7e914e6b4344b52d42f9ef8
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263933"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Usługi Azure Active Directory B2C: Weryfikacja adresu e-mail Wyłącz podczas rejestracji klienta
Po włączeniu usługi Azure Active Directory (Azure AD) B2C umożliwia odbiorcy w celu uzyskania aplikacji, zapewniając adresu e-mail i tworzenia konta lokalnego. Usługa Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od użytkowników sprawdzić, w procesie rejestracji. Go zapobiega także złośliwego procesu automatycznego generowania fałszywych kont dla aplikacji.

Niektórzy deweloperzy aplikacji wolą pominięcia weryfikacji wiadomości e-mail w procesie rejestracji i zamiast tego konsumentów weryfikacji adresu e-mail później. Aby to umożliwić, aby wyłączyć weryfikację poczty e-mail można skonfigurować usługi Azure AD B2C. Należy utworzyć gładsze procesu rejestracji i zapewnia elastyczność do odróżnienia odbiorców, którzy mają zweryfikować swój adres e-mail z tych klientów, które nie mają.

Domyślnie zasady tworzenia konta mają Weryfikacja adresu e-mail włączona. Aby je wyłączyć, wykonaj następujące kroki:

1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w witrynie Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **zasady tworzenia konta** lub **zasady rejestracji lub logowania** zależności od skonfigurowanych dla rejestracji.
3. Kliknij zasady (na przykład "B2C_1_SiUp"), aby go otworzyć. 
4. Kliknij przycisk **Edytuj** w górnej części bloku.
5. Kliknij przycisk **dostosowanie interfejsu użytkownika strony**.
6. Kliknij przycisk **strona rejestracji dla kont lokalnych**.
7. Kliknij przycisk **adres E-mail** w **nazwa** kolumnie, **atrybuty tworzenia konta** sekcji.
8. Przełącz **wymaga weryfikacji** opcję **nie**.
9. Kliknij przycisk **OK** u dołu, aż osiągniesz **Edytuj zasady** bloku.
10. Kliknij przycisk **Zapisz** w górnej części bloku. Gotowe!

> [!NOTE]
> Wyłączanie weryfikacji e-mail w procesie rejestracji może prowadzić do wysyłania spamu. Jeśli wyłączysz domyślny, zaleca się dodawania systemu weryfikacji.
> 
> 

Firma Microsoft zawsze są otwarte opinie i sugestie! Jeśli masz trudności z tego tematu lub poprawić tę zawartość, prosimy o wyrażenie opinii na dole strony. Żądania funkcji, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
