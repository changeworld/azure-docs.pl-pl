---
title: Tworzenie planu w stosie Azure | Dokumentacja firmy Microsoft
description: Jako administrator chmury Utwórz plan, który umożliwia maszynom wirtualnym udostępniania subskrybentów.
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
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Tworzenie planu w usłudze Azure Stack

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

[Plany](azure-stack-key-features.md) są grupami obejmującymi co najmniej jedną usługę. Jako dostawca można utworzyć plany oferować użytkownikom. Z kolei użytkownikom subskrybować oferty korzystać z planów i usług, które obejmują one. Ten przykład przedstawia sposób tworzenia planu, który zawiera compute, sieci i dostawcy zasobów magazynu. Ten plan umożliwia subskrybentów na umieszczanie maszyn wirtualnych.

1. Zaloguj się do portalu administratora platformy Azure stosu (https://adminportal.local.azurestack.external).

2. Aby utworzyć plan i oferty, które użytkownicy mogą subskrybować, wybierz **nowy** > **oferuje + plany** > **planu**.  
   ![Wybierz plan](media/azure-stack-create-plan/select-plan.png)

3. W **nowy plan** okienka, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, dostępna dla użytkowników. Tylko administrator może zobaczyć Nazwa zasobu jest nazwą Administratorzy służy do pracy z planem jako zasób usługi Azure Resource Manager.  
   ![Określ szczegóły](media/azure-stack-create-plan/plan-name.png)

4. Utwórz nową **grupy zasobów**, lub wybierz istniejący, jako kontener dla planu.  
   ![Określ grupę zasobów](media/azure-stack-create-plan/resource-group.png)

5. Wybierz **usług** , a następnie zaznacz pole wyboru **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Następnie wybierz **wybierz** Aby zapisać konfigurację. Pola wyboru są wyświetlane, gdy wskaźnik myszy znajduje się nad każdej opcji.  
   ![Wybierz usługi](media/azure-stack-create-plan/services.png)

6. Wybierz **przydziały**, **Microsoft.Storage (lokalne)**, a następnie wybierz pozycję domyślnego przydziału albo wybierz **Utwórz nowy przydział** dostosować limit przydziału.  
   ![Przydziały](media/azure-stack-create-plan/quotas.png)

7. Jeśli tworzysz nowy przydział, wprowadź **nazwa** dla limitu przydziału > określ wartości przydziału > Wybierz **OK**. **Tworzenie przydziału** okienko zostanie zamknięte.
   ![Nowy przydział](media/azure-stack-create-plan/new-quota.png)

   Następnie wybierz nowy przydział utworzony. Wybór przydział przypisuje go i zamyka okienko wyboru.  
   ![Przypisz limitu przydziału](media/azure-stack-create-plan/assign-quota.png)

8. Powtórz kroki 6 i 7, aby utworzyć i przypisać przydziały na potrzeby **Microsoft.Network (lokalne)** i **Microsoft.Compute (lokalne)**.  Jeśli wszystkie trzy usługi są przypisane przydziały, pojawią się one podobne do następujących obrazów.  
   ![Przydziały pełną przydziału](media/azure-stack-create-plan/all-quotas-assigned.png)

9. W **przydziały** okienku wybierz **OK**, a następnie w **nowy plan** okienku wybierz **Utwórz** Aby utworzyć plan.  
    ![Tworzenie planu](media/azure-stack-create-plan/create.png)
10. Aby wyświetlić nowy plan, wybierz **wszystkie zasoby**, następnie wyszukaj plan i wybierz jego nazwę. Jeśli lista zasobów jest długa, użyj **wyszukiwania** można zlokalizować planu według nazwy.  
   ![Przejrzyj plan](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Kolejne kroki
[Tworzenie oferty](azure-stack-create-offer.md)
