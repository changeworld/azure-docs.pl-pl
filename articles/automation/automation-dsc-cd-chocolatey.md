---
title: Konfiguracja stanu usługi Azure Automation ciągłe wdrażanie za pomocą Chocolatey
description: Ciągłe wdrażanie metodyki DevOps przy użyciu usługi Azure Automation State Configuration, DSC i Chocolatey Menedżera pakietów.  Przykład z pełnym szablonem usługi Resource Manager w formacie JSON i źródła programu PowerShell.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b53cb65ec99637dadb16ed9d97c495571be956d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073916"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Przykład użycia: Ciągłe wdrażanie na maszynach wirtualnych za pomocą automatyzacji konfiguracji stanu i narzędzia Chocolatey

W świecie DevOps istnieje wiele narzędzi, która pomaga w różnych punktach w potoku ciągłej integracji. Konfiguracja stanu usługi Azure Automation jest powitalnej nowy dodatek opcje, które mogą wykorzystywać zespoły DevOps. W tym artykule przedstawiono ustawienia zapasowej ciągłego wdrażania (polecenie CD) na komputerze Windows. Możesz łatwo rozszerzyć techniki, aby dołączyć dowolną liczbę komputerów Windows zgodnie z potrzebami w roli (witryna sieci web, na przykład), a w tym miejscu można również dodatkowe role.

