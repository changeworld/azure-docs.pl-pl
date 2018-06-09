---
title: Utwórz ofertę w stosie Azure | Dokumentacja firmy Microsoft
description: Jako administrator chmury Dowiedz się, jak utworzyć ofertę dla użytkowników w stosie Azure.
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
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247521"
---
# <a name="create-an-offer-in-azure-stack"></a>Tworzenie oferty w usłudze Azure Stack

[Oferuje](azure-stack-key-features.md) są grupami co najmniej jeden plan, które dostawców znajdujących się użytkowników, aby kupić lub subskrybować. Ten dokument przedstawia sposób tworzenia oferty, która obejmuje [utworzony plan](azure-stack-create-plan.md). Ta oferta zapewnia subskrybentów możliwość skonfigurowania maszyn wirtualnych.

1. Zaloguj się do portalu administratora platformy Azure stosu (https://adminportal.local.azurestack.external) i wybierz **nowy** > **oferuje dzierżawy + planów** > **oferują**.

   ![Tworzenie oferty](media/azure-stack-create-offer/image01.png)
  
2. W obszarze **oferują nowe**, wprowadź **Nazwa wyświetlana** i **Nazwa zasobu**, a następnie w obszarze **grupy zasobów**, wybierz pozycję **Utwórz Nowy** lub **Użyj istniejącego**. Nazwa wyświetlana jest przyjazna nazwa dla oferty. Przyjaznej nazwy, to tylko informacje o ofercie, które użytkownicy widzą po zasubskrybowaniu oferty. Użyj intuicyjne nazwy, która ułatwia użytkownikom zrozumienie, co jest dostarczany z oferty. Nazwa zasobu jest widoczna tylko dla administratora. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nowe oferty](media/azure-stack-create-offer/image01a.png)
  
3. Wybierz **podstawowa planów** otworzyć **Planowanie**. Wybierz planów chcesz dołączyć do oferty, a następnie wybierz pozycję **wybierz**. Aby utworzyć ofertę wybierz **Utwórz**.

   ![Wybierz plan](media/azure-stack-create-offer/image02.png)
  
4. Po utworzeniu oferty, można zmienić jego stanu. Musi być dokonana oferty *publicznego* użytkownikom uzyskanie pełnego widoku po ich subskrypcji. Oferty można:

   - **Publiczny**: widoczne dla użytkowników.
   - **Prywatne**: widoczne tylko dla administratorów w chmurze. To ustawienie jest przydatne podczas opracowywania planu lub oferty, lub jeśli administrator chmury chce [utworzyć każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowane**: zamknięte dla nowych subskrybentów. Administrator chmury można użyć wycofany z eksploatacji uniknąć przyszłych subskrypcji, pozostawiając jednak bieżący subskrybentów nie dotyczy.

   > [!TIP]  
   > Zmiany oferty nie są bezpośrednio widoczne dla użytkownika. Aby wyświetlić zmiany, użytkownicy mogą mieć Wyloguj się i zaloguj się ponownie w aplikacji portal użytkowników zobaczysz tę ofertę nowe.

   Aby zmienić stan oferty:

   - **Wersja 1803 i nowsze**:  
     W przeglądzie oferty, wybierz **stan dostępności**. Wybierz stan, którego chcesz użyć (na przykład *publicznego*), a następnie wybierz **zapisać**.
 
     ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/change-state.png)

     Alternatywnie, po uzyskaniu dostępu oferty można przejść do **oferują ustawienia**. Wybierz **stan dostępności** zmiany stanu.

   - **Przed wersją 1803**:  
     Wybierz **wszystkie zasoby**, Wyszukaj ofertę nowe, a następnie wybierz nowy oferty. Wybierz **zmiany stanu**, a następnie wybierz **publicznego**.

   > [!NOTE]
   > Można również utworzyć oferty domyślne, planów i przydziały za pomocą programu PowerShell. Aby uzyskać więcej informacji, zobacz [readme administratora usługi Azure stosu](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie subskrypcji](azure-stack-subscribe-plan-provision-vm.md)
- [Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
