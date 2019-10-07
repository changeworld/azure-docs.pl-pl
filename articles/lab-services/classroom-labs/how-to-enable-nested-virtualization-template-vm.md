---
title: Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services.
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981997"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services
W tym artykule opisano sposób konfigurowania zagnieżdżonej wirtualizacji na maszynie wirtualnej szablonu w Azure Lab Services. Wirtualizacja zagnieżdżona jest używana w Azure Lab Services, gdy każdy student w klasie wymaga wielu maszyn.
 
## <a name="considerations"></a>Uwagi
Przed skonfigurowaniem laboratorium z wirtualizacją zagnieżdżoną poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę.

- Podczas tworzenia nowego laboratorium wybierz opcję **Średni (Wirtualizacja zagnieżdżona)** lub **duże** rozmiary maszyny wirtualnej. Te rozmiary maszyn wirtualnych obsługują wirtualizację zagnieżdżoną. 
- Wybierz rozmiar, który będzie zapewniał dobrą wydajność zarówno dla maszyn wirtualnych hosta, jak i klienta.  Należy pamiętać, że w przypadku korzystania z wirtualizacji wybrany rozmiar musi być odpowiedni dla nie tylko jednego komputera, ale host oraz wszystkie komputery klienckie, które muszą być uruchamiane współbieżnie.
- Maszyny wirtualne klienta nie będą miały dostępu do zasobów platformy Azure, takich jak serwery DNS w sieci wirtualnej platformy Azure.
- Maszyna wirtualna hosta wymaga, aby komputer kliencki miał łączność z Internetem. 
- Klienckie maszyny wirtualne są licencjonowane jako niezależne maszyny. Zobacz temat [Licencjonowanie firmy Microsoft](https://www.microsoft.com/licensing/default) , aby uzyskać informacje o licencjonowaniu systemów operacyjnych i produktów firmy Microsoft. Zapoznaj się z umowami licencyjnymi dotyczącymi wszelkich innych programów używanych przed skonfigurowaniem komputera szablonu.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu
Kroki opisane w tej sekcji koncentrują się na konfigurowaniu zagnieżdżonej wirtualizacji dla systemu Windows Server 2016 lub Windows Server 2019. Za pomocą skryptu można skonfigurować maszynę szablonu przy użyciu funkcji Hyper-V. Aby uzyskać zautomatyzowane rozwiązanie, zobacz skrypty w [usłudze Lab Services skrypty funkcji Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). Poniższe kroki przedstawiają sposób użycia skryptu.

1. Jeśli używasz programu Internet Explorer, może być konieczne dodanie `https://github.com` do listy zaufanych witryn. 
    1. Otwórz program Internet Explorer.
    1. Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Opcje internetowe**.  
    1. Po wyświetleniu okna dialogowego **Opcje internetowe** wybierz pozycję **zabezpieczenia**, wybierz pozycję **Zaufane witryny**, kliknij przycisk **witryny** .
    1. Po wyświetleniu okna dialogowego **Zaufane witryny** Dodaj `https://github.com` do listy zaufanych witryn sieci Web i wybierz pozycję **Zamknij**.

        ![Zaufane witryny](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Pobierz pliki repozytorium git, jak opisano w poniższych krokach.  Alternatywnie repozytorium git można sklonować z [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Przejdź do [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Kliknij przycisk ***Klonuj lub Pobierz** .
    1. Kliknij pozycję **Pobierz plik zip**.
    1. Wyodrębnij plik ZIP
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

## <a name="conclusion"></a>Wniosek
Teraz komputer z szablonem jest gotowy do tworzenia maszyn wirtualnych funkcji Hyper-V. Aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-v, zobacz temat [Tworzenie maszyny wirtualnej w funkcji Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Zobacz również artykuł [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) , aby wyewidencjonować dostępne systemy operacyjne i oprogramowanie.  

## <a name="next-steps"></a>Następne kroki 
Zobacz następujące artykuły:

- [Jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md) 
- [Instalowanie roli funkcji Hyper-V w systemie Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Skrypty funkcji Hyper-V dla usług Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
