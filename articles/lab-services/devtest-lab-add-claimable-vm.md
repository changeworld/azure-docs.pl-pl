---
title: Tworzenie i zarządzanie nimi claimable maszyn wirtualnych w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać claimable maszyny wirtualnej do laboratorium w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 669dfab75f34a0d1f997dc34f600402d3c10669b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787493"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Tworzenie i zarządzanie nimi claimable maszyn wirtualnych w usłudze Azure DevTest Labs
Dodaj Maszynę wirtualną claimable laboratorium w podobny sposób jak możesz [dodać standardowe maszyny Wirtualnej](devtest-lab-add-vm.md) — z *podstawowej* czyli albo [niestandardowego obrazu](devtest-lab-create-template.md), [formuła](devtest-lab-manage-formulas.md) , lub [obrazu z witryny Marketplace](devtest-lab-configure-marketplace-images.md). Ten samouczek przeprowadzi Cię przez dodawanie claimable maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu portalu Azure i przedstawiono procesy, które użytkownik wykonuje oświadczeń i unclaim maszyny Wirtualnej.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać claimable maszyny Wirtualnej do laboratorium w usłudze Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz laboratorium, w którym chcesz utworzyć claimable maszyny Wirtualnej.  
1. W laboratorium **omówienie** okienku wybierz **+ Dodaj**.  

    ![Dodawanie przycisku maszyny Wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **wybierz podstawowej** obszarze, wybierz podstawowy dla maszyny Wirtualnej.
1. W **maszyny wirtualnej** okienku, wprowadź nazwę dla nowej maszyny wirtualnej w **nazwę maszyny wirtualnej** pola tekstowego.

    ![Okienko maszyny Wirtualnej laboratorium](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Wprowadź **nazwy użytkownika** udzieleniu uprawnień administratora na maszynie wirtualnej.  
1. Jeśli chcesz skorzystać z hasła przechowywane w Twojej [tajny magazynu](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), wybierz pozycję **używać hasła zapisane**i określ wartości klucza, który odpowiada klucz tajny (hasło). W przeciwnym razie wprowadź hasło w polu tekstowym etykietą **wpisz wartość**.
1. **Typu dysku maszyny wirtualnej** Określa, który typ dysku magazynu jest dozwolony dla maszyn wirtualnych w laboratorium.
1. Wybierz **rozmiar maszyny wirtualnej** i wybierz jedną z wstępnie zdefiniowane elementy, które Określ rdzeni procesora, rozmiar pamięci RAM i rozmiar dysku twardego maszyny wirtualnej do utworzenia.
1. Wybierz **artefakty** i na liście artefaktów, wybierz i skonfiguruj artefaktów, które chcesz dodać do obrazu podstawowego. Jeśli nowych użytkowników programu DevTest Labs lub konfigurowanie artefaktów, zapoznaj się [Dodaj istniejący artefakt do maszyny Wirtualnej](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sekcji, a następnie wróć tutaj po zakończeniu.
1. Wybierz **Zaawansowane ustawienia** Aby skonfigurować opcje wygaśnięcia i Opcje sieci maszyny Wirtualnej. W obszarze **oświadczeń opcje**, wybierz **tak** dokonanie claimable komputera.

  ![Wybierz claimable maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Jeśli chcesz wyświetlić lub skopiuj szablon usługi Azure Resource Manager, zapoznaj się [szablonu Zapisz Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) , a następnie wróć tutaj po zakończeniu.
1. Wybierz **Utwórz** dodać określoną maszynę Wirtualną do laboratorium.

   Wyświetlany jest stan tworzenia maszyny Wirtualnej, najpierw **tworzenie**, następnie jako **systemem** po uruchomieniu maszyny Wirtualnej.

> [!NOTE]
> W przypadku wdrożenia maszyn wirtualnych laboratorium za pośrednictwem [szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md), można utworzyć claimable maszyn wirtualnych, ustawiając **allowClaim** właściwości na wartość true w sekcji właściwości.
>
>

## <a name="using-a-claimable-vm"></a>Przy użyciu claimable maszyny Wirtualnej

Użytkownik może oświadczeń żadnej maszyny Wirtualnej z listy "Claimable maszyny wirtualne", wykonując jedną z następujących czynności:

* Z listy "Claimable maszyny wirtualne" u dołu okienka "Overview" laboratorium należy utworzyć, kliknij prawym przyciskiem myszy na jednym z maszyn wirtualnych na liście, a następnie wybierz pozycję **maszyny oświadczeń**.

 ![Żądanie claimable określonej maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* W górnej części okienka "Overview", wybierz **uzyskania**. Losowe maszyny wirtualnej jest przypisane z listy claimable maszyn wirtualnych.

 ![Żądanie żadnej claimable maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Po użytkownika oświadczeń maszyny Wirtualnej, przenieść w górę do listy "Moje maszyny wirtualne" i nie jest już claimable przez innego użytkownika.

## <a name="unclaim-a-vm"></a>Unclaim maszyny Wirtualnej

Gdy użytkownik jest już korzystać z oświadczeniem maszyny Wirtualnej i chce udostępnić innemu użytkownikowi, ich powrócić oświadczeniem maszyny Wirtualnej do listy claimable maszyn wirtualnych, wykonując jedną z następujących czynności:

- Z listy "Moje maszyny wirtualne", kliknij prawym przyciskiem myszy na jednym z maszyn wirtualnych na liście — lub zaznacz jego wielokropek (...) — i wybierz polecenie **Unclaim**.

  ![Unclaim Maszynę wirtualną na liście maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na liście "Moje maszyny wirtualne", wybierz maszyny Wirtualnej, aby otworzyć jego okienku zarządzanie, a następnie wybierz **Unclaim** na pasku menu u góry.

  ![Unclaim Maszynę wirtualną w okienku Zarządzanie maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Gdy użytkownik unclaims maszyny Wirtualnej, już nie ma uprawnień dla tego laboratorium określonej maszyny Wirtualnej.

### <a name="transferring-the-data-disk"></a>Transferowanie dysk z danymi
Jeśli claimable maszyna wirtualna ma dysku danych oraz użytkownika unclaims go, pozostaje na dysku danych z maszyny Wirtualnej. Gdy innego użytkownika następnie oświadczeń, że maszyna wirtualna, nowy użytkownik oświadczeń dysk z danymi, a także maszyny Wirtualnej.

Jest to nazywane "transferu dysku danych". Dysk z danymi staje się dostępna na liście nowego użytkownika z **dysków z danymi** zarządzanie.

![Unclaim dysków z danymi.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Kolejne kroki
* Po jego utworzeniu można Połącz się z maszyną Wirtualną, wybierając **Connect** w okienku jego zarządzania.
* Eksploruj [galerię szablonów Szybki Start DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
