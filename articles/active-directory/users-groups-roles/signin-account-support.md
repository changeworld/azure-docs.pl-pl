---
title: Jak sprawdzić, jeśli na stronie logowania usługi Azure AD akceptuje kont Microsoft | Dokumentacja firmy Microsoft
description: Jak na ekranie komunikatów odzwierciedla wyszukiwanie nazwy użytkownika podczas logowania
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468221"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opcje logowania dla konta Microsoft w usłudze Azure Active Directory

Strony logowania usługi Microsoft 365 dla usługi Azure Active Directory (Azure AD) obsługuje konta służbowe ani służbowych i kont Microsoft, ale w zależności od ewentualne problemy użytkownika, może to być jednej lub drugiej lub oba. Na przykład strony logowania usługi Azure AD obsługuje:

* Aplikacje, które akceptują logowania z obu typów kont
* Organizacje, które akceptują gości

## <a name="identification"></a>Identyfikacja
Aby sprawdzić, czy strony logowania, której używa Twoja organizacja obsługuje konta Microsoft, analizując tekst wskazówki w polu Nazwa użytkownika. Jeśli tekst wskazówki mówi "Wiadomości E-mail, telefon lub Skype", strona logowania obsługuje konta Microsoft.

![Różnica między strony logowania konta](./media/signin-account-support/ui-prompt.png)

[Dodatkowe opcje logowania działają tylko w przypadku osobistych kont Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ale nie można użyć do logowania do zasobów konta służbowego.

## <a name="next-steps"></a>Kolejne kroki

[Dostosowywanie logowania znakowanie](../fundamentals/add-custom-domain.md)