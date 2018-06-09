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
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 1fa01d23108ce92fbd7c854442c0474b19395d25
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248707"
---
# <a name="create-a-plan-in-azure-stack"></a>Tworzenie planu w usłudze Azure Stack

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

[Plany](azure-stack-key-features.md) są grupami obejmującymi co najmniej jedną usługę. Jako dostawca można utworzyć plany oferować użytkownikom. Z kolei użytkownikom subskrybować oferty korzystać z planów i usług, które obejmują one. Ten przykład przedstawia sposób tworzenia planu, który zawiera compute, sieci i dostawcy zasobów magazynu. Ten plan umożliwia subskrybentów na umieszczanie maszyn wirtualnych.

1. Zaloguj się do portalu administratora platformy Azure stosu (https://adminportal.local.azurestack.external).

2. Aby utworzyć plan i oferty, które użytkownicy mogą subskrybować, wybierz **nowy** > **oferuje + plany** > **planu**.
  
   ![Wybierz plan](media/azure-stack-create-plan/select-plan.png)

3. W obszarze **nowy plan**, wprowadź **Nazwa wyświetlana** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, który użytkownicy mogą zobaczyć. Tylko administrator może zobaczyć Nazwa zasobu, które Administratorzy służy do pracy z planem jako zasób usługi Azure Resource Manager.

   ![Określ szczegóły](media/azure-stack-create-plan/plan-name.png)

4. Utwórz nową **grupy zasobów**, lub wybierz istniejący, jako kontener dla planu.

   ![Określ grupę zasobów](media/azure-stack-create-plan/resource-group.png)

5. Wybierz **usług** , a następnie zaznacz pole wyboru **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Następnie wybierz **wybierz** Aby zapisać konfigurację. Pola wyboru są wyświetlane, gdy wskaźnik myszy znajduje się nad każdej opcji.
  
   ![Wybieranie usług](media/azure-stack-create-plan/services.png)

6. Wybierz **przydziały**, **Microsoft.Storage (lokalne)**, a następnie wybierz pozycję domyślnego przydziału albo wybierz **Utwórz nowy przydział** do utworzenia dostosowanego przydziału.
  
   ![Przydziały](media/azure-stack-create-plan/quotas.png)

7. Jeśli tworzysz nowy przydział, wprowadź **nazwa** dla limitu przydziału > określ wartości przydziału > Wybierz **OK**. **Tworzenie przydziału** zamknięciu okna.

   ![Nowy limit przydziału](media/azure-stack-create-plan/new-quota.png)

   Następnie wybierz nowy przydział utworzony. Wybranie przydział przypisuje go i zamknięcie okna dialogowego wyboru.
  
   ![Przypisz limitu przydziału](media/azure-stack-create-plan/assign-quota.png)

8. Powtórz kroki 6 i 7, aby utworzyć i przypisać przydziały na potrzeby **Microsoft.Network (lokalne)** i **Microsoft.Compute (lokalne)**. Gdy wszystkie trzy usługi są przypisane przydziały, będzie wyglądać następnym przykładzie.

   ![Przydziały pełną przydziału](media/azure-stack-create-plan/all-quotas-assigned.png)

9. W obszarze **przydziały**, wybierz **OK**, a następnie w obszarze **nowy plan**, wybierz **Utwórz** Aby utworzyć plan.

    ![Tworzenie planu](media/azure-stack-create-plan/create.png)

10. Aby wyświetlić nowy plan, wybierz **wszystkie zasoby**, następnie wyszukaj plan i wybierz jego nazwę. Jeśli lista zasobów jest długa, użyj **wyszukiwania** można zlokalizować planu według nazwy.

   ![Przejrzyj plan](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie oferty](azure-stack-create-offer.md)
