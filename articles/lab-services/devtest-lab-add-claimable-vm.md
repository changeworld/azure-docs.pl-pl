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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: fdffa3862f45b99c2c3f2ed41934e09247808ca7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311834"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Tworzenie i zarządzanie nimi przejęcia maszyn wirtualnych w usłudze Azure DevTest Labs
Dodawanie przejęcia maszyny Wirtualnej do laboratorium w podobny sposób jak możesz [Dodaj maszyna wirtualna w warstwie standardowa](devtest-lab-add-vm.md) — od *podstawowy* oznacza to jedną [obrazu niestandardowego](devtest-lab-create-template.md), [formuły](devtest-lab-manage-formulas.md) , lub [obrazu z witryny Marketplace](devtest-lab-configure-marketplace-images.md). Ten samouczek przeprowadzi Cię przez dodawanie przejęcia maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu witryny Azure portal oraz przedstawiono procesy, które użytkownik wykona oświadczeń i wywołującemu maszyny Wirtualnej.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać przejęcia maszyny Wirtualnej do laboratorium Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** w **DEVOPS** sekcji. Jeśli wybierzesz * (w formie gwiazdek) obok pozycji **DevTest Labs** w **DEVOPS** sekcji. Ta akcja spowoduje dodanie **DevTest Labs** do menu nawigacji po lewej stronie, aby łatwo go dostępu podczas kolejnego. Następnie można wybrać **DevTest Labs** w menu nawigacji po lewej stronie.

    ![Wszystkie usługi — Wybieranie usługi DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Zaznacz na liście laboratoriów laboratorium, w którym chcesz utworzyć maszynę Wirtualną.
2. W laboratorium **Przegląd** wybierz opcję **+ Dodaj**.

    ![Dodawanie przycisku maszyny Wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na **wybierz podstawowej** wybierz obraz z witryny marketplace dla maszyny Wirtualnej.
1. Na **podstawowych ustawień** karcie **maszyny wirtualnej** wykonaj następujące czynności:
    1. Wprowadź nazwę maszyny Wirtualnej w **nazwę maszyny wirtualnej** pola tekstowego. Pole tekstowe jest wstępnie wypełnione o unikatowej nazwie wygenerowany automatycznie. Nazwa odnosi się do nazwy użytkownika w ramach swój adres e-mail, następuje unikatowy numer 3-cyfrowego. Ta funkcja zaoszczędzić czas myśleć o nazwę komputera, a następnie wpisz go za każdym razem, gdy tworzysz maszynę. To pole automatycznie wypełniony nazwą wybranego można zastąpić, jeśli chcesz. Aby zastąpić nazwę automatycznie wypełniony dla maszyny Wirtualnej, wprowadź nazwę w **nazwę maszyny wirtualnej** pola tekstowego.
    2. Wprowadź **nazwa_użytkownika** udzieleniu uprawnień administratora na maszynie wirtualnej. **Nazwa_użytkownika** maszyny jest wstępnie wypełniony o unikatowej nazwie wygenerowany automatycznie. Nazwa odnosi się do nazwy użytkownika w ramach swój adres e-mail. Ta funkcja oszczędza czas do podejmowania decyzji o nazwę użytkownika, za każdym razem, gdy tworzysz nową maszynę. To pole automatycznie wypełniony przy użyciu nazwy użytkownika w wybranym można zastąpić ponownie, jeśli chcesz. Aby zastąpić wartość automatycznie wypełniony dla nazwy użytkownika, wprowadź wartość w **nazwa_użytkownika** pola tekstowego. Ten użytkownik otrzymuje **administratora** uprawnień na maszynie wirtualnej.
    3. W przypadku tworzenia pierwszej maszyny Wirtualnej w środowisku laboratoryjnym, wprowadź **hasło** dla użytkownika. Aby zapisać to hasło jako domyślne hasło w magazynie kluczy Azure skojarzonego z laboratorium, zaznacz **Zapisz jako domyślne hasło**. Domyślne hasło jest zapisywane w usłudze key vault o nazwie: **VmPassword**. Podczas próby tworzenia kolejnych maszyn wirtualnych w środowisku laboratoryjnym **VmPassword** jest automatycznie wybierany do **hasło**. Aby zastąpić wartości, wyczyść **używać hasła zapisane** pole wyboru, a następnie wprowadź hasło.

        Można również najpierw zapisać wpisów tajnych w magazynie kluczy, a następnie użyć go podczas tworzenia maszyny Wirtualnej w środowisku laboratoryjnym. Aby uzyskać więcej informacji, zobacz [Store wpisów tajnych w magazynie kluczy](devtest-lab-store-secrets-in-key-vault.md). Aby użyć hasła przechowywane w usłudze key vault, wybierz **używać hasła zapisane**i określ wartości klucza, który odpowiada klucz tajny (hasło).
    4. W **więcej opcji** zaznacz **Zmień rozmiar**. Wybierz jeden z elementów wstępnie zdefiniowanych, które określają rdzeni procesora, pamięci RAM i rozmiar dysku twardego maszyny wirtualnej, aby utworzyć.
    5. Wybierz **Dodawanie lub usuwanie artefaktów**. Wybierz i skonfiguruj artefaktów, które chcesz dodać do obrazu podstawowego.
    **Uwaga:** Jeśli jesteś nowym użytkownikiem usługi DevTest Labs, lub konfigurowanie artefaktów, dotyczą [Dodaj istniejący artefakt do maszyny Wirtualnej](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sekcji, a następnie wróć tutaj po zakończeniu.
2. Przełącz się do **Zaawansowane ustawienia** u góry, a następnie wykonaj następujące czynności:
    1. Aby zmienić sieci wirtualnej, należącym do maszyny Wirtualnej, wybierz **zmienić sieci wirtualnej**.
    2. Aby zmienić podsieć, wybierz **zmienić podsieć**.
    3. Określ, czy adres IP maszyny Wirtualnej jest **publiczne, prywatne lub udostępnione**.
    4. Aby automatycznie usunąć maszynę Wirtualną, należy określić **datę i godzinę wygaśnięcia**.
    5. Aby maszyna wirtualna przejęcia przez użytkownika laboratorium, wybierz **tak** dla **Przydziel tej maszynie przejęcia** opcji.
    6. Określ liczbę **wystąpień maszyny Wirtualnej** chcesz udostępnić użytkownikom laboratorium.
3. Wybierz **Utwórz** dodać określoną maszynę Wirtualną do laboratorium.

   Na stronie laboratorium Wyświetla stan tworzenia maszyny Wirtualnej — najpierw jako **tworzenie**, następnie jako **systemem** po uruchomieniu maszyny Wirtualnej.

> [!NOTE]
> W przypadku wdrożenia laboratoryjnego maszyn wirtualnych na podstawie [szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md), przejęcia maszyn wirtualnych można utworzyć, ustawiając **allowClaim** właściwości na wartość true w sekcji właściwości.


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
* Zapoznaj się z [galerii szablonów szybkiego startu usługi DevTest Labs usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
