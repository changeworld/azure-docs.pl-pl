---
title: Własnym resetowania haseł w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Pokazuje, jak skonfigurować samoobsługowego resetowania hasła dla klientów w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ea8b23618b382f557340643afd62e56932bbfb2d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712100"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania hasła dla klientów
Dzięki funkcji resetowania hasła samoobsługi klientów, którzy utworzyli konto dla kont lokalnych można zresetować hasła na ich własnych. To znacznie ograniczyć obciążenie działu pomocy technicznej, zwłaszcza, jeśli aplikacja ma miliony klientów przy użyciu go na bieżąco. Obecnie korzystanie ze zweryfikowanym adresem e-mail jest metoda tylko obsługiwane odzyskiwania.

> [!NOTE]
> Ten artykuł dotyczy hasła Samoobsługowe Resetowanie używane w kontekście zasad logowania. Jeśli potrzebujesz zasady resetowania hasła można swobodnie dostosowywać wywoływane z aplikacji, zobacz [w tym artykule](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Domyślnie katalogu nie ma hasła Samoobsługowe resetowanie jest włączona. Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) jako Administrator subskrypcji. To jest tej samej pracy lub konta służbowego lub tego samego konta Microsoft, który został użyty do utworzenia katalogu.
2. Otwórz **usługi Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
4. Ustaw **samodzielnego resetowania hasła usługi włączone** do **wszystkich**. 
5. Kliknij przycisk **zapisać** w górnej części strony. Gotowe!

Aby przetestować, funkcja "Uruchom teraz" na żadnych zasad logowania z kontami lokalnymi funkcję dostawcy tożsamości. Na logowanie lokalne konto strony (gdzie należy wprowadzić adres e-mail i hasło lub nazwę użytkownika i hasło), kliknij przycisk **nie może uzyskać dostępu do konta?** można zweryfikować obsługi klienta.

> [!NOTE]
> Strony resetowania hasła samoobsługi można dostosować za pomocą [firmowe funkcji](../active-directory/customize-branding.md).
> 
> 

