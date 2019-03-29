---
title: Wymagania dotyczące korzystania z usługi Azure Active Directory PIM - licencji | Dokumentacja firmy Microsoft
description: W tym artykule opisano wymagania licencyjne, aby używać usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
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
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e16209a185623b6e15650f70141edd6394e337
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576160"
---
# <a name="license-requirements-to-use-pim"></a>Wymagania licencyjne, aby używać aplikacji PIM

Aby korzystać z usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), katalog musi mieć ważną licencję. Ponadto należy przypisać licencji dla administratorów i użytkowników, których. W tym artykule opisano wymagania licencyjne, aby używać aplikacji PIM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z usługi PIM, katalogiem musi mieć jedną z następujących płatnych lub licencje próbne:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Które użytkownicy muszą mieć licencje?

Każdy administrator lub użytkownik, który wchodzi w interakcję z lub otrzymuje jedną z korzyści z usługi PIM musi mieć licencję. Przykłady:

- Administratorzy z ról usługi Azure AD zarządzane za pomocą usługi PIM
- Administratorzy z role zasobów platformy Azure zarządzane przy użyciu usługi PIM
- Administratorzy przypisani do roli Administrator ról uprzywilejowanych
- Użytkownicy przypisani jako kwalifikujące się role usługi Azure AD zarządzane za pomocą usługi PIM
- Użytkownicy mogli zatwierdzanie/odrzucanie żądań w usłudze PIM
- Użytkownicy przypisani do roli zasobów platformy Azure przy użyciu just in time lub bezpośrednich przypisań (w oparciu o czas)  
- Użytkownicy przypisani do przeglądu dostępu
- Użytkowników, którzy wykonują przeglądów dostępu

Aby uzyskać informacje dotyczące sposobu przypisywania licencji do używany przez, zobacz [Przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Co się stanie po wygaśnięciu licencji?

Jeśli program Azure AD Premium P2, EMS E5 lub licencja próbna wygaśnie, funkcje usługi PIM nie będzie już dostępna w katalogu:

- Będzie to miało wpływu trwałego roli przypisania do ról usługi Azure AD.
- Usługi PIM w witrynie Azure portal oraz poleceń cmdlet interfejsu API programu Graph i interfejsy programu PowerShell usługi PIM, nie będą dostępne użytkownikom aktywację ról uprzywilejowanych, zarządzanie dostępem uprzywilejowanym lub wykonać przeglądów ról uprzywilejowanych.
- Kwalifikujące się przypisania ról ról usługi Azure AD zostanie usunięty, ponieważ użytkownicy nie będą mogły uaktywniać swoje role uprzywilejowane.
- Zakończy się dowolnym bieżących przeglądów ról usługi Azure AD i ustawień konfiguracji usługi PIM zostaną usunięte.
- Usługi PIM nie wyśle wiadomości e-mail na zmianach przypisania roli.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie usługi PIM](pim-deployment-plan.md)
- [Rozpoczynanie korzystania z usługi PIM](pim-getting-started.md)
- [Role nie można zarządzać w usłudze PIM](pim-roles.md)
