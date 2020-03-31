---
title: Tworzenie maszyn wirtualnych podlegania z roszczeniem i zarządzanie nimi w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać maszynę wirtualną z roszczeniem do laboratorium w laboratorium usługi Azure DevTest Labs
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
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270799"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Tworzenie maszyn wirtualnych z żądaniem i zarządzanie nimi w laboratoriach devtest azure
Maszyna wirtualna z roszczeniem jest dodawany do laboratorium w sposób podobny do sposobu [dodawania standardowej maszyny Wirtualnej](devtest-lab-add-vm.md) — z *bazy,* która jest [obrazem niestandardowym](devtest-lab-create-template.md), [formułą](devtest-lab-manage-formulas.md)lub [obrazem Marketplace.](devtest-lab-configure-marketplace-images.md) W tym samouczku przedstawiono użytkownika przy użyciu witryny Azure portal, aby dodać maszynę wirtualną z roszczeniem do laboratorium w DevTest Labs i pokazuje procesy, które użytkownik następuje do żądania i odliczyć maszynę wirtualną.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać maszynę wirtualną z roszczeniem do laboratorium w laboratorium usługi Azure DevTest Labs
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
3. Wybierz **utwórz,** aby dodać określoną maszynę wirtualną do laboratorium.

   Strona laboratorium wyświetla stan tworzenia maszyny Wirtualnej — najpierw jako **Tworzenie**, a następnie jako **uruchomiony** po uruchomieniu maszyny wirtualnej.

> [!NOTE]
> Jeśli wdrożysz maszyny wirtualne laboratorium za pomocą [szablonów usługi Azure Resource Manager,](devtest-lab-create-environment-from-arm.md)można utworzyć maszyny wirtualne z roszczeniem, ustawiając **właściwość allowClaim** na true w sekcji właściwości.


## <a name="using-a-claimable-vm"></a>Korzystanie z maszyny wirtualnej z roszczeniem

Użytkownik może ubiegać się o dowolną maszynę wirtualną z listy "Maszyn wirtualnych z żądaniem", wykonując jeden z następujących kroków:

* Z listy "Maszyny wirtualne zgodną z roszczeniem" u dołu okienka "Przegląd" laboratorium kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych na liście i wybierz pozycję **Claim machine**.

  ![Poproś o określoną maszynę wirtualną z roszczeniem.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* U góry okienka "Przegląd" wybierz pozycję **Zgłaś dowolny**plik . Losowa maszyna wirtualna jest przypisywana z listy maszyn wirtualnych, które można zgłaszać.

  ![Poproś o dowolną maszynę wirtualną, która może być zgłaszana do żądania.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Po żądaniu przez użytkownika maszyny wirtualnej, DevTest Labs uruchomi komputer i przeniesie go do listy użytkowników laboratorium "Moje maszyny wirtualne". Oznacza to, że użytkownik laboratorium będzie teraz miał uprawnienia właściciela na tym komputerze. Czas wymagany dla tego kroku może się różnić w zależności od czasu uruchomienia, a także innych akcji niestandardowych wykonywanych podczas zdarzenia oświadczenia. Po zgłośeniu, maszyna nie jest już dostępna w puli podlegania roszczeń.  

## <a name="unclaim-a-vm"></a>Odłączyć maszynę wirtualną

Po zakończeniu korzystania z deklarowanej maszyny Wirtualnej i chce udostępnić ją innej osobie, mogą zwrócić żądaną maszynę wirtualną do listy maszyn wirtualnych, których można odebrać, wykonując jeden z następujących kroków:

- Z listy "Moje maszyny wirtualne" kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych na liście – lub wybierz jej wielokropek (...) – i wybierz **opcję Odwołuj.**

  ![Odwąż maszynę wirtualną na liście maszyn wirtualnych.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na liście "Moje maszyny wirtualne" wybierz maszynę wirtualną, aby otworzyć okienko zarządzania, a następnie wybierz **pozycję Odwdzięcz** się z górnego paska menu.

  ![Odwłącz maszynę wirtualną w okienku zarządzania maszyną wirtualną.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Gdy użytkownik unclaims maszyny Wirtualnej, nie mają już uprawnień właściciela dla tego określonego laboratorium maszyny Wirtualnej i jest dostępny do żądania przez innego użytkownika laboratorium w stanie, że został ponownie do puli. 

### <a name="transferring-the-data-disk"></a>Przesyłanie dysku danych
Jeśli maszyna wirtualna z roszczeniem ma dołączony dysk danych, a użytkownik ją odwurze, dysk danych pozostaje przy maszynie wirtualnej. Gdy inny użytkownik następnie twierdzi, że maszyna wirtualna, że nowy użytkownik twierdzi, dysk danych, jak również maszyny Wirtualnej.

Jest to nazywane "przesyłaniem dysku danych". Dysk danych staje się następnie dostępny na liście nowych użytkowników **moich dysków danych,** którymi może zarządzać.

![Odłącz dyski danych.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Następne kroki
* Po utworzeniu można połączyć się z maszyną wirtualną, wybierając **pozycję Połącz** w okienku zarządzania.
* Zapoznaj się z [galerią szablonów szybki start usługi DevTest Labs w usłudze Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
