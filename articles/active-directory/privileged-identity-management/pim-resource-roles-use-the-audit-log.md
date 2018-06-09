---
title: Inspekcji role zasobów dla zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak wyświetlać wszystkie działania roli dla zasobu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 211b8c69a1462f7efdcb4002269d96d1d5cf2ae6
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233788"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Inspekcji role zasobów dla zasobów platformy Azure przy użyciu Privileged Identity Management 

Inspekcji zasobów umożliwia wyświetlanie wszystkich działań roli dla zasobu. Można filtrować dane przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowych.
![Informacje o filtrze](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Inspekcji zasobów również zapewnia szybki dostęp do szczegółów działania użytkownika. W obszarze **inspekcji typu**, wybierz pozycję **Aktywuj**. Wybierz **(działanie)** do działania tego użytkownika w zasobów platformy Azure.
![Szczegóły działania](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Więcej szczegółów działania](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Moja inspekcja

Moje inspekcji zapewnia widok działania roli osobistych użytkownika. Można filtrować dane przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowych.
![Działania roli osobiste](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działania zasobów Azure

Aby sprawdzić, jakie akcje miało określonego użytkownika w różnych zasobów, można przejrzeć działania zasobów platformy Azure skojarzoną z okresu danego aktywacji. Uruchom po wybraniu użytkownika z **członków** widoku lub na liście elementów członkowskich w określonej roli. Wynik zawiera graficzne przedstawienie działań użytkownika w zasobów systemu Azure według daty. Przedstawiono również ostatnie aktywacji roli, w tym samym okresie czasu.

![Szczegóły użytkownika](media/azure-pim-resource-rbac/rbac-user-details.png)

Wybieranie aktywacji konkretnej roli przedstawiono szczegóły aktywacji roli i odpowiadające mu działanie zasobów platformy Azure, który wystąpił podczas aktywnego użytkownika.

![Wybierz rolę aktywacji](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

