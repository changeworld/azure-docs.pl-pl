---
title: Wymagania licencyjne dotyczące korzystania z programu PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 01/16/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c31c2731a8e872dfd2750fced8b91d283d0892
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804060"
---
# <a name="license-requirements-to-use-pim"></a>Wymagania licencyjne dotyczące korzystania z programu PIM

Aby można było użyć usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), katalog musi mieć ważną licencję. Ponadto licencje muszą być przypisane do administratorów i odpowiednich użytkowników. W tym artykule opisano wymagania licencyjne dotyczące korzystania z programu PIM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z usługi PIM, katalog musi mieć jedną z następujących licencji płatnych lub próbnych:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Którzy użytkownicy muszą mieć licencje?

Każdy administrator lub użytkownik, który współdziała z usługą lub otrzymuje korzyść z usługi PIM, musi mieć licencję. Przykłady:

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

Jeśli licencja na Azure AD — wersja Premium P2, EMS E5 lub wersja próbna wygaśnie, funkcje PIM nie będą już dostępne w Twoim katalogu:

- Nie wpłynie to na trwałe przypisania roli do ról usługi Azure AD.
- Usługa PIM w Azure Portal, a także polecenia cmdlet interfejs API programu Graph i interfejsy programu PowerShell w usłudze PIM nie będą już dostępne, aby użytkownicy mogli aktywować role uprzywilejowane, zarządzać dostępem uprzywilejowanym lub wykonywać przeglądy dostępu ról uprzywilejowanych.
- Uprawnione przypisania ról usługi Azure AD zostaną usunięte, ponieważ użytkownicy nie będą już mogli aktywować uprzywilejowanych ról.
- Wszystkie bieżące przeglądy dostępu ról usługi Azure AD zostaną zakończone, a ustawienia konfiguracji PIM zostaną usunięte.
- Program PIM nie będzie już wysyłać wiadomości e-mail w ramach zmian przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi PIM](pim-deployment-plan.md)
- [Rozpoczynanie korzystania z usługi PIM](pim-getting-started.md)
- [Role, którymi nie można zarządzać w usłudze PIM](pim-roles.md)
