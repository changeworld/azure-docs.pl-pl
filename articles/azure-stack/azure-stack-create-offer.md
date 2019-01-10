---
title: Tworzenie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator chmury Dowiedz się, jak utworzyć ofertę dla użytkowników w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 079f45e37bef24ac974a0e2df7b1e81f1002cac0
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159081"
---
# <a name="create-an-offer-in-azure-stack"></a>Tworzenie oferty w usłudze Azure Stack

[Oferuje](azure-stack-key-features.md) grup co najmniej jeden plan, które dostawcy przedstawiają użytkowników, których Ci użytkownicy mogą kupić lub subskrybować. W tym artykule opisano, jak utworzyć ofertę obejmującą [planu, który został utworzony](azure-stack-create-plan.md). Ta oferta zapewnia subskrybentom możliwość konfigurowania maszyn wirtualnych (VM).

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external) i wybierz **+ Utwórz zasób**, następnie **dzierżawy oferty i plany**, a następnie **oferują**.

   ![Tworzenie oferty](media/azure-stack-create-offer/image01.png)
  
2. W obszarze **nowa oferta**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**, a następnie w obszarze **grupy zasobów**, wybierz opcję **Create nowe** lub **Użyj istniejącej**. Nazwa wyświetlana jest przyjazną nazwę dla oferty. Ta przyjazna nazwa jest tylko informacje dotyczące oferty, którą widzą użytkownicy Po subskrybowaniu oferty. Użyj nazwy intuicyjne, która pomaga użytkownikom zrozumieć, co jest dostarczane z tej oferty. Tylko administrator może zobaczyć nazwy zasobu. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nowa oferta](media/azure-stack-create-offer/image01a.png)
  
3. Wybierz **plany bazowe** otworzyć **Planowanie**. Wybierz plany, aby uwzględnić w ofercie, a następnie wybierz **wybierz**. Aby utworzyć wybierz ofertę **Utwórz**.

   ![Wybierz plan](media/azure-stack-create-offer/image02.png)
  
4. Po utworzeniu oferty, możesz zmienić jego stan. Oferty muszą być wykonane **publicznych** dla użytkowników uzyskać pełny widok podczas subskrybowania. Może być oferty:

   - **Publiczne**: Widoczne dla użytkowników.
   - **Prywatne**: Jest to widoczne tylko dla administratorów w chmurze. To ustawienie jest przydatne podczas opracowywania planu lub oferty, lub jeśli chce się z administratorem chmury [tworzenia każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowana**: Zamknięte dla nowych subskrybentów. Administrator chmury można zlikwidować oferty, aby uniemożliwić przyszłe subskrypcje, ale pozostawić bieżących subskrybentów bez zmian.

   > [!TIP]  
   > Zmiany w ramach oferty nie są bezpośrednio widoczne dla użytkownika. Aby zobaczyć zmiany, użytkownicy mogą mieć wylogowania się i zaloguj się ponownie do portalu użytkowników będzie nowej oferty.

   Na ekranie Przegląd dla oferty, wybierz **stan ułatwień dostępu**. Wybierz stan, w której chcesz użyć (na przykład **publicznych**), a następnie wybierz pozycję **Zapisz**.

     ![Wybierz stan](media/azure-stack-create-offer/change-stage-1807.png)

     Jako alternatywę, wybierz **zmiany stanu** , a następnie wybierz stan.

    ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > Umożliwia także środowiska PowerShell do utworzenia domyślnego oferty, przydziały i planów. Aby uzyskać więcej informacji, zobacz [modułu programu PowerShell usługi Azure Stack 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie subskrypcji](azure-stack-subscribe-plan-provision-vm.md)
- [Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
