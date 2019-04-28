---
title: Uruchom ponownie Maszynę wirtualną w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponownie uruchomić maszynę wirtualną w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864425"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Uruchom ponownie Maszynę wirtualną w laboratorium Azure DevTest Labs
Można szybko i łatwo rozpocząć maszynę wirtualną w usłudze DevTest Labs, wykonując kroki opisane w tym artykule. Należy wziąć pod uwagę następujące przed ponownym uruchomieniem maszyny Wirtualnej:

- Maszyna wirtualna musi być uruchomiona, aby włączyć funkcję ponownego uruchamiania.
- Jeśli użytkownik jest podłączony do uruchomionej maszyny Wirtualnej, gdy wykonują ponowne uruchomienie komputera, ich należy nawiązać ponownie połączenie z maszyną wirtualną po jej ponownym uruchomieniu.
- Jeśli artefakt jest stosowany po ponownym uruchomieniu maszyny Wirtualnej, pojawi się ostrzeżenie artefaktu nie może być stosowane.

    ![Ostrzeżenie podczas ponownego uruchamiania podczas stosowania artefaktów](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Jeśli maszyna wirtualna został zatrzymany podczas stosowania artefakt, można użyć funkcji ponownego uruchomienia maszyny Wirtualnej jako potencjalne sposób, aby rozwiązać ten problem.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Kroki, aby ponownie uruchomić Maszynę wirtualną w laboratorium Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Zaznacz na liście laboratoriów laboratorium, która zawiera maszyny Wirtualnej, który chcesz ponownie uruchomić.
1. W panelu po lewej stronie wybierz **Moje maszyny wirtualne**.
1. Z listy maszyn wirtualnych wybierz uruchomionej maszyny Wirtualnej.
1. W górnej części okienka zarządzania maszyny Wirtualnej wybierz **ponowne uruchomienie**.

    ![Ponowne uruchomienie maszyny Wirtualnej, przycisk](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorować stan ponownego uruchomienia, wybierając **powiadomienia** ikonę w prawym górnym rogu okna.

    ![Wyświetlanie stanu ponownego uruchamiania maszyny Wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Można również uruchomić ponownie uruchomionej maszyny Wirtualnej, wybierając jego ikonę wielokropka (...) na liście **Moje maszyny wirtualne**.

![Uruchom ponownie maszynę Wirtualną przy użyciu wielokropka](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Kolejne kroki
* Po ponownym uruchomieniu, można ponownie połączyć z maszyną wirtualną, wybierając **Connect** w jej okienku zarządzania.
* Zapoznaj się z [galerii szablonów szybkiego startu usługi DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
