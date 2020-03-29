---
title: Ponowne uruchamianie maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera kroki, aby szybko i łatwo ponownie uruchomić maszyny wirtualne (VM) w laboratorium usługi Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759998"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ponowne uruchamianie maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
Można szybko i łatwo ponownie uruchomić maszynę wirtualną w DevTest Labs, wykonując kroki opisane w tym artykule. Przed ponownym uruchomieniem maszyny wirtualnej należy wziąć pod uwagę następujące kwestie:

- Maszyna wirtualna musi być uruchomiona, aby funkcja ponownego uruchomienia była włączona.
- Jeśli użytkownik jest połączony z uruchomionej maszyny Wirtualnej podczas wykonywania ponownego uruchomienia, muszą ponownie połączyć się z maszyną wirtualną po uruchomieniu kopii zapasowej.
- Jeśli artefakt jest stosowany po ponownym uruchomieniu maszyny Wirtualnej, pojawi się ostrzeżenie, że artefakt może nie zostać zastosowany.

    ![Ostrzeżenie podczas ponownego uruchamiania podczas stosowania artefaktów](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Jeśli maszyna wirtualna utknęła w martwym punkcie podczas stosowania artefaktu, można użyć funkcji ponownego uruchomienia maszyny Wirtualnej jako potencjalnego sposobu rozwiązania problemu.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Kroki ponownego uruchamiania maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz laboratorium, które zawiera maszynę wirtualną, którą chcesz ponownie uruchomić.
1. W lewym panelu wybierz pozycję **Moje maszyny wirtualne**.
1. Z listy maszyn wirtualnych wybierz uruchomiona maszynę wirtualną.
1. U góry okienka zarządzania maszyną wirtualną wybierz pozycję **Uruchom ponownie**.

    ![Przycisk Uruchom ponownie maszynę wirtualną](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitoruj stan ponownego uruchomienia, wybierając ikonę **Powiadomienia** w prawym górnym rogu okna.

    ![Wyświetlanie stanu ponownego uruchamiania maszyny Wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Można również ponownie uruchomić działającą maszynę wirtualną, wybierając jej wielokropek (...) na liście **Moje maszyny wirtualne**.

![Ponowne uruchamianie maszyny wirtualnej za pomocą elips](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Następne kroki
* Po ponownym uruchomieniu można ponownie połączyć się z maszyną wirtualną, wybierając **pozycję Połącz** w okienku zarządzania.
* Zapoznaj się z [galerią szablonów szybki start usługi DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
