---
title: Włączanie wirtualizacji zagnieżdżonej na szablonie maszyny Wirtualnej w usłudze Azure Lab Services (Skrypt) | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć szablon maszyny Wirtualnej z wieloma maszynami wirtualnymi wewnątrz.  Innymi słowy włącz zagnieżdżoną wirtualizację na szablonie maszyny Wirtualnej w usłudze Azure Lab Services.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503038"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usłudze Azure Lab Services przy użyciu skryptu

Wirtualizacja zagnieżdżona umożliwia utworzenie środowiska wielu maszyn wirtualnych wewnątrz maszyny wirtualnej szablonu laboratorium. Publikowanie szablonu zapewni każdemu użytkownikowi w laboratorium maszynę wirtualną skonfigurowaną z wieloma maszynami wirtualnymi w nim.  Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej i usług Azure Lab Services, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usługach Azure Lab Services.](how-to-enable-nested-virtualization-template-vm.md)

W tym artykule skupiono się na konfigurowaniu zagnieżdżonej wirtualizacji dla systemu Windows Server 2016 lub Windows Server 2019. Skrypt służy do konfigurowania maszyny szablonu z funkcji Hyper-V.  Poniższe kroki poprowadzą Cię przez sposób korzystania ze [skryptów Funkcji Hyper-V usług laboratoryjnych](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Wybierz **opcję Duże (wirtualizacja zagnieżdżona)** lub **Średnia (wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Zagnieżdżona wirtualizacja nie będzie działać inaczej.  

## <a name="run-script"></a>Uruchamianie skryptu

1. Jeśli używasz programu Internet Explorer, może `https://github.com` być trzeba dodać do listy zaufanych witryn.
    1. Otwórz program Internet Explorer.
    1. Wybierz ikonę koła zębatego i wybierz **opcję Opcje internetowe**.  
    1. Po wyświetleniu okna dialogowego **Opcje internetowe** wybierz pozycję **Zabezpieczenia**, wybierz pozycję **Zaufane witryny**, kliknij przycisk **Witryny.**
    1. Po wyświetleniu okna dialogowego **Zaufane witryny** dodaj `https://github.com` je do listy zaufanych witryn sieci Web i wybierz pozycję **Zamknij**.

        ![Zaufane witryny](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Pobierz pliki repozytorium Git zgodnie z poniższymi krokami.
    1. Przejdź [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)do .
    1. Kliknij przycisk **Klonuj lub Pobierz.**
    1. Kliknij **pozycję Pobierz plik ZIP**.
    1. Wyodrębnianie pliku ZIP

    >[!TIP]
    >Repozytorium Git można również sklonować w pliku [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. Uruchom **program PowerShell** w trybie **administratora.**
1. W oknie programu PowerShell przejdź do folderu z pobranym skryptem. Jeśli nawigujesz z górnego folderu plików repozytorium, skrypt `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`znajduje się pod adresem .
1. Może być konieczna zmiana zasad wykonywania, aby pomyślnie uruchomić skrypt. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Uruchom skrypt:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skrypt może wymagać ponownego uruchomienia komputera. Postępuj zgodnie z instrukcjami ze skryptu i ponownie uruchomić skrypt, dopóki **skrypt ukończony** nie zostanie widoczny w danych wyjściowych.
1. Nie zapomnij zresetować zasad wykonywania. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Podsumowanie

Teraz maszyna szablonu jest gotowa do tworzenia maszyn wirtualnych funkcji Hyper-V. Zobacz [Tworzenie maszyny wirtualnej w funkcji Hyper-V,](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-V. Zobacz Centrum [oceny firmy Microsoft,](https://www.microsoft.com/evalcenter/) aby sprawdzić dostępne systemy operacyjne i oprogramowanie.  

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)