---
title: Włączanie wielu maszyn wirtualnych środowisk w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowisko z wieloma maszynami wirtualnymi wewnątrz szablonu maszyny wirtualnej w laboratorium na potrzeby zajęć z usługi Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702444"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Utwórz środowisko z wieloma maszynami wirtualnymi w szablonie maszyny Wirtualnej laboratorium na potrzeby zajęć
Obecnie usługi Azure Lab Services pozwala na konfigurowanie jednego szablonu maszyny wirtualnej w laboratorium i udostępnić pojedynczej kopii każdego z użytkowników. Ale jeśli wykładowcą nauczania klasę IT na temat sposobu konfigurowania zapory lub serwery, konieczne może być zapewnić wszystkich swoich uczniów środowisko, w którym wiele maszyn wirtualnych mogą komunikować się ze sobą za pośrednictwem sieci.

Wirtualizacja zagnieżdżona pozwala utworzyć środowisko z wieloma Maszynami wewnątrz maszyny wirtualnej szablonu laboratorium. Publikowanie szablonu zapewni każdego użytkownika w środowisku laboratoryjnym przy użyciu maszyn wirtualnych z wieloma maszynami wirtualnymi w nim.

## <a name="what-is-nested-virtualization"></a>Co to jest wirtualizacja zagnieżdżona?
Wirtualizacja zagnieżdżona umożliwia tworzenie maszyn wirtualnych w ramach maszyny wirtualnej. Wirtualizacja zagnieżdżona jest realizowane za pomocą funkcji Hyper-V i jest dostępna tylko na maszynach wirtualnych Windows.

Więcej informacji na temat wirtualizacji zagnieżdżonej na ten temat można znaleźć w następujących artykułach:

- [Wirtualizacja zagnieżdżona na platformie Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak włączyć wirtualizacji zagnieżdżonej na Maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Używanie wirtualizacji zagnieżdżonej w usłudze Azure Lab Services
Ważne czynności są następujące:

1. Tworzenie **dużych** wielkości **Windows** maszyny szablonu dla laboratorium. 
2. Połącz z nim i [włączać wirtualizację zagnieżdżoną](../../virtual-machines/windows/nested-virtualization.md).


Poniższa procedura zawiera szczegółowy opis kroków: 

1. Utwórz konto laboratorium, jeśli nie masz jeszcze takiego. Aby uzyskać instrukcje, zobacz [samouczka: Konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services](tutorial-setup-lab-account.md).
2. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
3. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
4. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Określ maksymalną **liczbę maszyn wirtualnych** w laboratorium. Liczbę maszyn wirtualnych można zmniejszyć lub zwiększyć po utworzeniu laboratorium lub w istniejącym laboratorium. Aby uzyskać więcej informacji, zobacz [Update number of VMs in a lab (Aktualizowanie liczby maszyn wirtualnych w laboratorium)](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stronie **Wybór specyfikacji maszyny wirtualnej** wykonaj następujące czynności:
    1. Wybierz **duże** rozmiaru maszyn wirtualnych (VM) do utworzenia w środowisku laboratoryjnym. Obecnie tylko duży rozmiar obsługuje wirtualizację zagnieżdżoną.
    2. Wybierz obraz maszyny wirtualnej, która jest **obrazu Windows**. Wirtualizacja zagnieżdżona jest dostępna tylko na komputerach Windows. 
    3. Wybierz opcję **Dalej**.

        ![Określanie specyfikacji maszyny wirtualnej](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. Na stronie **Ustawianie poświadczeń** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. 
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wybierz pozycję **Utwórz**. 

        ![Ustawianie poświadczeń](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stronie **Konfigurowanie szablonu** zostanie wyświetlony stan procesu tworzenia laboratorium. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po zakończeniu konfiguracji szablonu zostanie wyświetlona następująca strona: 

    ![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Na **Konfigurowanie szablonu** wybierz opcję **Connect** połączyć się z szablonu maszyny Wirtualnej, aby skonfigurować wirtualizacji zagnieżdżonej. Można również skonfigurować później po wykonaniu czynności opisanych w tym kreatorze. 
9. Wewnątrz szablonu maszyny wirtualnej należy skonfigurować wirtualizacji zagnieżdżonej i skonfiguruj sieć wirtualną z wieloma maszynami wirtualnymi. Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [jak włączyć wirtualizacji zagnieżdżonej na Maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md). Poniżej przedstawiono krótkie podsumowanie kroków: 
    1. Włącz funkcję Hyper-V w szablonie maszyny wirtualnej.
    2. Konfigurowanie wewnętrzną siecią wirtualną za pomocą połączenie z Internetem dla zagnieżdżonych maszyn wirtualnych
    3. Tworzenie maszyn wirtualnych za pomocą Menedżera funkcji Hyper-V
    4. Przypisz adres IP do maszyn wirtualnych
10. Wybierz przycisk **Dalej** na stronie szablonu. 
11. Na stronie **Publikowanie szablonu** wykonaj następujące czynności. 
    1. Aby opublikować szablon natychmiast, wybierz pozycję **Publikuj**.  

        > [!WARNING]
        > Nie można cofnąć publikowania szablonu. 
    2. Aby opublikować później, wybierz pozycję **Zachowaj na później**. Maszynę wirtualną szablonu można opublikować po zakończeniu działania kreatora. Aby uzyskać więcej informacji na temat sposobu konfigurowania i publikowania po zakończeniu działania kreatora, zobacz sekcję [Publikowanie szablonu](how-to-create-manage-template.md#publish-the-template-vm) w artykule [Jak zarządzać laboratoriami na potrzeby zajęć](how-to-manage-classroom-labs.md).

        ![Publikowanie szablonu](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Zostanie wyświetlony **postęp publikowania** szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po pomyślnym opublikowaniu szablonu zostanie wyświetlona następująca strona. Wybierz pozycję **Done** (Gotowe).

    ![Publikowanie szablonu — powodzenie](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Kolejne kroki

Teraz każdy użytkownik otrzymuje jedna maszyna wirtualna, zawierający środowisko z wieloma Maszynami w nim. Aby dowiedzieć się, jak dodać użytkowników do laboratorium i wysłać link rejestracji do nich, zobacz następujący artykuł: [Dodawanie użytkowników do laboratorium](tutorial-setup-classroom-lab.md#add-users-to-the-lab).