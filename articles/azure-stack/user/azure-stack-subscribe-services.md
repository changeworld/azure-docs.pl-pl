---
title: W tym samouczku dowiesz się, jak subskrybować ofertę usługi Azure Stack | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć nową subskrypcję do usługi Azure Stack i przetestować ofertę, tworząc testowej maszyny wirtualnej.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0e2fa9b01d27d68c1eab9097a20b6e350ba47f99
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028818"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Samouczek: tworzenie i testowanie subskrypcji
W tym samouczku dowiesz się, jak utworzyć subskrypcję zawierającą oferty i przetestuj. Dla testu będzie zalogować się do portalu użytkowników usługi Azure Stack jako administrator w chmurze, subskrybowaniu oferty, a następnie utwórz maszynę wirtualną.

> [!TIP]
> Dla bardziej zaawansowanych próbnego, możesz [Utwórz subskrypcję dla danego użytkownika](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) i następnie zaloguj się jako ten użytkownik w aplikacji portal użytkowników. 

W tym samouczku dowiesz się, jak subskrybować ofertę usługi Azure Stack.

Co dowiesz się:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty

## <a name="subscribe-to-an-offer"></a>Subskrybowanie oferty
Aby subskrybować ofertę jako użytkownik, należy zalogować się do portalu użytkownika usługi Azure Stack do odnajdywania usługi oferowane przez operatora infrastruktury Azure Stack.

1. Zaloguj się do portalu użytkowników, a następnie kliknij przycisk **Uzyskaj subskrypcję**.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-services/get-subscription.png)

2. W polu **Nazwa wyświetlana** wpisz nazwę subskrypcji. Następnie kliknij przycisk **oferują** można wybrać jedną z dostępnych ofert w **wybierz ofertę** sekcji, a następnie kliknij przycisk **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Teraz musisz odświeżyć portal użytkowników, aby rozpocząć korzystanie z subskrypcji.

3. Aby wyświetlić subskrypcji, którą utworzono, kliknij **wszystkich usług**.  Następnie w obszarze **ogólne** wybierz kategorię **subskrypcje**, a następnie wybierz nową subskrypcję. Po subskrybowaniu oferty, Odśwież portal aby zobaczyć, jeśli nowe usługi zostały zawarte w ramach nowej subskrypcji. W tym przykładzie **maszyn wirtualnych** został dodany.

   ![Widok subskrypcji](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testowanie oferty
Po zalogowaniu się do portalu użytkowników, możesz przetestować ofertę, inicjowanie obsługi administracyjnej maszyny wirtualnej za pomocą nowych możliwości subskrypcji. 

> [!NOTE]
> Ten test wymaga, czy maszyny Wirtualnej z systemem Windows Server 2016 Datacenter najpierw został dodany do portalu marketplace usługi Azure Stack. 

1. Zaloguj się do portalu użytkowników.

2. W aplikacji portal użytkowników, kliknij przycisk **maszyn wirtualnych** > **Dodaj** > **systemu Windows Server 2016 Datacenter**, a następnie kliknij przycisk **Create** .

3. W **podstawy** sekcji, wpisz **nazwa**, **nazwa_użytkownika**, i **hasło**, wybierz **subskrypcji**, Tworzenie **grupy zasobów** (lub wybierz istniejącą grupę), a następnie kliknij przycisk **OK**.

4. W **wybierz rozmiar** kliknij **standardowa A1**, a następnie kliknij przycisk **wybierz**.  

5. W bloku ustawienia, zaakceptuj wartości domyślne, a następnie kliknij przycisk **OK**.

6. W **Podsumowanie** kliknij **OK** do utworzenia maszyny wirtualnej.  

7. Aby wyświetlić swoją nową maszyną wirtualną, kliknij **maszyn wirtualnych**, a następnie wyszukaj nową maszynę wirtualną i kliknij jego nazwę.

    ![Wszystkie zasoby](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Wdrażanie maszyny wirtualnej potrwa kilka minut.


## <a name="next-steps"></a>Kolejne kroki

Przedstawiono w tym samouczku:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty


> [!div class="nextstepaction"]
> [Tworzenie maszyny Wirtualnej na podstawie szablonu społeczności](azure-stack-create-vm-template.md)