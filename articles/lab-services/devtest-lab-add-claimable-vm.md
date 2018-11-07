---
title: Tworzenie i zarządzanie nimi przejęcia maszyn wirtualnych w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać przejęcia maszyny wirtualnej do laboratorium Azure DevTest Labs
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
ms.openlocfilehash: 3bfb674fa66f0701a099d237f4e760453c7b6a6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232131"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Tworzenie i zarządzanie nimi przejęcia maszyn wirtualnych w usłudze Azure DevTest Labs
Dodawanie przejęcia maszyny Wirtualnej do laboratorium w podobny sposób jak możesz [Dodaj maszyna wirtualna w warstwie standardowa](devtest-lab-add-vm.md) — od *podstawowy* oznacza to jedną [obrazu niestandardowego](devtest-lab-create-template.md), [formuły](devtest-lab-manage-formulas.md) , lub [obrazu z witryny Marketplace](devtest-lab-configure-marketplace-images.md). Ten samouczek przeprowadzi Cię przez dodawanie przejęcia maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu witryny Azure portal oraz przedstawiono procesy, które użytkownik wykona oświadczeń i wywołującemu maszyny Wirtualnej.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać przejęcia maszyny Wirtualnej do laboratorium Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Zaznacz na liście laboratoriów laboratorium, w którym chcesz utworzyć przejęcia maszyn wirtualnych.  
1. W laboratorium **Przegląd** okienku wybierz **+ Dodaj**.  

    ![Dodawanie przycisku maszyny Wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **wybierz podstawowej** obszaru, wybierz podstawowy dla maszyny Wirtualnej.
1. W **maszyny wirtualnej** okienku, wprowadź nazwę dla nowej maszyny wirtualnej w **nazwę maszyny wirtualnej** pola tekstowego.

    ![Okienko maszyn wirtualnych laboratorium](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Wprowadź **nazwa_użytkownika** udzieleniu uprawnień administratora na maszynie wirtualnej.  
1. Jeśli chcesz używać hasła przechowywane w swojej [magazynu wpisów tajnych](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), wybierz opcję **używać hasła zapisane**i określ wartości klucza, który odpowiada klucz tajny (hasło). W przeciwnym razie, wprowadź hasło w polu tekstowym etykietą **wpisz wartość**.
1. **Typ dysku maszyny wirtualnej** Określa typy dysku magazynu jest dozwolone dla maszyn wirtualnych w środowisku laboratoryjnym.
1. Wybierz **rozmiar maszyny wirtualnej** i wybierz jeden z wstępnie zdefiniowanych elementów, które określają rdzeni procesora, pamięci RAM i rozmiar dysku twardego maszyny wirtualnej, aby utworzyć.
1. Wybierz **artefaktów** i z listy artefaktów, wybierz i skonfiguruj artefaktów, które chcesz dodać do obrazu podstawowego. Jeśli jesteś nowym użytkownikiem usługi DevTest Labs, lub konfigurowanie artefaktów, dotyczą [Dodaj istniejący artefakt do maszyny Wirtualnej](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sekcji, a następnie wróć tutaj po zakończeniu.
1. Wybierz **Zaawansowane ustawienia** skonfigurować opcje wygaśnięcia i Opcje sieci maszyny Wirtualnej. W obszarze **oświadczenia opcje**, wybierz **tak** do Przydziel tej maszynie przejęcia.

  ![Wybierz przejęcia maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Aby wyświetlić lub skopiuj szablon usługi Azure Resource Manager, zapoznaj się [szablonu Zapisz Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) sekcji, a następnie wróć tutaj po zakończeniu.
1. Wybierz **Utwórz** dodać określoną maszynę Wirtualną do laboratorium.

   Stan tworzenia maszyny Wirtualnej jest wyświetlany, najpierw **tworzenie**, następnie jako **systemem** po uruchomieniu maszyny Wirtualnej.

> [!NOTE]
> W przypadku wdrożenia laboratoryjnego maszyn wirtualnych na podstawie [szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md), przejęcia maszyn wirtualnych można utworzyć, ustawiając **allowClaim** właściwości na wartość true w sekcji właściwości.
>
>

## <a name="using-a-claimable-vm"></a>Za pomocą przejęcia maszyny Wirtualnej

Użytkownik może przyjąć dowolnej maszyny Wirtualnej z listy "Przejęcia maszyny wirtualne", wykonując jedną z następujących czynności:

* Z listy "Przejęcia maszyny wirtualne" w dolnej części okienka "Przegląd" laboratorium, kliknij prawym przyciskiem myszy na jednej z maszyn wirtualnych, na liście, a następnie wybierz **maszyny oświadczenia**.

 ![Żądanie przejęcia określonej maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* W górnej części okienka "Przegląd" Wybierz **dowolny oświadczeń**. Losowe maszyny wirtualne są przypisane z listy przejęcia maszyn wirtualnych.

 ![Żądanie dowolnej przejęcia maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Po użytkownik oświadczeń maszyny Wirtualnej, są przenoszone w górę do listy "Moje maszyny wirtualne" i nie jest już przejęcia przez innych użytkowników.

## <a name="unclaim-a-vm"></a>Wywołującemu maszyny Wirtualnej

Po użytkownik jest już korzystać z oświadczeniem maszyny Wirtualnej i chce udostępnić innemu użytkownikowi, funkcje te mogą zwracać oświadczeniem maszyny Wirtualnej do listy przejęcia maszyn wirtualnych, wykonując jedną z następujących czynności:

- Z listy "Moje maszyny wirtualne", kliknij prawym przyciskiem myszy na jednej z maszyn wirtualnych, na liście — lub wybierz jego przycisk wielokropka (...) — i wybierz **Unclaim**.

  ![Wywołującemu Maszynę wirtualną na liście maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na liście "Moje maszyny wirtualne" wybierz maszynę Wirtualną w celu otwarcia jej okienku zarządzanie, a następnie wybierz **Unclaim** na pasku menu u góry.

  ![Wywołującemu Maszynę wirtualną w okienku zarządzania maszyny Wirtualnej.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Po użytkownik unclaims Maszynę wirtualną, nie mają uprawnień dla tego laboratorium określonej maszyny Wirtualnej.

### <a name="transferring-the-data-disk"></a>Przeniesienie dysku danych
Jeśli przejęcia maszyna wirtualna ma dysk danych dołączony do niej użytkownika unclaims go, dysk danych pozostaje z maszyną Wirtualną. Kiedy inny użytkownik następnie oświadczeń, maszyny Wirtualnej, ten nowy użytkownik twierdzi, dysk z danymi, a także maszyny Wirtualnej.

Jest to nazywane "transferu dysku danych". Dysk danych staje się dostępna na liście nowego użytkownika **dysków z danymi** ich do zarządzania.

![Usuwa zgłoszenie dysków z danymi.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Kolejne kroki
* Po jego utworzeniu można nawiązaniu połączenia z maszyną wirtualną, wybierając **Connect** w jej okienku zarządzania.
* Zapoznaj się z [galerii szablonów szybkiego startu usługi DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
