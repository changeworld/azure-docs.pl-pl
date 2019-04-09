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
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288594"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opcje logowania dla konta Microsoft w usłudze Azure Active Directory

Strony logowania usługi Microsoft 365 dla usługi Azure Active Directory (Azure AD) obsługuje jeden lub oba pracy lub kont służbowych i kont Microsoft, w zależności od sytuacji, w celu obsługi:

* Aplikacje, które akceptują logowania z obu typów kont
* Organizacje, które akceptują gości

## <a name="identification"></a>Identyfikacja
Aby sprawdzić, czy strony logowania, której używa Twoja organizacja obsługuje konta Microsoft, analizując tekst wskazówki w polu Nazwa użytkownika. Jeśli tekst wskazówki mówi "Wiadomości E-mail, telefon lub Skype", strona logowania obsługuje konta Microsoft.

![Różnica między strony logowania konta](./media/signin-account-support/ui-prompt.png)

[Dodatkowe opcje logowania działają tylko w przypadku osobistych kont Microsoft](index.yml) , ale nie można użyć do logowania do zasobów konta służbowego.

## <a name="next-steps"></a>Kolejne kroki

[Dostosowywanie logowania znakowanie](../fundamentals/add-custom-domain.md)