---
title: Usuwanie laboratorium lub maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
description: W tym artykule pokazano, jak usunąć laboratorium lub usunąć maszynę wirtualną w laboratorium przy użyciu portalu Azure(Azure DevTest Labs).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270812"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Usuwanie laboratorium lub maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
W tym artykule pokazano, jak usunąć laboratorium lub maszynę wirtualną w laboratorium.

## <a name="delete-a-lab"></a>Usuwanie laboratorium
Po usunięciu wystąpienia DevTest Labs z grupy zasobów usługa DevTest Labs wykonuje następujące akcje: 

- Wszystkie zasoby, które zostały automatycznie utworzone w momencie tworzenia laboratorium są automatycznie usuwane. Sama grupa zasobów nie jest usuwana. Jeśli utworzono ręcznie wszystkie zasoby tej grupy zasobów, usługa nie usuwa ich. 
- Wszystkie maszyny wirtualne w laboratorium i grupy zasobów skojarzone z tymi maszynami wirtualnymi są automatycznie usuwane. Podczas tworzenia maszyny Wirtualnej w laboratorium usługa tworzy zasoby (dysk, interfejs sieciowy, publiczny adres IP itp.) dla maszyny Wirtualnej w oddzielnej grupie zasobów. Jeśli jednak ręcznie utworzysz dodatkowe zasoby w tych grupach zasobów, usługa DevTest Labs nie usunie tych zasobów i grupy zasobów. 

Aby usunąć laboratorium, wykonaj następujące czynności: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu po lewej stronie, wybierz **DevTest Labs** dla typu usługi i wybierz laboratorium.

    ![Wybierz swoje laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na stronie **Laboratorium testów deweloperskich** kliknij pozycję **Usuń** na pasku narzędzi. 

    ![Przycisk Usuń](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na stronie **Potwierdzenie** wprowadź **nazwę** laboratorium i wybierz pozycję **Usuń**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Aby wyświetlić stan operacji, wybierz ikonę **Powiadomienia** (Dzwonek). 

    ![Powiadomienia](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Usuwanie maszyny wirtualnej w laboratorium
Jeśli usunę maszynę wirtualną w laboratorium, niektóre zasoby (nie wszystkie), które zostały utworzone w momencie tworzenia laboratorium są usuwane. Następujące zasoby nie są usuwane: 

-   Magazyn kluczy w głównej grupie zasobów
-   Zestaw dostępności, moduł równoważenia obciążenia, publiczny adres IP w grupie zasobów maszyny Wirtualnej. Te zasoby są współużytkowane przez wiele maszyn wirtualnych w grupie zasobów. 

Maszyna wirtualna, interfejs sieciowy i dysk skojarzony z maszyną wirtualną są usuwane. 

Aby usunąć maszynę wirtualną w laboratorium, wykonaj następujące czynności: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu po lewej stronie, wybierz **DevTest Labs** dla typu usługi i wybierz laboratorium.

    ![Wybierz swoje laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Wybierz **... (wielokropek)** dla maszyny Wirtualnej na liście maszyn wirtualnych i wybierz pozycję **Usuń**. 

    ![Usuwanie maszyny wirtualnej w menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. W oknie dialogowym **potwierdzenia** **wybierz**ok . 
5. Aby wyświetlić stan operacji, wybierz ikonę **Powiadomienia** (Dzwonek). 

Aby usunąć maszynę wirtualną ze **strony maszyny wirtualnej,** wybierz pozycję **Usuń** z paska narzędzi, jak pokazano na poniższej ilustracji:

![Usuwanie maszyny wirtualnej ze strony maszyny Wirtualnej](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz utworzyć laboratorium, zobacz następujące artykuły: 

- [Tworzenie laboratorium](devtest-lab-create-lab.md)
- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)