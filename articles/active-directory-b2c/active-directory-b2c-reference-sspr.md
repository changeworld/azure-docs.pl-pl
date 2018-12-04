---
title: Samoobsługowe resetowanie haseł w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Pokazuje, jak skonfigurować samoobsługowe resetowanie haseł dla klientów w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c9d6a66a9d54c75fbb20775f0ae4de4d22a7d599
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840873"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania haseł dla swoich klientów
Korzystając z funkcji resetowania haseł klientów, którzy utworzyli konto dla kont lokalnych mogą resetować swoje hasła, samodzielnie. Pozwala to znacznie ograniczyć obciążenie z personelem pomocy technicznej, zwłaszcza, jeśli aplikacja ma miliony klientów przy użyciu go w regularnych odstępach czasu. Obecnie korzystanie ze zweryfikowanym adresem e-mail jest metoda tylko obsługiwane odzyskiwania.

> [!NOTE]
> Ten artykuł dotyczy haseł resetowania używane w kontekście V1 **Zaloguj** przepływ użytkownika, który używa **logowanie za pomocą konta lokalnego** jako dostawcy tożsamości. Jeśli potrzebujesz przepływy użytkownika resetowania hasła w pełni dostosowywalnych wywoływane z poziomu aplikacji, zobacz [w tym artykule](active-directory-b2c-reference-policies.md#create-a-password-reset-user-flow).
> 
> 

Domyślnie katalogu nie ma hasła Samoobsługowe resetowanie jest włączona. Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator subskrypcji. Jest to ten sam pracy lub konta służbowego lub tego samego konta Microsoft, który został użyty do utworzenia katalogu.
2. Otwórz **usługi Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
4. Ustaw **samodzielnie resetowania hasła włączone** do **wszystkich**. 
5. Kliknij przycisk **Zapisz** w górnej części strony. Gotowe!

Aby przetestować, funkcja "Uruchom teraz" na dowolnym przepływu logowania użytkownika, który zawiera konta lokalnego jako dostawcy tożsamości. Na logowanie lokalne konto stronie (którym możesz wprowadzić adres e-mail i hasło, lub nazwę użytkownika i hasło), kliknij **nie może uzyskać dostępu do konta?** zweryfikowanie środowiska klienta.

> [!NOTE]
> Strony resetowania haseł, które można dostosować za pomocą [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md).
> 
> 

