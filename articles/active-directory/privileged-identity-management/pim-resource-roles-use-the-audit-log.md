---
title: Inspekcja role zasobów dla zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak uzyskać wgląd w całą aktywność roli dla danego zasobu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8f328a609d696886ed452589d3cdfb5f45aec62a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621287"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Role zasobów inspekcji dla zasobów platformy Azure przy użyciu Privileged Identity Management 

Inspekcja zasobu zapewnia wgląd w całą aktywność roli dla zasobu. Można filtrować informacje przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowy.
![Informacje o filtrze](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Inspekcja zasobu również zapewnia szybki dostęp do szczegółowych działań użytkownika. W obszarze **typ inspekcji**, wybierz opcję **Aktywuj**. Wybierz **(działanie)** aby zobaczyć akcje użytkownika w ramach zasobów platformy Azure.
![Szczegóły działania](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Więcej szczegółów działania](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Moja inspekcja

Moja inspekcja zapewnia wgląd w działania roli osobistych użytkownika. Można filtrować informacje przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowy.
![Działania roli osobistej](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działanie zasobów platformy Azure

Aby zobaczyć, jakie akcje określonego użytkownika odbyło się w różnych zasobów, możesz przejrzeć działania zasobów platformy Azure, który jest skojarzony z okresem danego aktywacji. Rozpocznij, wybierając użytkownika z **członków** widoku lub z listy elementów członkowskich w określonej roli. Wynik zawiera graficzne akcji przez użytkownika w ramach zasobów platformy Azure według daty. Pokazuje również ostatnie aktywacje ról w tym samym okresie czasu.

![Szczegóły użytkownika](media/azure-pim-resource-rbac/rbac-user-details.png)

Wybieranie aktywacji określoną rolę pokazuje szczegóły uaktywnienia roli i odpowiadającego im działania zasobów platformy Azure, który wystąpił podczas, gdy ten użytkownik był aktywny.

![Wybierz aktywacji roli](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

