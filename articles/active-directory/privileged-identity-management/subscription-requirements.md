---
title: Wymagania licencyjne dotyczące używania Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Opisuje wymagania licencyjne dotyczące korzystania z Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895114"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Wymagania licencyjne dotyczące używania Privileged Identity Management

Aby można było użyć usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), katalog musi mieć ważną licencję. Ponadto licencje muszą być przypisane do administratorów i odpowiednich użytkowników. W tym artykule opisano wymagania licencyjne dotyczące korzystania z Privileged Identity Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z Privileged Identity Management, katalog musi mieć jedną z następujących licencji płatnych lub wersji próbnej:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Którzy użytkownicy muszą mieć licencje?

Każdy administrator lub użytkownik, który współdziała z programem lub otrzymuje korzyść z Privileged Identity Management musi mieć licencję. Przykłady obejmują:

- Administratorzy z rolami usługi Azure AD zarządzanymi przy użyciu programu PIM
- Administratorzy z rolami zasobów platformy Azure zarządzanymi przy użyciu usług PIM
- Administratorzy przypisani do roli administrator ról uprzywilejowanych
- Użytkownicy przypisani jako uprawnieni do ról usługi Azure AD zarządzanych przy użyciu programu PIM
- Użytkownicy z możliwością zatwierdzania/odrzucania żądań w usłudze PIM
- Użytkownicy przypisani do roli zasobów platformy Azure za pomocą przypisań just-in-Time lub Direct (opartych na czasie)  
- Użytkownicy przypisani do przeglądu dostępu
- Użytkownicy, którzy wykonują przeglądy dostępu

Aby uzyskać informacje na temat sposobu przypisywania licencji do użycia, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Co się stanie po wygaśnięciu licencji?

Jeśli licencja na Azure AD — wersja Premium P2, EMS E5 lub wersja próbna wygaśnie, funkcje Privileged Identity Management nie będą już dostępne w Twoim katalogu:

- Nie wpłynie to na trwałe przypisania roli do ról usługi Azure AD.
- Usługa Privileged Identity Management w Azure Portal, jak również polecenia cmdlet interfejs API programu Graph i interfejsy programu PowerShell dla programu Privileged Identity Management, nie będą już dostępne dla użytkowników w celu aktywowania uprzywilejowanych ról, zarządzania dostępem uprzywilejowanym lub wykonywania przeglądy dostępu ról uprzywilejowanych.
- Uprawnione przypisania ról usługi Azure AD zostaną usunięte, ponieważ użytkownicy nie będą już mogli aktywować uprzywilejowanych ról.
- Wszystkie bieżące przeglądy dostępu ról usługi Azure AD zostaną zakończone, a ustawienia konfiguracji Privileged Identity Management zostaną usunięte.
- Privileged Identity Management nie będą już wysyłać wiadomości e-mail w ramach zmian przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Wdróż Privileged Identity Management](pim-deployment-plan.md)
- [Zacznij korzystać z Privileged Identity Management](pim-getting-started.md)
- [Role, którymi nie można zarządzać w Privileged Identity Management](pim-roles.md)
