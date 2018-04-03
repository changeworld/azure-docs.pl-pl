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
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Tworzenie oferty w usłudze Azure Stack

[Oferuje](azure-stack-key-features.md) są grupami co najmniej jeden plan udostępniające dostawców do użytkowników w celu zakupu lub subskrybować. Ten dokument przedstawia sposób tworzenia oferty, która obejmuje [utworzony plan](azure-stack-create-plan.md) w ostatnim kroku. Ta oferta umożliwia subskrybentów na umieszczanie maszyn wirtualnych.

1. Zaloguj się do portalu administratora platformy Azure stosu (https://adminportal.local.azurestack.external) > kliknij **nowy** > **oferuje dzierżawy + planów** > **oferują**.

   ![](media/azure-stack-create-offer/image01.png)
2. W **oferują nowe** wypełnienia w okienku **Nazwa wyświetlana** i **Nazwa zasobu**, a następnie wybierz nowy lub istniejący **grupy zasobów**. Nazwa wyświetlana jest przyjazna nazwa dla oferty. Przyjaznej nazwy, to tylko informacje o ofercie, który jest wyświetlany podczas subskrybowania. W związku z tym należy użyć nazwy intuicyjny, która ułatwia użytkownikom zrozumienie, co jest dostarczany z oferty. Nazwa zasobu jest widoczna tylko dla administratora. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![](media/azure-stack-create-offer/image01a.png)
3. Kliknij przycisk **podstawowa planów** otworzyć **Planowanie** okienku wybierz planów chcesz dołączyć do oferty, a następnie kliknij przycisk **wybierz**. Kliknij pozycję **Utwórz**, aby utworzyć ofertę.

   ![](media/azure-stack-create-offer/image02.png)
4. Po utworzeniu oferty, można zmienić jego stanu. Musi być dokonana oferty *publicznego* użytkownikom uzyskanie pełnego widoku po ich subskrypcji. Oferty można:
   - **Publiczny**: widoczne dla użytkowników.
   - **Prywatne**: widoczne tylko dla administratorów w chmurze. Przydatne podczas opracowywania planu lub oferty, lub jeśli administrator chmury chce [utworzyć każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowane**: zamknięte dla nowych subskrybentów. Administrator chmury można użyć wycofany z eksploatacji uniknąć przyszłych subskrypcji, pozostawiając jednak bieżący subskrybentów bez zmian.

   > [!TIP]  
   > Zmiany do oferty, nie są bezpośrednio widoczne dla użytkowników. Aby wyświetlić zmiany, użytkownicy mogą mieć Wyloguj się i zaloguj ponownie do aplikacji portal użytkowników, aby zobaczyć nowe oferty. 

   Aby zmienić stan oferty: 

   - **Wersja 1803 i nowsze**:  
     W bloku Przegląd oferty, kliknij **stanu dostępności**, wybierz stan, którego chcesz użyć, takie jak *publicznego*, a następnie kliknij przycisk **zapisać**. 
 
     ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/change-state.png) 

     Alternatywnie po dostępu oferty można przejdź do **oferują ustawienia**, a następnie wybierz **stanu dostępności** zmiany stanu. 

   - **Przed wersją 1803**:  
     Kliknij przycisk **wszystkie zasoby**wyszukać nowe ofertę, kliknij na nowej oferty, kliknij przycisk **zmiany stanu**, a następnie kliknij przycisk **publicznego**.

  
   > [!NOTE] 
   > Można również utworzyć oferty domyślne, planów i przydziały za pomocą programu PowerShell. Aby uzyskać więcej informacji, zobacz [readme administratora usługi Azure stosu](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
   >


### <a name="next-steps"></a>Kolejne kroki
[Tworzenie subskrypcji](azure-stack-subscribe-plan-provision-vm.md)      
[Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
