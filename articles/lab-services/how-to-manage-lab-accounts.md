---
title: Zarządzanie kontami laboratorium w usłudze Azure laboratorium Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć konto laboratorium, Wyświetl wszystkie konta laboratorium i usuwania konta laboratorium w subskrypcji platformy Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 53494abead5701052f6e08f68b01ccdf1bfa211c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Zarządzanie kontami laboratorium w usłudze Azure laboratorium Services (poprzednio Azure DevTest Labs)
W tym artykule opisano, jak utworzyć konto laboratorium, wyświetlania wszystkich kont laboratorium lub usunąć konto laboratorium.

## <a name="create-a-lab-account"></a>Utwórz konto laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz z menu głównego po lewej stronie **Utwórz zasób**.
3. Wyszukaj **usługi laboratorium** w portalu Azure Marketplace, a następnie wybierz **usługi laboratorium** na liście rozwijanej. 
4. Wybierz **Sercices laboratorium (wersja zapoznawcza)** flitered na liście usług. 
5. W **Utwórz konto laboratorium** wybierz **Utwórz**.
7. W **konta laboratorium** okna, wykonaj następujące czynności: 
    1. Aby uzyskać **nazwa konta laboratorium**, wprowadź nazwę. 
    2. Wybierz **subskrypcji platformy Azure** , w której chcesz utworzyć konto laboratorium.
    3. Dla **grupy zasobów**, wybierz pozycję **Utwórz nowy**, a następnie wprowadź nazwę grupy zasobów.
    4. Aby uzyskać **lokalizacji**, wybierz lokalizację/regionu, w którym mają konta laboratorium ma zostać utworzony. 
    5. Wybierz pozycję **Utwórz**. 

        ![Utworzenie okna konta laboratorium](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Jeśli widzisz stronę dla konta laboratorium, wybierz **powiadomienia** przycisk, a następnie kliknij przycisk **przejdź do zasobu** przycisk w powiadomieniach. 

    ![Utworzenie okna konta laboratorium](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Zobacz następujące tematy **konta laboratorium** strony:

    ![Strona konta laboratorium](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby zapewnić instytucjom uprawnienia do tworzenia laboratoria ich klasy, należy je dodać do roli twórcy laboratorium:

1. Na **konta laboratorium** wybierz pozycję **(IAM) kontroli dostępu**i kliknij przycisk **+ Dodaj** na pasku narzędzi. 

    ![Strona konta laboratorium](./media/tutorial-setup-lab-account/access-control.png)
2. Na **dodać uprawnienia** wybierz pozycję **twórcy laboratorium** dla **roli**, wybierz użytkownika, aby dodać do roli twórcy laboratorium, a następnie wybierz **zapisać**. 

    ![Dodaj użytkownika do roli twórcy laboratorium](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Wyświetlanie kont laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu. 
3. Wybierz **usługi laboratorium** dla **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacji i tagów. 

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Wykonaj instrukcje z poprzedniej sekcji, która wyświetla na liście kont laboratorium. Użyj poniższych instrukcji, aby usunąć konto laboratorium: 

1. Wybierz **konta laboratorium** przewidzianą do usunięcia. 
2. Wybierz **usunąć** z paska narzędzi. 
3. Typ **tak** o potwierdzenie.
4. Wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usług Azure laboratorium:

- [Konfigurowanie laboratorium klas](tutorial-setup-classroom-lab.md)
- [Konfigurowanie niestandardowych laboratorium](tutorial-create-custom-lab.md)
