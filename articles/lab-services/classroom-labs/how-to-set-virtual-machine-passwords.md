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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 40cdd0adf7bf100e1dbca64dbba68db3bc59a4fe
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331403"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Skonfiguruj pulę maszyn wirtualnych i zarządzaj nią 
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Zwiększ liczbę maszyn wirtualnych w laboratorium
- Uruchom wszystkie maszyny wirtualne lub wybrane maszyny wirtualne 
- Resetowanie maszyn wirtualnych

## <a name="update-the-lab-capacity"></a>Aktualizowanie pojemności laboratorium
Aby zwiększyć lub zmniejszyć wydajność laboratorium (liczbę maszyn wirtualnych w laboratorium), wykonaj następujące czynności:

1. Na stronie **Pula maszyn wirtualnych** wybierz pozycję **pojemność laboratorium: &lt;number @ no__t-3 maszyn**.
2. Wprowadź nową **liczbę maszyn wirtualnych** , które mają być w laboratorium. Ta wartość musi być większa lub równa liczbie użytkowników zarejestrowanych w laboratorium. 
3. Następnie wybierz pozycję **Zapisz**. 

    ![Przycisk Uruchom wszystko](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. W przypadku zwiększenia pojemności można zobaczyć utworzoną maszynę wirtualną lub maszyny wirtualne. 

    ![Maszyna wirtualna jest tworzona](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Uruchom maszyny wirtualne

### <a name="start-ot-stop-all-vms"></a>Rozpocznij zatrzymywanie wszystkich maszyn wirtualnych
1. Przejdź do strony **puli maszyn wirtualnych** . 
2. Wybierz pozycję **Rozpocznij wszystko** na pasku narzędzi. 

    ![Przycisk Uruchom wszystko](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Po rozpoczęciu wszystkich maszyn wirtualnych możesz zatrzymać wszystkie maszyny wirtualne, wybierając przycisk **Zatrzymaj wszystko** na pasku narzędzi. 

    ![Przycisk Zatrzymaj wszystko](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Uruchom wybrane maszyny wirtualne
Istnieją dwa sposoby uruchamiania wybranych maszyn wirtualnych (jeden lub więcej). Pierwszym sposobem jest wybranie maszyny wirtualnej lub maszyn wirtualnych z listy, a następnie na pasku narzędzi wybierz pozycję **Rozpocznij** . Drugi sposób polega na wybraniu maszyny wirtualnej lub maszyn wirtualnych z listy, wybraniu listy rozwijanej w kolumnie **stan** w jednym z wierszy, a następnie wybraniu opcji **Rozpocznij**. 

![Uruchom wybrane maszyny wirtualne](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Podobnie można zatrzymać co najmniej jedną maszynę wirtualną za pomocą listy rozwijanej w kolumnie **stan** lub **zatrzymać** na pasku narzędzi. 

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

    ![Menu resetowania hasła na stronie głównej](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. W oknie dialogowym **Resetowanie hasła** wprowadź hasło, a następnie wybierz pozycję **Resetuj hasło**.
    
    ![Okno dialogowe Ustawianie hasła](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach użycia ucznia (jako właściciel laboratorium), zobacz następujący artykuł: [Konfigurowanie użycia ucznia](how-to-configure-student-usage.md).

Aby dowiedzieć się, w jaki sposób uczniowie mogą resetować hasła dla swoich maszyn wirtualnych, zobacz [Ustawianie lub Resetowanie hasła dla maszyny wirtualnej w laboratoriach z zajęć (students)](how-to-set-virtual-machine-passwords-student.md).