---
title: Dodawanie maszyny wirtualnej do laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać maszynę wirtualną do laboratorium w laboratorium usługi Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284254"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Dodawanie maszyny wirtualnej do laboratorium w laboratorium usługi Azure DevTest Labs
Jeśli [utworzono już pierwszą maszynę wirtualną,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)prawdopodobnie zrobiłeś to z fabrycznie załadowanego [obrazu z rynku.](devtest-lab-configure-marketplace-images.md) Teraz, jeśli chcesz dodać kolejne maszyny wirtualne do laboratorium, możesz również wybrać *bazę,* która jest [obrazem niestandardowym](devtest-lab-create-template.md) lub [formułą](devtest-lab-manage-formulas.md). W tym samouczku można przejść przez za pomocą witryny Azure portal, aby dodać maszynę wirtualną do laboratorium w DevTest Labs.

W tym artykule pokazano również, jak zarządzać artefaktami dla maszyny Wirtualnej w laboratorium.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać maszynę wirtualną do laboratorium w laboratorium usługi Azure DevTest Labs
1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz **devtest labs** w sekcji **DEVOPS.** Jeśli wybierzesz * (gwiazdka) obok **DevTest Labs** w sekcji **DEVOPS.** Ta akcja dodaje **DevTest Labs** do lewego menu nawigacyjnego, dzięki czemu można uzyskać do niego łatwy dostęp następnym razem. Następnie można wybrać **DevTest Labs** w lewym menu nawigacyjnym.

    ![Wszystkie usługi - wybierz DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz utworzyć maszynę wirtualną.
2. Na stronie **Przegląd** laboratorium wybierz pozycję **+ Dodaj**.

    ![Przycisk Dodaj maszynę wirtualną](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na stronie **Wybierz stronę bazową** wybierz obraz portalu Marketplace dla maszyny Wirtualnej.
1. Na karcie **Ustawienia podstawowe** na stronie **Maszyny wirtualnej** wykonaj następujące czynności:
    1. Wprowadź nazwę maszyny Wirtualnej w polu **tekstowym Nazwa maszyny wirtualnej.** Pole tekstowe jest wstępnie wypełnione unikatową automatycznie generowaną nazwą. Nazwa odpowiada nazwie użytkownika w twoim adresie e-mail, po której następuje unikatowy 3-cyfrowy numer. Ta funkcja pozwala zaoszczędzić czas na zastanowienie się nad nazwą maszyny i wpisanie jej przy każdym utworzeniu maszyny. W razie potrzeby można zastąpić to pole wypełnione automatycznie wybraną nazwą. Aby zastąpić automatycznie wypełnioną nazwę maszyny Wirtualnej, wprowadź nazwę w polu **tekstowym Nazwa maszyny wirtualnej.**
    2. Wprowadź **nazwę użytkownika,** która ma przyznane uprawnienia administratora na maszynie wirtualnej. **Nazwa użytkownika** urządzenia jest wstępnie wypełniona unikatową automatycznie generowaną nazwą. Nazwa odpowiada nazwie użytkownika w twoim adresie e-mail. Ta funkcja pozwala zaoszczędzić czas na podjęcie decyzji o nazwie użytkownika przy każdym utworzeniu nowego komputera. Ponownie, możesz zastąpić to automatycznie wypełnione pole wybraną nazwą użytkownika. Aby zastąpić automatycznie wypełnioną wartość nazwy użytkownika, wprowadź wartość w polu tekstowym **Nazwa użytkownika.** Ten użytkownik ma uprawnienia **administratora** na maszynie wirtualnej.
    3. Jeśli tworzysz pierwszą maszynę wirtualną w laboratorium, wprowadź **hasło** dla użytkownika. Aby zapisać to hasło jako hasło domyślne w magazynie kluczy platformy Azure skojarzonym z laboratorium, wybierz pozycję **Zapisz jako hasło domyślne**. Domyślne hasło jest zapisywane w magazynie kluczy o nazwie: **VmPassword**. Podczas próby utworzenia kolejnych maszyn wirtualnych w laboratorium, **VmPassword** jest automatycznie wybierany dla **hasła**. Aby zastąpić wartość, wyczyść pole wyboru **Użyj zapisanego klucza tajnego** i wprowadź hasło.

        ![Wybieranie bazy](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Można również zapisać wpisy tajne w magazynie kluczy, a następnie użyć go podczas tworzenia maszyny Wirtualnej w laboratorium. Aby uzyskać więcej informacji, zobacz [Przechowywanie wpisów tajnych w magazynie kluczy](devtest-lab-store-secrets-in-key-vault.md). Aby użyć hasła przechowywanego w magazynie kluczy, wybierz pozycję **Użyj zapisanego klucza tajnego**i określ wartość klucza odpowiadającą kluczowi tajnemu (haśle).
    4. W sekcji **Więcej opcji** wybierz pozycję **Zmień rozmiar**. Wybierz jeden ze wstępnie zdefiniowanych elementów określających rdzenie procesora, rozmiar pamięci RAM i rozmiar dysku twardego maszyny Wirtualnej do utworzenia.
    5. Wybierz **pozycję Dodaj lub usuń artefakty**. Zaznacz i skonfiguruj artefakty, które chcesz dodać do obrazu podstawowego.
    **Uwaga:** Jeśli jesteś nowy w DevTest Labs lub konfigurowanie artefaktów, odnoszą się do [Dodaj istniejący artefakt do](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) maszyny Wirtualnej sekcji, a następnie powrócić tutaj po zakończeniu.
2. Przełącz się na kartę **Ustawienia zaawansowane** u góry i wykonaj następujące czynności:
    1. Aby zmienić sieć wirtualną, w których znajduje się maszyna **wirtualna,** wybierz pozycję Zmień sieć wirtualną .
    2. Aby zmienić podsieć, wybierz pozycję **Zmień podsieć**.
    3. Określ, czy adres IP maszyny Wirtualnej jest **publiczny, prywatny czy udostępniony.**
    4. Aby automatycznie usunąć maszynę wirtualną, określ **datę i godzinę wygaśnięcia**.
    5. Aby maszyna wirtualna zgłaszała roszczenie przez użytkownika laboratorium, wybierz opcję **Tak** dla **opcji Niech to urządzenie będzie podlegać żądalności.**
    6. Określ liczbę **wystąpień maszyny Wirtualnej,** które mają być dostępne dla użytkowników laboratorium.

        ![Wybieranie bazy](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Wybierz **utwórz,** aby dodać określoną maszynę wirtualną do laboratorium.

   Strona laboratorium wyświetla stan tworzenia maszyny Wirtualnej — najpierw jako **Tworzenie**, a następnie jako **uruchomiony** po uruchomieniu maszyny wirtualnej.

    ![Stan tworzenia maszyny wirtualnej](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Dodawanie istniejącego artefaktu do maszyny Wirtualnej
Podczas tworzenia maszyny Wirtualnej można dodać istniejące artefakty. Każde laboratorium zawiera artefakty z repozytorium artefaktów Public DevTest Labs, a także artefakty, które zostały utworzone i dodane do własnego repozytorium artefaktów.

* Artefakty programu Azure DevTest *Labs* umożliwiają określenie *akcji* wykonywanych podczas inicjowania obsługi administracyjnej maszyny Wirtualnej, takich jak uruchamianie skryptów programu Windows PowerShell, uruchamianie poleceń Bash i instalowanie oprogramowania.
* *Parametry* artefaktu umożliwiają dostosowanie artefaktu do konkretnego scenariusza

Aby dowiedzieć się, jak tworzyć artefakty, zobacz artykuł, [Dowiedz się, jak tworzyć własne artefakty do użytku z DevTest Labs](devtest-lab-artifact-author.md).

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz laboratorium zawierające maszynę wirtualną, z którą chcesz pracować.
1. Wybierz **pozycję Moje maszyny wirtualne**.
1. Wybierz żądaną maszynę wirtualną.
1. Wybierz pozycję **Zarządzaj artefaktami**.
1. Wybierz **opcję Zastosuj artefakty**.
1. W okienku **Zastosuj artefakty** wybierz artefakt, który chcesz dodać do maszyny Wirtualnej.
1. W okienku **Dodaj artefakt** wprowadź wymagane wartości parametrów i wszystkie potrzebne parametry opcjonalne.
1. Wybierz **dodaj,** aby dodać artefakt i powrócić do okienka **Zastosuj artefakty.**
1. Kontynuuj dodawanie artefaktów zgodnie z potrzebami maszyny Wirtualnej.
1. Po dodaniu artefaktów można [zmienić kolejność uruchamiania artefaktów.](#change-the-order-in-which-artifacts-are-run) Można również wrócić do [wyświetlania lub modyfikowania artefaktu](#view-or-modify-an-artifact).
1. Po zakończeniu dodawania artefaktów wybierz pozycję **Zastosuj**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Zmienianie kolejności uruchamiania artefaktów
Domyślnie akcje artefaktów są wykonywane w kolejności, w jakiej są dodawane do maszyny Wirtualnej.
Poniższe kroki ilustrują, jak zmienić kolejność uruchamiania artefaktów.

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.

    ![Liczba artefaktów dodanych do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **Wybrane artefakty** przeciągnij i upuść artefakty do żądanej kolejności. **Uwaga:** Jeśli masz problemy z przeciągnięciem artefaktu, upewnij się, że przeciągasz od lewej strony artefaktu.
1. Po zakończeniu wybierz przycisk **OK**.

## <a name="view-or-modify-an-artifact"></a>Wyświetlanie lub modyfikowanie artefaktu
Poniższe kroki ilustrują sposób wyświetlania lub modyfikowania parametrów artefaktu:

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.

    ![Liczba artefaktów dodanych do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **Wybrane artefakty** zaznacz artefakt, który chcesz wyświetlić lub edytować.
1. W okienku **Dodawanie artefaktów** wcielij wszelkie potrzebne zmiany i wybierz przycisk **OK,** aby zamknąć okienko **Dodaj artefakt.**
1. Wybierz **przycisk OK,** aby zamknąć okienko **Wybrane artefakty.**

## <a name="save-azure-resource-manager-template"></a>Zapisz szablon usługi Azure Resource Manager
Szablon usługi Azure Resource Manager zapewnia deklaratywny sposób definiowania powtarzalnej wdrożenie.
W poniższych krokach wyjaśniono, jak zapisać szablon usługi Azure Resource Manager dla tworzonej maszyny wirtualnej.
Po zapisaniu można użyć szablonu usługi Azure Resource Manager do [wdrażania nowych maszyn wirtualnych za pomocą programu Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. W okienku **Maszyna wirtualna** wybierz pozycję **Wyświetl szablon usługi Azure Resource Manager**.
2. W okienku **szablonu Wyświetlanie usługi Azure Resource Manager** zaznacz tekst szablonu.
3. Skopiuj zaznaczony tekst do schowka.
4. Wybierz **przycisk OK,** aby zamknąć **okienko Szablonu programu View Azure Resource Manager**.
5. Otwórz edytor tekstów.
6. Wklej tekst szablonu ze schowka.
7. Zapisz plik do późniejszego użycia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Po utworzeniu maszyny Wirtualnej można połączyć się z maszyną wirtualną, wybierając **pozycję Połącz** w okienku maszyny Wirtualnej.
* Dowiedz się, jak [tworzyć niestandardowe artefakty dla maszyny Wirtualnej DevTest Labs](devtest-lab-artifact-author.md).
* Zapoznaj się z [galerią szablonów Szybki start programu Quick Start programu DevTest Labs w usłudze Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
