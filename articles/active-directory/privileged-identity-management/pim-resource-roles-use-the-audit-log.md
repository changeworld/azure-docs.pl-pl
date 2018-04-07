---
title: Privileged Identity Management zasobów Azure - inspekcji zasobów | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak wyświetlać wszystkie działania roli dla zasobu.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - role zasobów - inspekcji

Inspekcji zasobów umożliwia wyświetlanie wszystkich działań roli dla zasobu. Można filtrować dane przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowych.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Inspekcji zasobów również zapewnia szybki dostęp, aby wyświetlić szczegóły działania użytkownika. Wybierz pozycję "Uaktywnij" w obszarze "Inspekcji type". Kliknij na "(działanie)", aby wyświetlić czynności wykonywanych przez użytkownika na zasobów Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Moja inspekcja

Moje inspekcji zapewnia widok działania roli osobistych użytkownika. Można filtrować dane przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowych.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działania zasobów Azure

W przypadku, gdy potrzebujesz zobaczyć, jakie akcje trwało określonego użytkownika na różnych zasobów, można przejrzeć działania zasobów Azure skojarzonego z okresem danego aktywacji (w przypadku uprawnionych użytkowników). Rozpocznij od wybór użytkownika z widoku elementów członkowskich lub z listy elementów członkowskich w określonej roli. Wynik zawiera graficzne przedstawienie akcje użytkownika zasobów Azure według daty i ostatnie aktywacji roli w tym samym czasie.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Wybieranie aktywacji konkretnej roli zostaną wyświetlone szczegóły aktywacji dla roli, a odpowiadające mu działanie zasobów Azure, który wystąpił podczas aktywnego użytkownika.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

