---
title: Samoobsługowe resetowanie haseł w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Pokazuje, jak skonfigurować samoobsługowe resetowanie haseł dla klientów w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3612e10df12e2b18f32caae55bdd83b12a4e24a6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450074"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania haseł dla swoich klientów
Korzystając z funkcji resetowania haseł klientów, którzy utworzyli konto dla kont lokalnych mogą resetować swoje hasła, samodzielnie. Pozwala to znacznie ograniczyć obciążenie z personelem pomocy technicznej, zwłaszcza, jeśli aplikacja ma miliony klientów przy użyciu go w regularnych odstępach czasu. Obecnie korzystanie ze zweryfikowanym adresem e-mail jest metoda tylko obsługiwane odzyskiwania.

> [!NOTE]
> Ten artykuł dotyczy haseł resetowania używane w kontekście zasad logowania. Jeśli potrzebne są zasady resetowania hasła w pełni dostosowywalnych wywoływane z poziomu aplikacji, zobacz [w tym artykule](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Domyślnie katalogu nie ma hasła Samoobsługowe resetowanie jest włączona. Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator subskrypcji. Jest to ten sam pracy lub konta służbowego lub tego samego konta Microsoft, który został użyty do utworzenia katalogu.
2. Otwórz **usługi Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
4. Ustaw **samodzielnie resetowania hasła włączone** do **wszystkich**. 
5. Kliknij przycisk **Zapisz** w górnej części strony. Gotowe!

Aby przetestować, funkcja "Uruchom teraz" na dowolnym zasad logowania z konta lokalnego jako dostawcę tożsamości. Na logowanie lokalne konto stronie (którym możesz wprowadzić adres e-mail i hasło, lub nazwę użytkownika i hasło), kliknij **nie może uzyskać dostępu do konta?** zweryfikowanie środowiska klienta.

> [!NOTE]
> Strony resetowania haseł, które można dostosować za pomocą [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md).
> 
> 