![Ciągłe wdrażanie dla maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie

Brak znacznej dzieje się w tym miejscu, ale na szczęście może można podzielić na dwie główne procesy:

- Pisanie kodu i testowanie go, a następnie tworzenie i publikowanie pakietów instalacyjnych dla wersji głównych i pomocniczych systemu.
- Tworzenie i zarządzanie nimi maszyn wirtualnych, które można zainstalować i wykonywania kodu w pakietach.  

Gdy oba procesy core znajdują się w miejscu, jest krótki krok do automatycznego aktualizowania pakietu uruchomionych na dowolnej maszynie Wirtualnej z określonego jak nowe wersje są tworzone i wdrażane.

## <a name="component-overview"></a>Składnik — omówienie

Menedżerowie pakietów, takich jak [polecenia apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) są dość dobrze znane w świecie systemu Linux, ale nie tak wiele w środowisku Windows.
[Chocolatey](https://chocolatey.org/) to takich rzeczy oraz zarządzanie zasobami Scott Hanselman [blogu](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) tematu jest doskonałym wprowadzenie. Mówiąc Chocolatey pozwala na instalowanie pakietów z centralnego repozytorium pakietów w systemie Windows przy użyciu wiersza polecenia. Można tworzyć i zarządzać własnym repozytorium i Chocolatey zainstalować pakiety z dowolnej liczby repozytoriów, które należy wyznaczyć.

Desired State Configuration (DSC) ([Przegląd](/powershell/dsc/overview)) to narzędzie programu PowerShell, które służy do deklarowania konfigurację dla maszyny. Na przykład można powiedzieć "Chcę, aby zainstalować narzędzia Chocolatey, chcę, aby po zainstalowaniu usług IIS, chcę, aby otworzyć port 80, ma wersję 1.0.0 Moja witryna sieci Web zainstalowany." DSC lokalnego Configuration Manager (LCM) implementuje tę konfigurację. Serwera ściągania DSC przechowuje repozytorium konfiguracji maszyn. LCM na każdej maszynie, sprawdza okresowo jeśli jego konfiguracja jest zgodna przechowuje konfigurację. Jego stan lub spróbuj przywrócić komputer spełnia wymagania programu przechowuje konfigurację. Możesz edytować przechowuje konfigurację na serwerze ściągania, aby spowodować, że maszyna lub zestawu maszyn do dostosowania do zmiany konfiguracji.

Usługa Azure Automation to zarządzana usługa w systemie Microsoft Azure, która umożliwia automatyzowanie różnych zadań, za pomocą elementów runbook, węzły, poświadczenia, zasobów i zasoby, takie jak harmonogramy i zmiennych globalnych.
Konfiguracja stanu usługi Automatyzacja Azure rozszerza tej możliwości automatyzacji, aby dołączyć narzędzia do DSC programu PowerShell. W tym miejscu jest doskonałym [Przegląd](automation-dsc-overview.md).

Zasób DSC jest modułem kodu, który zawiera konkretne funkcje, takie jak zarządzanie sieci, usługi Active Directory lub programu SQL Server. Chocolatey zasobów DSC wie, jak uzyskać dostęp do serwera NuGet (między innymi), Pobierz pakiety, zainstalować pakiety i tak dalej. Dostępnych jest wiele innych zasobów DSC w [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Te moduły są zainstalowane na serwerze usługi Azure Automation stanu konfiguracji ściągania (przez użytkownika), dzięki czemu może służyć przez konfiguracje.

Szablony usługi Resource Manager zapewniają deklaratywną metodę generowania infrastruktury — np. sieci, podsieci, zabezpieczeń sieci i routingu, modułów równoważenia obciążenia, kart sieciowych, maszyny wirtualne i tak dalej. Oto [artykułu](../azure-resource-manager/resource-manager-deployment-model.md) porównuje modelu wdrażania usługi Resource Manager (deklaratywne) przy użyciu modelu wdrażania usługi Azure Service Management (ASM lub classic) (imperatywne), a w tym artykule omówiono podstawowe dostawców zasobów, moc obliczeniowa, Magazyn i sieci.

Kluczowa funkcja klasy szablonu usługi Resource Manager polega na instalowanie rozszerzenia maszyny Wirtualnej do maszyny Wirtualnej, zgodnie z jej zaaprowizowaniu. Rozszerzenie maszyny Wirtualnej ma konkretne funkcje takie jak uruchomienie niestandardowego skryptu, instalowanie oprogramowania antywirusowego lub uruchamianie skryptu konfiguracji DSC. Istnieje wiele typów rozszerzeń maszyny Wirtualnej.

## <a name="quick-trip-around-the-diagram"></a>Szybkie podróży wokół diagramu

Począwszy od góry pisania kodu, kompilacji i testowania, a następnie utwórz pakiet instalacyjny.
Narzędzia Chocolatey może obsługiwać różne rodzaje pakietów instalacyjnych, na przykład pliku MSI polecenie MSU, ZIP. I pełnych możliwości programu PowerShell w razie rzeczywista instalacja Chocolateys natywnych możliwości nie są dość maksymalnie go. Wprowadzenia pakietu niektóre dostępne — repozytorium pakietów. W tym przykładzie użycie użyto folderu publicznego na koncie magazynu obiektów blob platformy Azure, ale może być dowolnym miejscu. Narzędzia Chocolatey działa natywnie przy użyciu serwerów NuGet i kilka innych zasobów pod kątem zarządzania metadane pakietów. [W tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. W tym przykładzie użycie użyto NuGet. Nuspec to metadane dotyczące pakietów. Nuspec "kompilowania", do jego NuPkg i przechowywane na serwerze NuGet. Gdy konfigurację zażąda pakiet według nazwy, a następnie odwołuje się serwer NuGet, Chocolatey zasobów DSC (teraz na maszynie Wirtualnej) bierze pakietu i zainstaluje ją automatycznie. Możesz również poprosić o określonej wersji pakietu.

W lewej dolnej części obrazu jest szablonu usługi Azure Resource Manager. W tym przykładzie użycie rozszerzenia maszyny Wirtualnej rejestruje tej maszyny Wirtualnej z serwera usługi Azure Automation stan konfiguracji ściągnięcia (czyli serwera ściągania) jako węzeł. Konfiguracja jest przechowywana na serwerze ściągania.
W rzeczywistości jest on przechowywany dwa razy: jeden raz w postaci zwykłego tekstu i gdy kompilowany jako plik MOF (dla tych, które wiedzieć o takich operacji.) W portalu MOF jest "konfiguracji węzła" (a nie po prostu "Konfiguracja"). Jest artefakt, który jest skojarzona z węzłem, więc węzeł będzie wiadomo, jego konfiguracji. Szczegóły poniżej pokazano, jak można przypisać konfiguracji węzła do węzła.

Prawdopodobnie już wykonujesz bitu u góry lub część. Tworzenie nuspec, kompilowanie i przechowywanie ich w serwer NuGet jest to mała. I już zarządzania maszynami wirtualnymi. Przejściem do następnego kroku w celu ciągłego wdrażania wymaga Konfigurowanie serwera ściągania (po), zarejestrowani węzły (po) oraz tworzenia i przechowywania konfiguracji istnieje w (wstępnie). Następnie pakiety są uaktualnione i wdrażane w repozytorium Odśwież konfiguracji i konfiguracja węzła na serwerze ściągania (powtórzeń, zgodnie z potrzebami).

Jeśli one nie uruchamia się za pomocą szablonu usługi Resource Manager, który również jest OK. Brak ułatwiający rejestrowanie maszyn wirtualnych z serwera ściągania oraz wszystkie pozostałe polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji znajduje się w artykule: [Dołączanie maszyn w celu zarządzania usługi Azure Automation stan konfiguracji](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Konfigurowanie pull server i konta usługi automation

Pod uwierzytelnionego (`Connect-AzureRmAccount`) wiersza polecenia programu PowerShell: (może potrwać kilka minut, gdy skonfigurowano serwera ściągania)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Konto usługi automation można umieścić w dowolny spośród następujących regionów (zwane również lokalizacja): Wschodnie stany USA 2, południowo-środkowe stany USA, administracja USA — Wirginia, Europa Zachodnia, Azja południowo-wschodnia, Japonia Wschodnia, Indie środkowe i Australia Południowo-Wschodnia, Kanada Środkowa, Europa Północna.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Ulepszeń rozszerzenia maszyny Wirtualnej w szablonie usługi Resource Manager

Szczegóły rejestracji maszyny Wirtualnej (przy użyciu rozszerzenia maszyny Wirtualnej DSC programu PowerShell) podany w tej [szablon szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
W tym kroku rejestruje swojej nowej maszyny Wirtualnej z serwera ściągania na liście stanie konfiguracji węzłów. Część tej rejestracji jest określenie konfiguracji węzła, który ma zostać zastosowany do węzła. Ta konfiguracja węzeł nie ma jeszcze istnieje na serwerze ściągania, więc jest OK, który jest krok 4, gdzie odbywa się po raz pierwszy. Ale w tym miejscu w kroku 2 należy decyduje nazwę węzła i nazwa konfiguracji. W tym przykładzie użycie węzeł, który jest "isvbox", a konfiguracja jest "ISVBoxConfig". Nazwa konfiguracji węzła (w celu określenia w DeploymentTemplate.json) więc "ISVBoxConfig.isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Dodawanie wymaganych zasobów DSC na serwerze ściągania

Galeria programu PowerShell ma instrumentacji do zainstalowania zasobów DSC na koncie usługi Azure Automation.
Przejdź do zasobu i kliknij przycisk "Wdrażanie do usługi Azure Automation".

![Przykładu z galerii programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika ostatnio dodane do witryny Azure Portal umożliwia ściągać nowych modułów lub zaktualizować istniejące moduły. Klikaj elementy zasobów konta usługi Automation, Kafelek zasobów, a na końcu kafelka modułów. Ikona Przeglądaj galerię pozwala zapoznać się z listą modułów w galerii, przechodzenie do szczegółów i ostatecznie importowanie do konta usługi Automation. Jest to doskonały sposób moduły na bieżąco od czasu do czasu. Ponadto funkcji importowania sprawdza zależności za pomocą innych modułów, aby upewnić się, że nic nie jest niezsynchronizowana.

Lub brak podejście ręcznego. Struktura folderów modułu integracji programu PowerShell na komputerze Windows jest nieco inne niż strukturę folderów, oczekiwany przez usługę Azure Automation.
Ta migracja wymaga nieco Dostosowywanie ze strony użytkownika. Ale nie jest trudne i wykonywane tylko raz dla każdego zasobu (chyba że chcesz uaktualnić go w przyszłości.) Więcej informacji na temat tworzenia modułów integracji programu PowerShell znajduje się w artykule: [Tworzenie modułów integracji dla usługi Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Zainstaluj moduł, który należy się na stacji roboczej w następujący sposób:
  - Zainstaluj [Windows Management Framework w wersji 5](https://aka.ms/wmf5latest) (nie wymagane dla systemu Windows 10)
  - `Install-Module –Name MODULE-NAME`    < — bierze modułu z galerii programu PowerShell
- Skopiuj folder modułu z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do folderu tymczasowego
- Usuń przykłady i dokumentację z folderu głównego
- Folderu głównego, nazwy pliku ZIP, dokładnie tak samo jak folder zip 
- Umieść plik ZIP na dostępny lokalizacji HTTP, takie jak magazyn obiektów blob na koncie usługi Azure Storage.
- Uruchom ten program PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Dołączony przykład wykonuje następujące kroki cChoco i xNetworking. Zobacz [uwagi](#notes) dla specjalna obsługa cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Dodawanie konfiguracji węzła do serwera ściągania

Nie ma nic specjalne dotyczące importowania konfiguracji na serwerze ściągania i kompilacji po raz pierwszy. Wszystkie kolejne import/kompiluje o tej samej konfiguracji wyglądać dokładnie takie same. Każdorazowo, pakiet aktualizacji i aby wdrożyć je do środowiska produkcyjnego, możesz wykonać ten krok, po upewnieniu się, że plik konfiguracji jest prawidłowy — w tym nową wersję pakietu. Poniżej przedstawiono plik konfiguracji i środowiska PowerShell:

ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

New-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Wynik tych kroków, w nowej konfiguracji węzła o nazwie "ISVBoxConfig.isvbox" na serwerze ściągania. Nazwa konfiguracji węzła została stworzona jako "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5. Utworzenie i utrzymywanie metadane pakietu

Dla każdego pakietu, który można umieścić w repozytorium pakietu należy nuspec, który ją opisuje.
Ten nuspec musi zostanie skompilowany i przechowywane na serwerze NuGet. Ten proces jest opisany [tutaj](https://docs.nuget.org/create/creating-and-publishing-a-package). Jako serwer NuGet, można użyć portalu MyGet.org. One sprzedaży tej usługi, ale mają starter jednostki SKU, która jest bezpłatna. W witrynie NuGet.org znajdziesz instrukcje dotyczące instalowania serwer NuGet własne swoje prywatne pakiety.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Łącząc je wszystkie razem

Każdorazowo wersji przekazuje pytań i odpowiedzi, a zostanie zatwierdzona do wdrożenia, pakiet jest tworzony, nuspec i nupkg zaktualizowane i wdrożyć serwer NuGet. Ponadto należy zaktualizować musieli się zgodzić za pomocą nowego numeru wersji konfiguracji (krok 4 powyżej). Musi być wysyłane do serwera ściągania i skompilowany.
Od tego momentu to maszyny wirtualne, które są zależne od tej konfiguracji w celu pobierania aktualizacji i zainstalowania go. Każda z tych aktualizacji jest proste — po prostu linię lub dwóch od programu PowerShell. W przypadku usługi Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które można łączyć w łańcuch w kompilacji. To [artykułu](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółowych informacji. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) szczegółowo różne zadania dostępne kompilacji.

## <a name="notes"></a>Uwagi

W tym przykładzie użycie rozpoczyna się od maszyny Wirtualnej z ogólny obraz systemu Windows Server 2012 R2 z poziomu galerii Azure. Można uruchomić z dowolnego przechowywanego obrazu i następnie dostosować w tym miejscu przy użyciu konfiguracji DSC.
Zmiana konfiguracji, który jest rozszerzania obrazu jest jednak znacznie trudniejsze niż dynamiczne aktualizowanie konfiguracji za pomocą DSC.

Nie masz tej techniki należy używać z maszynami wirtualnymi za pomocą szablonu usługi Resource Manager i rozszerzenia maszyny Wirtualnej. I maszyny wirtualne nie muszą znajdować się na platformie Azure, aby być objęty zarządzaniem CD. Wszystko, co jest konieczne jest zainstalowanie narzędzia Chocolatey i LCM skonfigurowane na maszynie Wirtualnej będzie wówczas traktował w przypadku serwera ściągania.

Oczywiście po zaktualizowaniu pakietów na maszynie Wirtualnej, który znajduje się w środowisku produkcyjnym należy podjąć tej maszyny Wirtualnej z rotacji, gdy aktualizacja jest zainstalowana. Jak ta wartość znacznie się zmienia. Na przykład na maszynach wirtualnych za modułem równoważenia obciążenia platformy Azure, możesz dodać, niestandardowe sondy. Podczas aktualizowania maszyny Wirtualnej, należy mieć końcowego sondy zwracają 400. Dopasujemy niezbędne spowodować, że ta zmiana może znajdować się wewnątrz konfiguracji, tak jak w dopasujemy, aby przełączyć się do zwracania 200, po zakończeniu aktualizacji.

Trwa źródłowego w tym przykładzie użycie [tego projektu programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w witrynie GitHub.

## <a name="related-articles"></a>Powiązane artykuły
* [Omówienie DSC usługi Azure Automation](automation-dsc-overview.md)
* [Polecenia cmdlet usługi Azure Automation DSC](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [konfiguracji stan automatyzacji platformy Azure](automation-dsc-overview.md)
- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
