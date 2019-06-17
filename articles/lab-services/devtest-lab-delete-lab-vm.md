---
title: Usuń maszynę Wirtualną lub laboratorium w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak usunąć laboratorium lub maszyny Wirtualnej w laboratorium.
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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 9634c70566aba21bdd28ee016c9fa94464ec9c1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127416"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Usuń maszynę Wirtualną lub laboratorium w laboratorium Azure DevTest Labs
W tym artykule pokazano, jak usunąć laboratorium lub maszyny Wirtualnej w laboratorium.

## <a name="delete-a-lab"></a>Usuń laboratorium
Gdy wystąpienie usługi DevTest Labs zostanie usunięty z grupy zasobów, usługa DevTest Labs wykonuje następujące czynności: 

- Wszystkie zasoby, które zostały automatycznie utworzone w czasie tworzenia laboratorium są automatycznie usuwane. Samej grupy zasobów nie zostanie usunięty. Jeśli wszystkie zasoby została ręcznie utworzona ta grupa zasobów, usługa nie powoduje ich usunięcia. 
- Wszystkie maszyny wirtualne w laboratorium i grup zasobów skojarzonych z tych maszyn wirtualnych są automatycznie usuwane. Podczas tworzenia maszyny Wirtualnej w laboratorium, usługa tworzy zasobów (dysk, interfejs sieciowy, publiczny adres IP, itp.) dla maszyny Wirtualnej w oddzielnej grupie zasobów. Jednak jeśli ręcznie utworzysz dodatkowe zasoby w tych grupach zasobów, usługa DevTest Labs nie powoduje usunięcia tych zasobów i grupy zasobów. 

Aby usunąć laboratorium, wykonaj następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich zasobów** w menu po lewej stronie wybierz pozycję **DevTest Labs** dla typu usługi, a następnie wybierz laboratorium.

    ![Wybierz swoje laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na **laboratorium DevTest Lab** kliknij **Usuń** na pasku narzędzi. 

    ![Usuwanie przycisku](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na **potwierdzenie** wpisz **nazwa** swoje laboratorium, a następnie wybierz pozycję **Usuń**. 

    ![Potwierdź](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Aby wyświetlić stan operacji, wybierz **powiadomienia** ikonę (dzwonek). 

    ![Powiadomienia](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Usuwanie maszyny Wirtualnej w laboratorium
Jeśli usunę maszyny Wirtualnej w laboratorium, niektóre (nie wszystkich) zasobów, które zostały utworzone w czasie tworzenia laboratorium są usuwane. Poniższe zasoby nie zostaną usunięte: 

-   Magazyn w grupie zasobów głównego kluczy
-   Dostępność zestawu, usługi load balancer, publiczny adres IP w grupie zasobów maszyny Wirtualnej. Te zasoby są współużytkowane przez wiele maszyn wirtualnych w grupie zasobów. 

Maszyna wirtualna, interfejs sieciowy i dysku skojarzonego z maszyną Wirtualną są usuwane. 

Aby usunąć maszyny Wirtualnej w laboratorium, wykonaj następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich zasobów** w menu po lewej stronie wybierz pozycję **DevTest Labs** dla typu usługi, a następnie wybierz laboratorium.

    ![Wybierz swoje laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Wybierz **... (wielokropek)**  dla maszyny Wirtualnej na liście maszyn wirtualnych, a następnie wybierz pozycję **Usuń**. 

    ![Usuwanie maszyny Wirtualnej w menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Na **potwierdzenie** okno dialogowe, wybierz opcję **Ok**. 
5. Aby wyświetlić stan operacji, wybierz **powiadomienia** ikonę (dzwonek). 

Aby usunąć maszynę Wirtualną z **stronie maszyny wirtualnej**, wybierz opcję **Usuń** na pasku narzędzi, jak pokazano na poniższej ilustracji:

![Usuwanie maszyny Wirtualnej na stronie maszyny Wirtualnej](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz utworzyć laboratorium, zobacz następujące artykuły: 

- [Tworzenie laboratorium](devtest-lab-create-lab.md)
- [Dodawanie maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md)