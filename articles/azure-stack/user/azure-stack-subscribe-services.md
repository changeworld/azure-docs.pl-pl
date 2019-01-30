---
title: W tym samouczku dowiesz się, jak subskrybować ofertę usługi Azure Stack | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć nową subskrypcję do usługi Azure Stack i przetestować ofertę, tworząc testowej maszyny wirtualnej.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 4cc2adc334ede3f2377722137d4a6c84544bf425
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251483"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Samouczek: tworzenie i testowanie subskrypcji

W tym samouczku dowiesz się, jak utworzyć subskrypcję zawierającą oferty i przetestuj. Dla testu Zaloguj się do aplikacji portal użytkowników usługi Azure Stack jako administrator chmury subskrybowaniu oferty, a następnie utwórz maszynę wirtualną.

> [!TIP]
> Dla bardziej zaawansowanych próbnego, możesz [Utwórz subskrypcję dla danego użytkownika](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) a następnie zaloguj się jako ten użytkownik w aplikacji portal użytkowników. 

W tym samouczku dowiesz się, jak subskrybować ofertę usługi Azure Stack.

Co dowiesz się:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty

## <a name="subscribe-to-an-offer"></a>Subskrybowanie oferty

Aby subskrybować ofertę jako użytkownik, logowaniu do portalu użytkowników usługi Azure Stack do odnajdywania usługi oferowane przez operatora infrastruktury Azure Stack.

1. Zaloguj się do użytkownika w portalu i wybierz pozycję **Uzyskaj subskrypcję**.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-services/get-subscription.png)

2. W polu **Nazwa wyświetlana** wpisz nazwę subskrypcji. Następnie wybierz pozycję **oferują** wybrać jeden z dostępnych ofert w **wybierz ofertę** sekcji. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Teraz musisz odświeżyć portal użytkowników, aby rozpocząć korzystanie z subskrypcji.

3. Zaznacz, aby wyświetlić subskrypcji, którą utworzono **wszystkich usług**. Następnie w obszarze **ogólne** wybierz kategorię **subskrypcje**, a następnie wybierz nową subskrypcję. Po subskrybowaniu oferty, Odśwież portal aby zobaczyć, jeśli nowe usługi zostały zawarte w ramach nowej subskrypcji. W tym przykładzie **maszyn wirtualnych** został dodany.

   ![Widok subskrypcji](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testowanie oferty

Gdy zalogowany do portalu użytkowników, możesz przetestować ofertę, obsługi maszyny wirtualnej za pomocą nowych możliwości subskrypcji. 

> [!NOTE]
> Ten test wymaga, czy maszyny Wirtualnej z systemem Windows Server 2016 Datacenter najpierw został dodany do portalu marketplace usługi Azure Stack. 

1. Zaloguj się do portalu użytkowników.

2. W aplikacji portal użytkowników, wybierz **maszyn wirtualnych**, następnie **Dodaj**, następnie **systemu Windows Server 2016 Datacenter**, a następnie kliknij przycisk **Utwórz**.

3. W **podstawy** sekcji, wpisz **nazwa**, **nazwa_użytkownika**, i **hasło**, wybierz **subskrypcji**, Tworzenie **grupy zasobów** (lub wybierz istniejącą grupę), a następnie wybierz pozycję **OK**.

4. W **wybierz rozmiar** zaznacz **standardowa A1**, a następnie kliknij przycisk **wybierz**.  

5. W **ustawienia** bloku, zaakceptuj wartości domyślne i wybierz **OK**.

6. W **Podsumowanie** kliknij **OK** do utworzenia maszyny wirtualnej.  

7. Aby wyświetlić swoją nową maszyną wirtualną, wybierz **maszyn wirtualnych**, a następnie wyszukaj nową maszynę wirtualną i kliknij jego nazwę.

    ![Wszystkie zasoby](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Wdrażanie maszyny wirtualnej trwa kilka minut.


## <a name="next-steps"></a>Kolejne kroki

Przedstawiono w tym samouczku:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty


> [!div class="nextstepaction"]
> [Tworzenie maszyny Wirtualnej na podstawie szablonu społeczności](azure-stack-create-vm-template.md)