---
title: Z tego samouczka, dowiesz się subskrybowanie oferty Azure stosu | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak utworzyć nową subskrypcję do usług Azure stosu i przetestować oferty przez utworzenie testowej maszyny wirtualnej.
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
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238511"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Samouczek: tworzenie i testowanie subskrypcji
W tym samouczku przedstawiono sposób tworzenia subskrypcji, zawierające oferty, a następnie przetestujemy go. Dla testu będzie zalogować się do portalu użytkowników stosu Azure jako administrator chmury, subskrybować oferty, a następnie utwórz maszynę wirtualną.

> [!TIP]
> Bardziej zaawansowanych wersji ewaluacyjnej środowisko pracy, możesz [Utwórz subskrypcję dla określonego użytkownika](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) , a następnie zaloguj się jako użytkownik w portalu użytkowników. 

W tym samouczku przedstawiono sposób subskrybowania oferty Azure stosu.

Co dowiesz się:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty

## <a name="subscribe-to-an-offer"></a>Subskrybowanie oferty
Aby subskrybować ofertę jako użytkownik, musisz zalogować się do portalu użytkowników stosu Azure do odnajdywania usługi, które są oferowane przez podmiot stosu Azure.

1. Zaloguj się do portalu użytkowników, a następnie kliknij przycisk **uzyskania subskrypcji**.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-services/get-subscription.png)

2. W polu **Nazwa wyświetlana** wpisz nazwę subskrypcji. Następnie kliknij przycisk **oferują** można wybrać jedną z dostępnych ofert w **wybierz ofertę** sekcji, a następnie kliknij przycisk **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Teraz należy odświeżyć portal użytkowników, aby rozpocząć korzystanie z subskrypcji.

3. Aby wyświetlić utworzoną subskrypcję, kliknij przycisk **więcej usług**, kliknij przycisk **subskrypcje**, następnie kliknij przycisk nowej subskrypcji. Po zasubskrybowaniu ofertę odświeżyć portalu, aby zobaczyć, czy nowe usługi zostały zawarte w ramach nowej subskrypcji. W tym przykładzie **maszyn wirtualnych** został dodany.

   ![Widok subskrypcji](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testowanie oferty
Po zalogowaniu się do portalu użytkowników, możesz przetestować oferty obsługi maszyny wirtualnej przy użyciu nowych funkcji subskrypcji. 

> [!NOTE]
> Ten test wymaga, czy maszyna wirtualna centrum danych systemu Windows Server 2016 najpierw został dodany do stosu Azure marketplace. 

1. Zaloguj się do portalu użytkowników.

2. W portalu użytkowników, kliknij przycisk **maszyn wirtualnych** > **Dodaj** > **systemu Windows Server Datacenter 2016**, a następnie kliknij przycisk **Utwórz** .

3. W **podstawy** wpisz **nazwa**, **nazwy użytkownika**, i **hasło**, wybierz **subskrypcji**, Utwórz **grupy zasobów** (lub wybrać istniejącą), a następnie kliknij przycisk **OK**.

4. W **wybierz rozmiar** kliknij **A1 standardowe**, a następnie kliknij przycisk **wybierz**.  

5. W bloku ustawienia zaakceptuj wartości domyślne, a następnie kliknij przycisk **OK**.

6. W **Podsumowanie** kliknij **OK** można utworzyć maszyny wirtualnej.  

7. Aby zapoznać się z nowej maszyny wirtualnej, kliknij przycisk **maszyn wirtualnych**, następnie wyszukaj nowej maszyny wirtualnej i kliknij jego nazwę.

    ![Wszystkie zasoby](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Wdrożenie maszyny wirtualnej potrwa kilka minut.


## <a name="next-steps"></a>Kolejne kroki

Przedstawiono w tym samouczku:

> [!div class="checklist"]
> * Subskrybowanie oferty 
> * Testowanie oferty


> [!div class="nextstepaction"]
> [Utwórz maszynę Wirtualną z szablonu społeczności](azure-stack-create-vm-template.md)