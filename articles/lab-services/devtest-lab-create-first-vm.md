---
title: Tworzenie pierwszej maszyny Wirtualnej w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć pierwszą maszynę wirtualną w laboratorium Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c72e10991e27f7d616703e635ee6e1a18122afc5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078079"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Tworzenie pierwszej maszyny Wirtualnej w laboratorium Azure DevTest Labs

Przy pierwszym dostęp do usługi DevTest Labs i chcesz utworzyć pierwszą maszynę Wirtualną, użytkownik będzie prawdopodobnie to zrobić przy użyciu wstępnie załadowane [obrazu z witryny marketplace podstawowy](devtest-lab-configure-marketplace-images.md). Później będzie można również wybrać z [niestandardowego obrazu i formuły](devtest-lab-add-vm.md) podczas tworzenia więcej maszyn wirtualnych. 

Ten samouczek przeprowadzi Cię przez dodawanie pierwszej maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu witryny Azure portal.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać pierwszej maszyny Wirtualnej do laboratorium Azure DevTest Labs
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

## <a name="next-steps"></a>Kolejne kroki
* Po utworzeniu maszyny Wirtualnej można nawiązać maszyny Wirtualnej, wybierając **Connect** na stronie maszyny Wirtualnej.
* Zapoznaj się z [Dodawanie maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md) uzyskać pełniejsze informacje dotyczące dodawania kolejnych maszyn wirtualnych w środowisku laboratoryjnym.
* Zapoznaj się z [galerii szablonów DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
