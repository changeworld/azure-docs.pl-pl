---
title: Tworzenie planu w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator chmury Utwórz plan, który umożliwi subskrybentom aprowizację maszyn wirtualnych.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759473"
---
# <a name="create-a-plan-in-azure-stack"></a>Tworzenie planu w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

[Plany](azure-stack-key-features.md) są grupami jednej lub kilku usług i ich przydziałów. Jako dostawca może utworzyć plany do zaoferowania użytkownikom. Z kolei użytkownicy subskrybują oferty, aby korzystać z planów, usług i przydziałów, które należą do. W tym przykładzie pokazano, jak utworzyć plan, który zawiera magazyn dostawców zasobów obliczeniowych, sieci i. Ten plan zapewnia subskrybentom aprowizację maszyn wirtualnych.

## <a name="create-a-plan-1902-and-later"></a>Utwórz plan (1902 lub nowszy)

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Aby utworzyć plan i ofertę, która może być subskrybowana przez użytkowników, wybierz **+ Utwórz zasób**, następnie **oferty i plany**, następnie **Plan**.
  
   ![Wybierz plan](media/azure-stack-create-plan/select-plan.png)

3. Interfejs użytkownika z kartami pojawia się, która pozwala określić nazwę planu, Dodaj usługi i definiują przydziałów dla wszystkich wybranych usług. Co najważniejsze możesz przejrzeć szczegóły oferty, której tworzysz, przed podjęciem decyzji o jej utworzenia.

   W obszarze **podstawy** karcie **nowy plan** oknie wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazną nazwą planu, wyświetlanego dla operatorów. Należy pamiętać, że w portalu administratora szczegóły planu są widoczne tylko dla operatorów.

   ![Określ szczegóły](media/azure-stack-create-plan/plan-name.png)

4. Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę, jako kontener dla planu.

   ![Określ grupę zasobów](media/azure-stack-create-plan/resource-group.png)

5. Wybierz **usług** kartę, a następnie zaznacz pole wyboru **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage** .
  
   ![Wybieranie usług](media/azure-stack-create-plan/services.png)

6. Wybierz **przydziały** kartę. Obok pozycji **Microsoft.Storage**, w polu listy rozwijanej wybierz przydziału domyślnego, lub wybierz **Utwórz nowy** można utworzyć przydziału dostosowanego.
  
   ![Przydziały](media/azure-stack-create-plan/quotas.png)

7. Jeśli tworzysz nowy limit przydziału, wprowadź **nazwa** dla limitu przydziału, a następnie określ wartości limitu przydziału. Wybierz **OK** można utworzyć przydziału.

   ![Nowy limit przydziału](media/azure-stack-create-plan/new-quota.png)

8. Powtórz kroki 6 i 7, aby utworzyć i przypisać przydziały **Microsoft.Network** i **Microsoft.Compute**. Gdy wszystkie trzy usługi są przypisane przydziały, będzie wyglądać następnym przykładzie.

   ![Przydziały pełną limitu przydziału](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Wybierz **Przejrzyj + Utwórz** do przeglądu planu. Przejrzyj wszystkie wartości i przydziały, aby upewnić się, że są poprawne. Należy pamiętać, strzałki rozszerzeń po lewej stronie każdej pary limitu przydziału i usługi. Nowa funkcja umożliwia rozwijanie przydziały w wybranych planach, po kolei, aby wyświetlić szczegóły każdy przydział w planie i wróć do wprowadź niezbędne zmiany.

   ![Tworzenie planu](media/azure-stack-create-plan/create.png)

10. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** Aby utworzyć plan.

11. Aby wyświetlić nowy plan, wybierz **plany**, a następnie wyszukaj plan i wybierz odpowiednią nazwę. Jeśli lista zasobów jest długa, użyj **wyszukiwania** można zlokalizować planu według nazwy.

## <a name="create-a-plan-1901-and-earlier"></a>Utwórz plan (1901 i starszych)

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Aby utworzyć plan i ofertę, która może być subskrybowana przez użytkowników, wybierz **+ Utwórz zasób**, następnie **oferty i plany**, następnie **Plan**.
  
   ![Wybierz plan](media/azure-stack-create-plan/select-plan1901.png)

3. W obszarze **nowy plan**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, który użytkownicy mogą zobaczyć. Tylko administrator może zobaczyć nazwę zasobu, które Administratorzy użyć do pracy z planem jako zasobem usługi Azure Resource Manager.

   ![Określ szczegóły](media/azure-stack-create-plan/plan-name1901.png)

4. Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę, jako kontener dla planu.

   ![Określ grupę zasobów](media/azure-stack-create-plan/resource-group1901.png)

5. Wybierz **usług** a następnie zaznacz pole wyboru **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Następnie wybierz pozycję **wybierz** Aby zapisać konfigurację. Pola wyboru są wyświetlane, gdy wskaźnik myszy znajduje się nad każdej opcji.
  
   ![Wybieranie usług](media/azure-stack-create-plan/services1901.png)

6. Wybierz **przydziały**, **Microsoft.Storage (lokalne)**, a następnie wybierz domyślny limit przydziału lub wybierz **Tworzenie nowego limitu przydziału** można utworzyć przydziału dostosowanego.
  
   ![Przydziały](media/azure-stack-create-plan/quotas1901.png)

7. Jeśli tworzysz nowy limit przydziału, wprowadź **nazwa** dla limitu przydziału > określ wartości limitu przydziału > Wybierz **OK**. **Utwórz przydział** zamyka okno dialogowe.

   ![Nowy limit przydziału](media/azure-stack-create-plan/new-quota1901.png)

   Następnie wybierz nowy limit przydziału, który został utworzony. Wybranie przydział przypisuje do niego i zamyka okno dialogowe wyboru.
  
   ![Przypisz limitu przydziału](media/azure-stack-create-plan/assign-quota1901.png)

8. Powtórz kroki 6 i 7, aby utworzyć i przypisać przydziały **Microsoft.Network (lokalne)** i **Microsoft.Compute (lokalne)**. Gdy wszystkie trzy usługi są przypisane przydziały, będzie wyglądać następnym przykładzie.

   ![Przydziały pełną limitu przydziału](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. W obszarze **przydziały**, wybierz **OK**, a następnie w obszarze **nowy plan**, wybierz **Utwórz** Aby utworzyć plan.

    ![Tworzenie planu](media/azure-stack-create-plan/create1901.png)

10. Aby wyświetlić nowy plan, wybierz **wszystkie zasoby**, a następnie wyszukaj plan i wybierz odpowiednią nazwę. Jeśli lista zasobów jest długa, użyj **wyszukiwania** można zlokalizować planu według nazwy.

    ![Przejrzyj plan](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie oferty](azure-stack-create-offer.md)
