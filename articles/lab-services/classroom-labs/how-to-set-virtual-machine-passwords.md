---
title: Ustawianie haseł dla maszyn wirtualnych w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak ustawić i zresetować hasła do maszyn wirtualnych (VM) w laboratoriach szkolnych usługi Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933819"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Konfigurowanie puli maszyn wirtualnych i zarządzanie nią 
W tym artykule pokazano, jak wykonać następujące zadania:

- Zwiększenie liczby maszyn wirtualnych w laboratorium
- Uruchamianie wszystkich maszyn wirtualnych lub wybranych maszyn wirtualnych 
- Resetowanie maszyn wirtualnych

## <a name="update-the-lab-capacity"></a>Aktualizowanie pojemności laboratorium
Aby zwiększyć lub zmniejszyć pojemność laboratorium (liczba maszyn wirtualnych w laboratorium), wykonaj następujące czynności:

1. Na stronie **Pula maszyny wirtualnej** wybierz pozycję ** &lt;Pojemność laboratorium:&gt; liczba maszyn**.
2. Wprowadź nową **liczbę maszyn wirtualnych,** które chcesz w laboratorium. Liczba ta musi być większa lub równa liczbie użytkowników zarejestrowanych w laboratorium. 
3. Następnie wybierz pozycję **Zapisz**. 

    ![Przycisk Rozpocznij wszystko](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Jeśli zwiększysz pojemność, można zobaczyć maszyny wirtualne lub maszyny wirtualne tworzone. Jeśli nie widzisz nowej maszyny Wirtualnej na liście, odśwież stronę. 

    ![Tworzona maszyna wirtualna](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Uruchamianie maszyn wirtualnych

### <a name="start-ot-stop-all-vms"></a>Start ot stop wszystkie maszyny wirtualne
1. Przełącz się do strony **puli maszyny wirtualnej.** 
2. Wybierz **pozycję Rozpocznij wszystko** z paska narzędzi. 

    ![Przycisk Rozpocznij wszystko](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Po uruchomieniu wszystkich maszyn wirtualnych można zatrzymać wszystkie maszyny wirtualne, wybierając przycisk **Zatrzymaj wszystko** na pasku narzędzi. 

    ![Przycisk Zatrzymaj wszystko](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Uruchamianie wybranych maszyn wirtualnych
Istnieją dwa sposoby uruchamiania wybranych maszyn wirtualnych (jeden lub więcej). Pierwszym sposobem jest wybranie maszyny wirtualnej lub maszyn wirtualnych na liście, a następnie **wybranie przycisku Start** na pasku narzędzi. 

Drugim sposobem jest wybranie jednej lub więcej maszyn wirtualnych na liście i przełącz przycisk w kolumnie **Stan.** 

![Uruchamianie wybranych maszyn wirtualnych](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Podobnie można zatrzymać jedną lub więcej maszyn wirtualnych, przełączanie przycisk w **kolumnie Stan** lub wybierając **Zatrzymaj** na pasku narzędzi. 

> [!NOTE]
> Gdy nauczyciel włącza maszynę wirtualną dla uczniów, nie ma to wpływu na przydział dla ucznia. Przydział dla użytkownika określa liczbę godzin pracy dostępnych dla użytkownika poza zaplanowanym czasem zajęć. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Resetowanie maszyn wirtualnych
Aby zresetować jedną lub więcej maszyn wirtualnych, zaznacz je na liście, a następnie wybierz **pozycję Resetuj** na pasku narzędzi. 

![Resetowanie wybranych maszyn wirtualnych](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

W oknie dialogowym **Resetowanie maszyn wirtualnych** wybierz pozycję **Resetuj**. 

![Okno dialogowe Resetowanie maszyny Wirtualnej](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Ustawianie hasła dla maszyn wirtualnych
Właściciel laboratorium (nauczyciel) można ustawić/zresetować hasło dla maszyn wirtualnych w momencie tworzenia laboratorium (kreator tworzenia laboratorium) lub po utworzeniu laboratorium na stronie **szablon.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ustawianie hasła w momencie tworzenia laboratorium
Właściciel laboratorium (nauczyciel) można ustawić hasło dla maszyn wirtualnych w laboratorium na stronie **poświadczenia maszyny wirtualnej** kreatora tworzenia laboratorium.

![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Po włączeniu/wyłączeniu **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** opcji na tej stronie, nauczyciel może wybrać użycie tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawienie haseł dla ich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemu operacyjnego Windows i Linux z wyjątkiem Ubuntu. Gdy to ustawienie jest wyłączone, uczniowie zostaną poproszeni o ustawienie hasła podczas próby połączenia się z maszyną wirtualną po raz pierwszy. 

### <a name="reset-password-later"></a>Późniejsze zresetowanie hasła

1. Na stronie **Szablon** laboratorium wybierz pozycję **Resetuj hasło** na pasku narzędzi. 
1. W oknie dialogowym **Resetowanie hasła** wprowadź hasło i wybierz pozycję **Resetuj hasło**.
    
    ![Okno dialogowe Ustawianie hasła](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Łączenie się z maszynami wirtualnymi dla uczniów
Twórca laboratorium (instruktor/profesor) może połączyć się z maszyną wirtualną dla ucznia, jeśli spełnione są następujące warunki: 

- **Opcja Użyj tego samego hasła dla wszystkich maszyn wirtualnych** została wybrana podczas tworzenia laboratorium
- Maszyna wirtualna jest uruchomiona 

 Aby połączyć się z maszyną wirtualną dla uczniów, umieść wskaźnik myszy na maszynie Wirtualnej na liście i wybierz przycisk komputera.  

![Przycisk Połącz się z maszyną wirtualną dla uczniów](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Gdy profesor uruchamia maszynę wirtualną i łączy się z nią, przydział studenta pozostaje bez zmian. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Eksportowanie listy maszyn wirtualnych do pliku CSV

1. Przełącz się do karty **Puli maszyny wirtualnej.**
2. Wybierz **...** (wielokropek) na pasku narzędzi, a następnie wybierz **pozycję Eksportuj plik CSV**. 

    ![Lista eksportowania maszyn wirtualnych](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o innych opcjach użytkowania uczniów, które możesz skonfigurować (jako właściciel laboratorium), zobacz następujący artykuł: [Konfigurowanie użycia uczniów](how-to-configure-student-usage.md).

Aby dowiedzieć się, jak uczniowie mogą resetować hasła do swoich maszyn wirtualnych, zobacz [Ustawianie lub resetowanie hasła maszyn wirtualnych w laboratoriach szkolnych (uczniowie).](how-to-set-virtual-machine-passwords-student.md)