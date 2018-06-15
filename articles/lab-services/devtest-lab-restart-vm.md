---
title: Uruchom ponownie Maszynę wirtualną w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponownego uruchomienia maszyny wirtualnej w usłudze Azure DevTest Labs
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
ms.openlocfilehash: 24a17ce09bee1097b0418ad4e20990d359b3e084
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787367"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Uruchom ponownie Maszynę wirtualną w laboratorium w usłudze Azure DevTest Labs
Można szybko i łatwo ponownego uruchomienia maszyny wirtualnej w usłudze DevTest Labs wykonując kroki opisane w tym artykule. Należy rozważyć przed ponownym uruchomieniem maszyny Wirtualnej:

- Maszyna wirtualna musi być uruchomiona, aby włączyć funkcję ponownego uruchamiania.
- Jeśli użytkownik jest podłączony do uruchomionej maszyny Wirtualnej, podczas ponownego uruchomienia komputera, one należy nawiązać ponownie połączenie z maszyną wirtualną po jego uruchomieniu, Utwórz kopię zapasową.
- Jeśli artefaktu jest stosowany podczas ponownego uruchamiania maszyny Wirtualnej, pojawi się ostrzeżenie artefakt nie mogą zostać zastosowane. 

    ![Ostrzeżenie podczas ponownego uruchamiania podczas stosowania artefaktów](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Jeśli maszyna wirtualna został zatrzymany podczas stosowania artefaktu, można użyć funkcji ponownego uruchomienia maszyny Wirtualnej jako potencjalny sposób, aby rozwiązać ten problem.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Kroki, aby ponownie uruchomić Maszynę wirtualną w laboratorium w usłudze Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz laboratorium, która obejmuje maszyny Wirtualnej, który chcesz ponownie uruchomić.  
1. W lewym panelu, wybierz **Moje maszyny wirtualne**. 
1. Wybierz uruchomionej maszyny Wirtualnej z listy maszyn wirtualnych.
1. W górnej części okienka zarządzania maszyny Wirtualnej, wybierz **ponownego uruchomienia**.  

    ![Uruchom ponownie przycisk maszyny Wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorowanie stanu ponownego uruchomienia, wybierając **powiadomienia** w górnym rogu okna.

    ![Wyświetlanie stanu ponownego uruchomienia maszyny Wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Można również uruchomić ponownie uruchomionej maszyny Wirtualnej, wybierając jego wielokropek (...) na liście **Moje maszyny wirtualne**.

![Ponowne uruchomienie maszyny Wirtualnej za pośrednictwem wielokropek](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Kolejne kroki
* Po ponownym uruchomieniu, można ponownie połączyć się z maszyną Wirtualną, wybierając **Connect** na jego okienko zarządzania.
* Eksploruj [galerię szablonów Szybki Start DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
