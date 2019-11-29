---
title: Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną szablonu z wieloma maszynami wirtualnymi wewnątrz.  Innymi słowy należy włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services.
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
ms.openlocfilehash: 64097a5b3b62bcd5a84f4472a844bb95cf24cd6f
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555084"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services

Obecnie Azure Lab Services umożliwia skonfigurowanie jednego szablonu maszyny wirtualnej w laboratorium i udostępnienie pojedynczej kopii dla każdego z użytkowników. Jeśli jesteś profesorami sieciowymi, bezpieczeństwem lub klasami IT, może być konieczne udostępnienie wszystkim uczniom środowiska, w którym wiele maszyn wirtualnych może komunikować się ze sobą za pośrednictwem sieci.

Wirtualizacja zagnieżdżona umożliwia tworzenie środowiska z obsługą wiele maszyn wirtualnych w ramach maszyny wirtualnej szablonu laboratorium. Opublikowanie szablonu zapewni każdemu użytkownikowi w laboratorium z maszyną wirtualną skonfigurowaną z wieloma maszynami wirtualnymi.  W tym artykule opisano sposób konfigurowania wirtualizacji zagnieżdżonej na komputerze szablonu w Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Co to jest wirtualizacja zagnieżdżona?

Wirtualizacja zagnieżdżona umożliwia tworzenie maszyn wirtualnych w ramach maszyny wirtualnej. Wirtualizacja zagnieżdżona odbywa się za pośrednictwem funkcji Hyper-V i jest dostępna tylko na maszynach wirtualnych z systemem Windows.

Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej, zobacz następujące artykuły:

- [Wirtualizacja zagnieżdżona na platformie Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Przed skonfigurowaniem laboratorium z wirtualizacją zagnieżdżoną poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę.

- Podczas tworzenia nowego laboratorium wybierz rozmiar maszyny wirtualnej **Średni (Wirtualizacja zagnieżdżona)** lub **duża (Wirtualizacja zagnieżdżona)** . Te rozmiary maszyn wirtualnych obsługują wirtualizację zagnieżdżoną.
- Wybierz rozmiar, który będzie zapewniał dobrą wydajność zarówno dla maszyn wirtualnych hosta, jak i klienta.  Należy pamiętać, że w przypadku korzystania z wirtualizacji wybrany rozmiar musi być odpowiedni dla nie tylko jednego komputera, ale host oraz wszystkie komputery klienckie, które muszą być uruchamiane współbieżnie.
- Maszyny wirtualne klienta nie będą miały dostępu do zasobów platformy Azure, takich jak serwery DNS w sieci wirtualnej platformy Azure.
- Maszyna wirtualna hosta wymaga, aby komputer kliencki miał łączność z Internetem.
- Klienckie maszyny wirtualne są licencjonowane jako niezależne maszyny. Zobacz temat [Licencjonowanie firmy Microsoft](https://www.microsoft.com/licensing/default) , aby uzyskać informacje o licencjonowaniu systemów operacyjnych i produktów firmy Microsoft. Zapoznaj się z umowami licencyjnymi dotyczącymi wszelkich innych programów używanych przed skonfigurowaniem komputera szablonu.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu

W tym artykule przyjęto założenie, że utworzono konto laboratorium i laboratorium.  Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md). Aby uzyskać więcej informacji na temat tworzenia laboratorium, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Wybierz opcję **duże (Wirtualizacja zagnieżdżona)** lub **Średni (Wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Wirtualizacja zagnieżdżona nie będzie działała inaczej.  

Aby nawiązać połączenie z maszyną szablonu, zobacz [Tworzenie i zarządzanie szablonem klasy](how-to-create-manage-template.md). 

Kroki opisane w tej sekcji koncentrują się na konfigurowaniu zagnieżdżonej wirtualizacji dla systemu Windows Server 2016 lub Windows Server 2019. Za pomocą skryptu można skonfigurować maszynę szablonu przy użyciu funkcji Hyper-V.  Poniższe kroki przeprowadzą Cię przez proces korzystania ze [skryptów funkcji Hyper-V dla usług Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

1. Jeśli używasz programu Internet Explorer, może być konieczne dodanie `https://github.com` do listy zaufanych witryn.
    1. Otwórz program Internet Explorer.
    1. Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Opcje internetowe**.  
    1. Po wyświetleniu okna dialogowego **Opcje internetowe** wybierz pozycję **zabezpieczenia**, wybierz pozycję **Zaufane witryny**, kliknij przycisk **witryny** .
    1. Po wyświetleniu okna dialogowego **Zaufane witryny** Dodaj `https://github.com` do listy zaufanych witryn sieci Web i wybierz pozycję **Zamknij**.

        ![Zaufane witryny](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1. Pobierz pliki repozytorium git, jak opisano w poniższych krokach.
    1. Przejdź do [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Kliknij przycisk **klonowania lub pobierania** .
    1. Kliknij pozycję **Pobierz plik zip**.
    1. Wyodrębnij plik ZIP

    >[!TIP]
    >Repozytorium git można również sklonować w [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. Uruchom program **PowerShell** w trybie **administratora** .
1. W oknie programu PowerShell przejdź do folderu z pobranym skryptem. Jeśli przechodzenie jest z górnego folderu plików repozytorium, skrypt znajduje się w `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Może być konieczna zmiana zasad wykonywania w celu pomyślnego uruchomienia skryptu. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Uruchom skrypt:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skrypt może wymagać ponownego uruchomienia komputera. Postępuj zgodnie z instrukcjami ze skryptu, a następnie uruchom skrypt do momentu wyświetlenia **skryptu** w danych wyjściowych.
1. Nie zapomnij zresetować zasad wykonywania. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Podsumowanie

Teraz komputer z szablonem jest gotowy do tworzenia maszyn wirtualnych funkcji Hyper-V. Aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-v, zobacz temat [Tworzenie maszyny wirtualnej w funkcji Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Zobacz również artykuł [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) , aby wyewidencjonować dostępne systemy operacyjne i oprogramowanie.  
