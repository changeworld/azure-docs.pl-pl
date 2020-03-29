---
title: Konfigurowanie ustawień autostartu dla maszyny Wirtualnej w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia autostartu maszyn wirtualnych w laboratorium. To ustawienie umożliwia automatyczne uruchamianie maszyn wirtualnych w laboratorium zgodnie z harmonogramem.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807916"
---
# <a name="auto-startup-lab-virtual-machines"></a>Automatyczne uruchamianie maszyn wirtualnych laboratorium  
Usługa Azure DevTest Labs umożliwia skonfigurowanie maszyn wirtualnych w laboratorium do automatycznego uruchamiania i zamykania na podstawie harmonogramu. Aby uzyskać informacje dotyczące konfigurowania ustawień automatycznego zasłaniania, zobacz [Zarządzanie zasadami automatycznego zasłaniania dla laboratorium w laboratorium DevTest Labs platformy Azure](devtest-lab-auto-shutdown.md). 

W przeciwieństwie do autoshutdown, gdzie wszystkie maszyny wirtualne są uwzględniane, gdy zasady są włączone, zasady autostart wymaga użytkownika laboratorium jawnie wybrać maszynę wirtualną i zdecydować się na ten harmonogram. W ten sposób nie będzie łatwo uruchomić w sytuacji, gdy niechciane maszyny wirtualne są przypadkowo automatycznie uruchamiane i spowodować nieoczekiwane wydatki.

W tym artykule pokazano, jak skonfigurować zasady autostartu dla laboratorium.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurowanie ustawień autostartu dla laboratorium 
1. Przejdź do strony głównej laboratorium. 
2. Wybierz **opcję Konfiguracja i zasady** w menu po lewej stronie. 

    ![Menu Konfiguracja i zasady](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na stronie **Konfiguracja i zasady** wykonaj następujące czynności:
    
    1. Wybierz **pozycję Włącz,** **aby zezwolić na automatyczne uruchamianie maszyn wirtualnych,** aby włączyć funkcję automatycznego uruchamiania dla tego laboratorium. 
    2. Wybierz godzinę rozpoczęcia (na przykład: 08:00:00) dla pola **Rozpoczęcie harmonogramu.** 
    3. Wybierz **strefę czasową,** która ma być używana. 
    4. Wybierz **dni tygodnia,** w których maszyny wirtualne muszą być uruchamiane automatycznie. 
    5. Następnie wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. 

        ![Ustawienia autostartu](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Ta zasada nie powoduje automatycznego uruchamiania do żadnych maszyn wirtualnych w laboratorium. Aby **zdecydować się na** poszczególne maszyny wirtualne, przejdź do strony maszyny wirtualnej i włącz automatyczne **uruchamianie** dla tej maszyny wirtualnej.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Włączanie automatycznego uruchamiania maszyny Wirtualnej w laboratorium
Poniższa procedura zawiera kroki dotyczące włączania maszyny wirtualnej do zasad autostart w laboratorium. 

1. Na stronie głównej laboratorium wybierz maszynę **wirtualną** na liście **Moje maszyny wirtualne.** 

    ![Menu Konfiguracja i zasady](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na stronie **Maszyna wirtualna** wybierz pozycję **Autostart** w menu po lewej stronie lub na liście **Harmonogramy.** 

    ![Wybierz menu autostartu](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na stronie **Autostart** wybierz pozycję **Włącz,** aby wybrać opcję **Zezwalaj na tę maszynę wirtualną do automatycznego uruchamiania.**

    ![Włączanie automatycznego uruchamiania maszyny Wirtualnej](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Następnie wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać to ustawienie. 


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zasadach automatycznego zasłaniania konfiguracji w laboratorium, zobacz [Zarządzanie zasadami automatycznego zasłaniania dla laboratorium w laboratoriach devtest azure](devtest-lab-auto-shutdown.md)
