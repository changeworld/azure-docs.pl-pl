---
title: Ustawianie haseł dla maszyn wirtualnych w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak ustawiać i resetować hasła dla maszyn wirtualnych w laboratorium z klasą Azure Lab Services.
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
ms.openlocfilehash: c01106bf7288e777e601c06558b1408960f4e49b
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539437"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Konfigurowanie puli maszyn wirtualnych i zarządzanie nią 
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Zwiększ liczbę maszyn wirtualnych w laboratorium
- Uruchom wszystkie maszyny wirtualne lub wybrane maszyny wirtualne 
- Resetowanie maszyn wirtualnych

## <a name="update-the-lab-capacity"></a>Aktualizowanie pojemności laboratorium
Aby zwiększyć lub zmniejszyć wydajność laboratorium (liczbę maszyn wirtualnych w laboratorium), wykonaj następujące czynności:

1. Na stronie **Pula maszyn wirtualnych** wybierz pozycję **pojemność laboratorium: &lt;numer&gt; maszyny**.
2. Wprowadź nową **liczbę maszyn wirtualnych** , które mają być w laboratorium. Ta wartość musi być większa lub równa liczbie użytkowników zarejestrowanych w laboratorium. 
3. Następnie wybierz pozycję **Zapisz**. 

    ![Przycisk Uruchom wszystko](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. W przypadku zwiększenia pojemności można zobaczyć utworzoną maszynę wirtualną lub maszyny wirtualne. Jeśli nowa maszyna wirtualna nie jest widoczna na liście, Odśwież stronę. 

    ![Maszyna wirtualna jest tworzona](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Uruchom maszyny wirtualne

### <a name="start-ot-stop-all-vms"></a>Rozpocznij zatrzymywanie wszystkich maszyn wirtualnych
1. Przejdź do strony **puli maszyn wirtualnych** . 
2. Wybierz pozycję **Rozpocznij wszystko** na pasku narzędzi. 

    ![Przycisk Uruchom wszystko](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Po rozpoczęciu wszystkich maszyn wirtualnych możesz zatrzymać wszystkie maszyny wirtualne, wybierając przycisk **Zatrzymaj wszystko** na pasku narzędzi. 

    ![Przycisk Zatrzymaj wszystko](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Uruchom wybrane maszyny wirtualne
Istnieją dwa sposoby uruchamiania wybranych maszyn wirtualnych (jeden lub więcej). Pierwszym sposobem jest wybranie maszyny wirtualnej lub maszyn wirtualnych z listy, a następnie na pasku narzędzi wybierz pozycję **Rozpocznij** . 

Drugi sposób polega na wybraniu co najmniej jednej maszyny wirtualnej na liście i przełączeniu przycisku w kolumnie **stan** . 

![Uruchom wybrane maszyny wirtualne](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Podobnie można zatrzymać co najmniej jedną maszynę wirtualną, przełączając przycisk w kolumnie **stan** lub wybierając pozycję **Zatrzymaj** na pasku narzędzi. 

## <a name="reset-vms"></a>Resetowanie maszyn wirtualnych
Aby zresetować co najmniej jedną maszynę wirtualną, wybierz ją z listy, a następnie na pasku narzędzi wybierz pozycję **Zresetuj** . 

![Resetuj wybrane maszyny wirtualne](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

W oknie dialogowym **resetowanie maszyn wirtualnych** wybierz pozycję **Zresetuj**. 

![Okno dialogowe resetowania maszyny wirtualnej](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Ustawianie hasła dla maszyn wirtualnych
Właściciel laboratorium (nauczyciel) może ustawić/zresetować hasło dla maszyn wirtualnych w momencie tworzenia laboratorium (Kreator tworzenia laboratorium) lub po utworzeniu laboratorium na stronie **szablonu** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ustaw hasło podczas tworzenia laboratorium
Właściciel laboratorium (nauczyciel) może ustawić hasło dla maszyn wirtualnych w laboratorium na stronie **poświadczenia maszyny wirtualnej** w Kreatorze tworzenia laboratorium.

![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Przez włączenie/wyłączenie opcji **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** na tej stronie nauczyciel może wybrać użycie tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemu operacyjnego Windows i Linux z wyjątkiem Ubuntu. Gdy to ustawienie jest wyłączone, uczniowie będą monitowani o ustawienie hasła podczas próby nawiązania połączenia z maszyną wirtualną po raz pierwszy. 

### <a name="reset-password-later"></a>Zresetuj hasło później

1. Na stronie **szablon** laboratorium wybierz pozycję **zresetuj hasło** na pasku narzędzi. 
1. W oknie dialogowym **Resetowanie hasła** wprowadź hasło, a następnie wybierz pozycję **Resetuj hasło**.
    
    ![Okno dialogowe Ustawianie hasła](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Nawiązywanie połączenia z maszynami wirtualnymi uczniów
Twórca laboratorium (instruktor/profesor) może nawiązać połączenie z maszyną wirtualną ucznia w przypadku spełnienia następujących warunków: 

- Podczas tworzenia laboratorium wybrano opcję **Użyj tego samego hasła dla wszystkich maszyn wirtualnych**
- Maszyna wirtualna jest uruchomiona 

 Aby nawiązać połączenie z maszyną wirtualną ucznia, umieść wskaźnik myszy na maszynie wirtualnej na liście i wybierz przycisk komputer.  

![Przycisk połączenia z maszyną wirtualną ucznia](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Gdy profesor uruchomi maszynę wirtualną i nawiąże połączenie z nią, nie ma to żadnego limitu. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach użycia ucznia (jako właściciel laboratorium), zobacz następujący artykuł: [Konfigurowanie użycia ucznia](how-to-configure-student-usage.md).

Aby dowiedzieć się, w jaki sposób uczniowie mogą resetować hasła dla swoich maszyn wirtualnych, zobacz [Ustawianie lub Resetowanie hasła dla maszyny wirtualnej w laboratoriach z zajęć (students)](how-to-set-virtual-machine-passwords-student.md).