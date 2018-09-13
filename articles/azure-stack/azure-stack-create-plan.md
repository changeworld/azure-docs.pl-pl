---
title: Tworzenie planu w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator chmury Utwórz plan, który umożliwi subskrybentom aprowizację maszyn wirtualnych.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0795c3d833133e2881f3c1ba0ae56584a229a31f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721249"
---
# <a name="create-a-plan-in-azure-stack"></a>Tworzenie planu w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

[Plany](azure-stack-key-features.md) są grupami obejmującymi co najmniej jedną usługę. Jako dostawca może utworzyć plany do zaoferowania użytkownikom. Z kolei użytkownicy subskrybują oferty, aby korzystać z planów i usług, które należą do. W tym przykładzie pokazano, jak utworzyć plan, który zawiera magazyn dostawców zasobów obliczeniowych, sieci i. Ten plan zapewnia subskrybentom aprowizację maszyn wirtualnych.

1. Zaloguj się do portalu administratora usługi Azure Stack (https://adminportal.local.azurestack.external).

2. Aby utworzyć plan i ofertę, która może być subskrybowana przez użytkowników, wybierz **+ Utwórz zasób** > **oferty i plany** > **Plan**.
  
   ![Wybierz plan](media/azure-stack-create-plan/select-plan.png)

3. W obszarze **nowy plan**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, który użytkownicy mogą zobaczyć. Tylko administrator może zobaczyć nazwę zasobu, które Administratorzy użyć do pracy z planem jako zasobem usługi Azure Resource Manager.

   ![Określ szczegóły](media/azure-stack-create-plan/plan-name.png)

4. Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę, jako kontener dla planu.

   ![Określ grupę zasobów](media/azure-stack-create-plan/resource-group.png)

5. Wybierz **usług** a następnie zaznacz pole wyboru **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Następnie wybierz pozycję **wybierz** Aby zapisać konfigurację. Pola wyboru są wyświetlane, gdy wskaźnik myszy znajduje się nad każdej opcji.
  
   ![Wybieranie usług](media/azure-stack-create-plan/services.png)

6. Wybierz **przydziały**, **Microsoft.Storage (lokalne)**, a następnie wybierz domyślny limit przydziału lub wybierz **Tworzenie nowego limitu przydziału** można utworzyć przydziału dostosowanego.
  
   ![Przydziały](media/azure-stack-create-plan/quotas.png)

7. Jeśli tworzysz nowy limit przydziału, wprowadź **nazwa** dla limitu przydziału > określ wartości limitu przydziału > Wybierz **OK**. **Utwórz przydział** zamyka okno dialogowe.

   ![Nowy limit przydziału](media/azure-stack-create-plan/new-quota.png)

   Następnie wybierz nowy limit przydziału, który został utworzony. Wybranie przydział przypisuje do niego i zamyka okno dialogowe wyboru.
  
   ![Przypisz limitu przydziału](media/azure-stack-create-plan/assign-quota.png)

8. Powtórz kroki 6 i 7, aby utworzyć i przypisać przydziały **Microsoft.Network (lokalne)** i **Microsoft.Compute (lokalne)**. Gdy wszystkie trzy usługi są przypisane przydziały, będzie wyglądać następnym przykładzie.

   ![Przydziały pełną limitu przydziału](media/azure-stack-create-plan/all-quotas-assigned.png)

9. W obszarze **przydziały**, wybierz **OK**, a następnie w obszarze **nowy plan**, wybierz **Utwórz** Aby utworzyć plan.

    ![Tworzenie planu](media/azure-stack-create-plan/create.png)

10. Aby wyświetlić nowy plan, wybierz **wszystkie zasoby**, a następnie wyszukaj plan i wybierz odpowiednią nazwę. Jeśli lista zasobów jest długa, użyj **wyszukiwania** można zlokalizować planu według nazwy.

   ![Przejrzyj plan](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie oferty](azure-stack-create-offer.md)
