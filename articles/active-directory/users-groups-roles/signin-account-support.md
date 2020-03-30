---
title: Czy strona logowania usługi Azure AD akceptuje konta Microsoft | Dokumenty firmy Microsoft
description: Jak wiadomości na ekranie odzwierciedlają wyszukiwanie nazwy użytkownika podczas logowania
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024280"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opcje logowania dla kont Microsoft w usłudze Azure Active Directory

Strona logowania usługi Microsoft 365 dla usługi Azure Active Directory (Azure AD) obsługuje konta służbowe lub szkolne i konta Microsoft, ale w zależności od sytuacji użytkownika może to być jedna lub druga lub obie. Na przykład strona logowania usługi Azure AD obsługuje:

* Aplikacje akceptujące logowania z obu typów kont
* Organizacje, które przyjmują gości

## <a name="identification"></a>Identyfikacja
Można sprawdzić, czy strona logowania używana przez organizację obsługuje konta Microsoft, patrząc na tekst podpowiedzi w polu nazwa użytkownika. Jeśli w tekście podpowiedzi jest "Poczta e-mail, telefon lub Skype", strona logowania obsługuje konta Microsoft.

![Różnica między stronami logowania do konta](./media/signin-account-support/ui-prompt.png)

[Dodatkowe opcje logowania działają tylko w przypadku osobistych kont Microsoft,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) ale nie można ich używać do logowania się do zasobów konta służbowego.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie marki logowania](../fundamentals/add-custom-domain.md)