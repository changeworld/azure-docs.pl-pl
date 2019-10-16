---
title: Włączanie środowisk z obsługą wielu maszyn wirtualnych w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć środowisko z wieloma maszynami wirtualnymi w ramach maszyny wirtualnej szablonu w laboratorium z klasą Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332331"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Tworzenie środowiska z wieloma maszynami wirtualnymi wewnątrz szablonu maszyny wirtualnej laboratorium zajęć
Obecnie Azure Lab Services umożliwia skonfigurowanie jednego szablonu maszyny wirtualnej w laboratorium i udostępnienie pojedynczej kopii dla każdego użytkownika. Ale jeśli jesteś profesorą, aby wyuczyć się, jak skonfigurować zapory lub serwery, może być konieczne udostępnienie wszystkim uczniom środowiska, w którym wiele maszyn wirtualnych może komunikować się ze sobą za pośrednictwem sieci.

Wirtualizacja zagnieżdżona umożliwia tworzenie środowiska z obsługą wiele maszyn wirtualnych w ramach maszyny wirtualnej szablonu laboratorium. Opublikowanie szablonu zapewni każdemu użytkownikowi w laboratorium z maszyną wirtualną skonfigurowaną z wieloma maszynami wirtualnymi.

## <a name="what-is-nested-virtualization"></a>Co to jest wirtualizacja zagnieżdżona?
Wirtualizacja zagnieżdżona umożliwia tworzenie maszyn wirtualnych w ramach maszyny wirtualnej. Wirtualizacja zagnieżdżona odbywa się za pośrednictwem funkcji Hyper-V i jest dostępna tylko na maszynach wirtualnych z systemem Windows.

Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej, zobacz następujące artykuły:

- [Wirtualizacja zagnieżdżona na platformie Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Używanie wirtualizacji zagnieżdżonej w Azure Lab Services
Ważne czynności są następujące:

1. Utwórz komputer szablonu **systemu Windows** o **dużej** wielkości dla laboratorium. 
2. Połącz się z nim i [Włącz wirtualizację zagnieżdżoną](../../virtual-machines/windows/nested-virtualization.md).


Poniższa procedura zawiera szczegółowe kroki: 

1. Utwórz konto laboratorium, jeśli jeszcze go nie masz. Aby uzyskać instrukcje, zobacz [Samouczek: Konfigurowanie konta laboratorium przy użyciu Azure Lab Services](tutorial-setup-lab-account.md).
1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Wybierz pozycję **nowe laboratorium**. 
    
    ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Wybierz opcję **duże (Wirtualizacja zagnieżdżona)** lub **Średni (Wirtualizacja zagnieżdżona)** dla **rozmiaru maszyny wirtualnej**.
    6. Wybierz **obraz** systemu Windows, który ma być używany. Wirtualizacja zagnieżdżona jest dostępna tylko na maszynach z systemem Windows. 
    4. Następnie wybierz opcję **Dalej**. 

        ![Tworzenie laboratorium na potrzeby zajęć](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Na stronie **poświadczenia maszyny wirtualnej** Określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. Określ **nazwę** i **hasło** użytkownika, a następnie wybierz przycisk **dalej**.  

        ![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Na stronie **zasady laboratorium** wprowadź liczbę godzin wyznaczonych dla każdego użytkownika (**limit przydziału dla każdego użytkownika**) poza zaplanowanym terminem dla laboratorium, a następnie wybierz pozycję **Zakończ**. 

        ![Przydział dla każdego użytkownika](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Powinien zostać wyświetlony następujący ekran pokazujący stan tworzenia szablonu maszyny wirtualnej. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Stan tworzenia szablonu maszyny wirtualnej](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Na stronie **szablon** wybierz pozycję **Dostosuj szablon** na pasku narzędzi. 

    ![Przycisk Dostosuj szablon](../media/how-to-create-manage-template/customize-template-button.png)
2. W oknie dialogowym **Dostosowywanie szablonu** wybierz pozycję **Kontynuuj**. Po rozpoczęciu szablonu i wprowadzeniu zmian nie będzie on już miał tego samego ustawienia co maszyny wirtualne ostatnio opublikowane dla użytkowników. Zmiany szablonów nie zostaną odzwierciedlone na istniejących maszynach wirtualnych użytkowników do momentu ponownego opublikowania.

    ![Dostosuj okno dialogowe](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Wybierz przycisk **Połącz z szablonem** na pasku narzędzi, aby połączyć się z SZABLONową maszyną wirtualną w celu skonfigurowania wirtualizacji zagnieżdżonej. i postępuj zgodnie z instrukcjami. Jeśli jest to maszyna z systemem Windows, zostanie wyświetlona opcja pobrania pliku RDP. 

    ![Łączenie z maszyną wirtualną szablonu](../media/how-to-create-manage-template/connect-template-vm.png) 
9. Na maszynie wirtualnej szablonu należy skonfigurować wirtualizację zagnieżdżoną i skonfigurować sieć wirtualną z wieloma maszynami wirtualnymi. Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md). Oto krótkie podsumowanie kroków: 
    1. Włącz funkcję Hyper-V na maszynie wirtualnej szablonu.
    2. Konfigurowanie wewnętrznej sieci wirtualnej z łącznością z Internetem dla zagnieżdżonych maszyn wirtualnych
    3. Tworzenie maszyn wirtualnych za pomocą Menedżera funkcji Hyper-V
    4. Przypisywanie adresu IP do maszyn wirtualnych
10. Na stronie **szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Publikuj szablon](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
8. Na stronie **szablon publikacji** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Publikuj**. 

    ![Szablon publikacji — liczba maszyn wirtualnych](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Zobaczysz **stan publikowania** szablonu na stronie. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Następne kroki

Teraz każdy użytkownik pobiera pojedynczą maszynę wirtualną, która zawiera środowisko obsługujące wiele maszyn wirtualnych. Aby dowiedzieć się, jak dodać użytkowników do laboratorium i wysłać do nich link rejestracji, zobacz następujący artykuł: [Dodawanie użytkowników do laboratorium](tutorial-setup-classroom-lab.md#add-users-to-the-lab).