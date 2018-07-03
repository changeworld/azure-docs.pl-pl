---
title: Tworzenie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator chmury Dowiedz się, jak utworzyć ofertę dla użytkowników w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/2/2018
ms.author: brenduns
ms.openlocfilehash: eed715a7c2cb967f6c9ea0b7d4442a4f9976bd17
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345893"
---
# <a name="create-an-offer-in-azure-stack"></a>Tworzenie oferty w usłudze Azure Stack

[Oferuje](azure-stack-key-features.md) grup co najmniej jeden plan, które dostawcy przedstawiają użytkownikom kupowanie lub subskrybować. W tym dokumencie pokazano, jak utworzyć ofertę, która obejmuje [planu, który został utworzony](azure-stack-create-plan.md). Ta oferta umożliwia subskrybentom Konfigurowanie maszyn wirtualnych.

1. Zaloguj się do portalu administratora usługi Azure Stack (https://adminportal.local.azurestack.external) i wybierz **New** > **dzierżawy oferty i plany** > **oferują**.

   ![Tworzenie oferty](media/azure-stack-create-offer/image01.png)
  
2. W obszarze **nowa oferta**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**, a następnie w obszarze **grupy zasobów**, wybierz opcję **Create nowe** lub **Użyj istniejącej**. Nazwa wyświetlana jest przyjazną nazwę dla oferty. Ta przyjazna nazwa jest tylko informacje dotyczące oferty, którą widzą użytkownicy Po subskrybowaniu oferty. Użyj nazwy intuicyjne, która pomaga użytkownikom zrozumieć, co jest dostarczane z tej oferty. Nazwa zasobu jest widoczna tylko dla administratora. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nowa oferta](media/azure-stack-create-offer/image01a.png)
  
3. Wybierz **plany bazowe** otworzyć **Planowanie**. Wybierz plany, aby uwzględnić w ofercie, a następnie wybierz **wybierz**. Aby utworzyć wybierz ofertę **Utwórz**.

   ![Wybierz plan](media/azure-stack-create-offer/image02.png)
  
4. Po utworzeniu oferty, możesz zmienić jego stan. Oferty muszą być wykonane *publicznych* dla użytkowników uzyskać pełny widok podczas subskrybowania. Może być oferty:

   - **Publiczne**: widoczne dla użytkowników.
   - **Prywatne**: widoczne tylko dla administratorów w chmurze. To ustawienie jest przydatne podczas opracowywania planu lub oferty, lub jeśli chce się z administratorem chmury [tworzenia każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowane**: zamknięte dla nowych subskrybentów. Administrator chmury można użyć stanu zlikwidowane, aby uniemożliwić przyszłe subskrypcje, ale pozostawić bieżących subskrybentów bez zmian.

   > [!TIP]  
   > Zmiany tej oferty nie są bezpośrednio widoczne dla użytkownika. Aby zobaczyć zmiany, użytkownicy mogą mieć wylogowania się i zaloguj się ponownie do portalu użytkowników będzie nowej oferty.

   Aby zmienić stan oferty:

   - **W wersji 1803 i nowszych**:  
     W obszarze Przegląd dla oferty wybierz **stan ułatwień dostępu**. Wybierz stan, w której chcesz użyć (na przykład *publicznych*), a następnie wybierz **Zapisz**.
 
     ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/change-state.png)

     Alternatywnie, po uzyskaniu dostępu ofertę możesz przejść do **ustawienia oferty**. Wybierz **stan ułatwień dostępu** zmiany stanu.

   - **Przed wersją 1803**:  
     Wybierz **wszystkie zasoby**, Wyszukaj nowe oferty, a następnie wybierz nową ofertę. Wybierz **zmiany stanu**, a następnie wybierz pozycję **publicznych**.

   > [!NOTE]
   > Umożliwia także środowiska PowerShell do utworzenia domyślnego oferty, przydziały i planów. Aby uzyskać więcej informacji, zobacz [modułu programu PowerShell usługi Azure Stack 1.3.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie subskrypcji](azure-stack-subscribe-plan-provision-vm.md)
- [Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
