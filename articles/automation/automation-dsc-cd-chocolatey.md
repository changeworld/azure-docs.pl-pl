---
title: Ciągłe wdrażanie konfiguracji stanu Azure Automation przy użyciu czekolady
description: DevOps ciągłe wdrażanie przy użyciu konfiguracji stanu Azure Automation, DSC i Menedżera pakietów czekolady.  Przykład z pełnym szablonem Menedżer zasobów JSON i źródłem programu PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: ad42d7c2257519c2622ba17f74f97b9521233850
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366434"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Przykład użycia: ciągłe wdrażanie do Virtual Machines przy użyciu konfiguracji stanu automatyzacji i czekolady

Na świecie DevOps istnieje wiele narzędzi, które pomagają w różnych punktach w potoku ciągłej integracji. Konfiguracja stanu Azure Automation jest nowym uzupełnieniem opcji, które mogą być używane przez zespoły DevOps. W tym artykule przedstawiono Konfigurowanie ciągłego wdrażania (CD) dla komputera z systemem Windows. Można łatwo zwiększyć technikę, aby dołączać tyle komputerów z systemem Windows, jak to konieczne, w roli (na przykład w witrynie sieci Web), a także do dodatkowych ról.

![Ciągłe wdrażanie dla maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie

W tym miejscu występuje już nieco, ale na szczęście można podzielić na dwa główne procesy:

- Pisanie kodu i testowanie go, a następnie Tworzenie i publikowanie pakietów instalacyjnych dla wersji głównych i pomocniczych systemu.
- Tworzenie maszyn wirtualnych, które będą instalować i uruchamiać kod w pakietach, oraz zarządzanie nimi.  

Po zakończeniu obu tych podstawowych procesów jest to krótki krok, aby automatycznie aktualizować pakiet uruchomiony na dowolnej maszynie wirtualnej w miarę tworzenia i wdrażania nowych wersji.

## <a name="component-overview"></a>Przegląd składnika

Menedżerów pakietów, takich jak [apt — get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , są dobrze znani w świecie systemu Linux, ale nie w świecie.
[Czekolada](https://chocolatey.org/) to taka rzecz, a [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) Scott Hanselman w temacie jest doskonałym wprowadzeniem. W Nutshell, czekolada pozwala na instalowanie pakietów z centralnego repozytorium pakietów w systemie Windows przy użyciu wiersza polecenia. Możesz utworzyć własne repozytorium i zarządzać nim, a czekolada może instalować pakiety z dowolnej liczby wyznaczonych repozytoriów.

Konfiguracja żądanego stanu (DSC) ([Omówienie](/powershell/scripting/dsc/overview/overview)) to narzędzie programu PowerShell, które pozwala na zadeklarować konfigurację dla maszyny. Na przykład możesz powiedzieć, "Chcę zainstalować czekoladę, chcę zainstalować usługi IIS, chcę otworzyć port 80 i chcę zainstalować wersję 1.0.0 mojej witryny sieci Web". Lokalna Configuration Manager DSC (LCM) implementuje tę konfigurację. Serwer ściągający DSC przechowuje repozytorium konfiguracji dla maszyn. LCM sprawdza okresowo, aby sprawdzić, czy jego konfiguracja jest zgodna z przechowywaną konfiguracją. Może zgłosić stan lub podjąć próbę przełączenia komputera w celu dostosowania go do przechowywanej konfiguracji. Można edytować przechowywaną konfigurację na serwerze ściągania, aby spowodować, że maszyna lub zestaw maszyn mają być wyrównane z zmienioną konfiguracją.

Azure Automation to usługa zarządzana w programie Microsoft Azure, która umożliwia automatyzację różnych zadań przy użyciu elementów Runbook, węzłów, poświadczeń, zasobów i aktywów, takich jak harmonogramy i zmienne globalne.
Azure Automation konfiguracja stanu rozszerza tę możliwość automatyzacji w celu uwzględnienia narzędzi programu PowerShell DSC. Oto doskonały [Przegląd](automation-dsc-overview.md).

Zasób DSC to moduł kodu, który ma określone możliwości, takie jak zarządzanie sieciami, Active Directory lub SQL Server. Zasób czekolady DSC wie, jak uzyskać dostęp do serwera NuGet (między innymi), pobrać pakiety, zainstalować pakiety i tak dalej. W [Galeria programu PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)istnieje wiele innych zasobów DSC.
Te moduły są instalowane na serwerze ściągania konfiguracji stanu Azure Automation (przez użytkownika), dzięki czemu mogą być używane przez konfiguracje.

Szablony Menedżer zasobów zapewniają deklaratywny sposób generowania infrastruktury, takich jak sieci, podsieci, zabezpieczenia sieci i routing, moduły równoważenia obciążenia, karty sieciowe, maszyny wirtualne i tak dalej. Poniżej znajduje się [artykuł](../azure-resource-manager/resource-manager-deployment-model.md) porównujący model wdrażania Menedżer zasobów (deklaracyjne) z modelem wdrażania usługi Azure Service Management (ASM lub klasycznym), a także omówiono podstawowych dostawców zasobów, obliczenia, magazyn i sieć.

Jedną z kluczowych funkcji szablonu Menedżer zasobów jest możliwość instalacji rozszerzenia maszyny wirtualnej na maszynie wirtualnej w ramach aprowizacji. Rozszerzenie maszyny wirtualnej ma określone funkcje, takie jak uruchamianie skryptu niestandardowego, instalowanie oprogramowania antywirusowego lub uruchamianie skryptu konfiguracji DSC. Istnieje wiele innych typów rozszerzeń maszyn wirtualnych.

## <a name="quick-trip-around-the-diagram"></a>Szybka podróż na diagramie

Zaczynając od góry, napiszesz kod, kompilujesz i testujesz, a następnie utworzysz pakiet instalacyjny.
Czekolada może obsługiwać różne typy pakietów instalacyjnych, takich jak MSI, MSU, ZIP. I masz pełną moc programu PowerShell, aby przeprowadzić rzeczywistą instalację, jeśli natywne możliwości Chocolateys nie są w pełni dostępne. Umieść pakiet w miejscu dostępnym — repozytorium pakietu. Ten przykład użycia używa folderu publicznego na koncie usługi Azure Blob Storage, ale może być dowolnym z dowolnego miejsca. Czekolada działa natywnie z serwerami NuGet i kilkoma innymi na potrzeby zarządzania metadanymi pakietu. W [tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. Ten przykład użycia korzysta z NuGet. Nuspec to metadane dotyczące pakietów. Nuspec są "kompilowane" w NuPkg i przechowywane na serwerze NuGet. Gdy konfiguracja żąda pakietu przy użyciu nazwy i odwołuje się do serwera NuGet, zasób z czekoladą DSC (teraz na maszynie wirtualnej) pobierze pakiet i zainstaluje go. Możesz również zażądać określonej wersji pakietu.

W lewej dolnej części obrazu istnieje szablon Azure Resource Manager. W tym przykładzie użycia rozszerzenie maszyny wirtualnej rejestruje maszynę wirtualną z serwerem ściągania konfiguracji stanu Azure Automation (to jest serwer ściągający) jako węzeł. Konfiguracja jest przechowywana na serwerze ściągania.
W rzeczywistości jest on przechowywany dwa razy: raz jako zwykły tekst i po skompilowaniu jako plik MOF (dla tych, które wiedzą o takich kwestiach). W portalu plik MOF jest "konfiguracją węzła" (a nie po prostu "konfiguracją"). Jest to artefakt skojarzony z węzłem, dzięki czemu węzeł będzie znać jego konfigurację. Szczegóły poniżej przedstawiają sposób przypisywania konfiguracji węzła do węzła.

Najprawdopodobniej już wykonujesz bit w górnej części lub większość z nich. Tworzenie nuspec, kompilowanie i przechowywanie ich na serwerze NuGet jest niewielkim elementem. I już zarządzasz maszynami wirtualnymi. W następnym kroku do ciągłego wdrażania wymagane jest skonfigurowanie serwera ściągania (raz), zarejestrowanie węzłów z nim (raz) i utworzenie i przechowywanie konfiguracji (początkowo). Następnie jako pakiety są uaktualniane i wdrażane w repozytorium, należy odświeżyć konfigurację i konfigurację węzła na serwerze ściągania (Powtarzaj w razie potrzeby).

Jeśli nie korzystasz z szablonu Menedżer zasobów, jest to również poprawne. Istnieją polecenia cmdlet programu PowerShell, które ułatwiają rejestrowanie maszyn wirtualnych na serwerze ściągania i wszystkich pozostałych. Aby uzyskać więcej informacji, zobacz ten artykuł: dołączanie [maszyn do zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1. Konfigurowanie serwera ściągania i konta usługi Automation

W wierszu polecenia programu PowerShell uwierzytelnionego (`Connect-AzureRmAccount`): (może upłynąć kilka minut, gdy serwer ściągania jest skonfigurowany)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Konto usługi Automation można umieścić w dowolnym z następujących regionów (w lokalizacji): Wschodnie stany USA 2, Południowo-środkowe stany USA, US Gov Wirginia, Europa Zachodnia, Azja Południowo-Wschodnia, Japonia Wschodnia, Indie Środkowe i Australia Południowo-Wschodnia, Kanada Środkowa i Europa Północna.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2. rozszerzenie maszyny wirtualnej dostraja do szablonu Menedżer zasobów

Szczegóły dotyczące rejestracji maszyny wirtualnej (przy użyciu rozszerzenia maszyny wirtualnej DSC programu PowerShell) dostępnego w tym [szablonie szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Ten krok rejestruje nową maszynę wirtualną na serwerze ściągania z listy węzłów konfiguracji stanu. Częścią tej rejestracji jest określenie konfiguracji węzła, która ma zostać zastosowana do węzła. Ta konfiguracja węzła nie musi jeszcze istnieć na serwerze ściągania, dlatego należy postępować zgodnie z krok 4. w tym celu należy to zrobić po raz pierwszy. Ale w kroku 2 należy podjąć decyzję o nazwie węzła i nazwie konfiguracji. W tym przykładzie użycia węzeł ma wartość "isvbox", a konfiguracja to "ISVBoxConfig". W związku z tym nazwa konfiguracji węzła (do określenia w DeploymentTemplate. JSON) to "ISVBoxConfig. isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3. Dodawanie wymaganych zasobów DSC do serwera ściągania

Galeria programu PowerShell jest Instrumentacją do instalowania zasobów DSC na koncie Azure Automation.
Przejdź do żądanego zasobu, a następnie kliknij przycisk "wdróż do Azure Automation".

![Przykład Galeria programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika ostatnio dodana do witryny Azure Portal umożliwia ściąganie nowych modułów lub aktualizowanie istniejących modułów. Kliknij kafelek zasób konta usługi Automation, elementy zawartości i na końcu kafelka moduły. Ikona Przeglądaj Galerię umożliwia wyświetlenie listy modułów w galerii, przechodzenie do szczegółów i ostateczne Importowanie do konta usługi Automation. Jest to świetny sposób, aby zapewnić aktualność swoich modułów od czasu do czasu. Ponadto funkcja import sprawdza zależności z innymi modułami, aby upewnić się, że nic nie jest zsynchronizowane.

Lub istnieje ręczne podejście. Struktura folderów modułu integracji programu PowerShell dla komputera z systemem Windows jest nieco inna niż struktura folderów oczekiwana przez Azure Automation.
Wymaga to nieco dostosowania Twojej części. Nie jest to jednak trudne i jest wykonywane tylko raz dla każdego zasobu (chyba że chcesz uaktualnić go w przyszłości). Aby uzyskać więcej informacji na temat tworzenia modułów integracji programu PowerShell, zobacz ten artykuł: [Tworzenie modułów integracji dla Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Zainstaluj moduł wymagany na stacji roboczej w następujący sposób:
  - Instalowanie programu [Windows Management Framework, V5](https://aka.ms/wmf5latest) (niewymagane w przypadku systemu Windows 10)
  - `Install-Module –Name MODULE-NAME` < — umożliwia pobranie modułu z Galeria programu PowerShell
- Skopiuj folder modułu z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do folderu tymczasowego
- Usuń przykłady i dokumentację z folderu głównego
- Zip folder główny, nazywanie pliku ZIP dokładnie tak samo jak folder 
- Umieść plik ZIP w dostępnej lokalizacji HTTP, takiej jak BLOB Storage na koncie usługi Azure Storage.
- Uruchom to środowisko PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

W zamieszczonym przykładzie wykonywane są następujące kroki dla cChoco i xNetworking. Zapoznaj się z [uwagami](#notes) dotyczącymi specjalnej obsługi cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4. Dodawanie konfiguracji węzła do serwera ściągania

Nie ma żadnych specjalnych informacji o tym, jak po raz pierwszy zaimportować konfigurację do serwera ściągania i skompilować. Wszystkie kolejne Importy/kompilacje tej samej konfiguracji wyglądają dokładnie tak samo. Za każdym razem, gdy aktualizujesz pakiet i konieczne jest wypchnięcie go do środowiska produkcyjnego, wykonaj ten krok po upewnieniu się, że plik konfiguracji jest prawidłowy — w tym nowej wersji pakietu. Oto plik konfiguracji i program PowerShell:

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

Te kroki powodują powstanie nowej konfiguracji węzła o nazwie "ISVBoxConfig. isvbox" umieszczanej na serwerze ściągania. Nazwa konfiguracji węzła jest skompilowana jako "ConfigurationName. nodename".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5. Tworzenie i konserwowanie metadanych pakietu

Dla każdego pakietu, który został umieszczony w repozytorium pakietu, potrzebne jest nuspec.
Ten nuspec musi zostać skompilowany i zapisany na serwerze NuGet. Ten proces jest opisany [tutaj](https://docs.nuget.org/create/creating-and-publishing-a-package). Możesz użyć MyGet.org jako serwera NuGet. Sprzedajeją tę usługę, ale mają bezpłatnie bezpłatną jednostkę SKU. W witrynie NuGet.org znajdziesz instrukcje dotyczące instalowania własnego serwera NuGet dla pakietów prywatnych.

## <a name="step-6-tying-it-all-together"></a>Krok 6. nawiązanie całego siebie

Za każdym razem, gdy wersja przekazuje funkcję kontroli jakości i jest zatwierdzona do wdrożenia, pakiet jest tworzony, nuspec i NUPKG aktualizowane i wdrażane na serwerze NuGet. Ponadto należy zaktualizować konfigurację (krok 4 powyżej), aby wyrazić zgodę na nowy numer wersji. Należy ją wysłać do serwera ściągania i skompilować.
Od tego momentu do maszyn wirtualnych, które są zależne od tej konfiguracji, można ściągnąć aktualizację i zainstalować ją. Każda z tych aktualizacji jest prosta — tylko wiersz lub dwa z programu PowerShell. W przypadku usługi Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które można połączyć ze sobą w kompilację. Ten [artykuł](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółów. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) zawiera szczegółowe informacje o różnych dostępnych zadaniach kompilacji.

## <a name="notes"></a>Uwagi

Ten przykład użycia rozpoczyna się od maszyny wirtualnej z ogólnego obrazu systemu Windows Server 2012 R2 z galerii platformy Azure. Możesz rozpocząć od dowolnego przechowywanego obrazu, a następnie dostosować je za pomocą konfiguracji DSC.
Jednak zmiana konfiguracji rozszerzania na obraz jest znacznie trudniejsza niż dynamiczne aktualizowanie konfiguracji przy użyciu DSC.

Nie musisz używać szablonu Menedżer zasobów i rozszerzenia maszyny wirtualnej, aby użyć tej techniki z maszynami wirtualnymi. A Twoje maszyny wirtualne nie muszą znajdować się na platformie Azure, aby można było zarządzać nimi. Jest to konieczne, aby zainstalować czekoladę i LCM skonfigurowany na maszynie wirtualnej, aby wiedział, gdzie jest serwer ściągania.

Oczywiście w przypadku aktualizowania pakietu na maszynie wirtualnej, która jest w środowisku produkcyjnym, należy przetworzyć maszynę wirtualną poza rotacją, podczas gdy aktualizacja jest zainstalowana. Jak to zrobić. Na przykład w przypadku maszyny wirtualnej za Azure Load Balancer można dodać sondę niestandardową. Podczas aktualizowania maszyny wirtualnej należy zwrócić punkt końcowy sondy o 400. Dostosowanie niezbędne do tego, że ta zmiana może znajdować się w konfiguracji, ponieważ może to spowodować przełączenie go w celu powrotu do 200 po zakończeniu aktualizacji.

Pełne źródło tego przykładu użycia znajduje się w [tym projekcie programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w witrynie GitHub.

## <a name="related-articles"></a>Powiązane artykuły
* [Azure Automation DSC — Omówienie](automation-dsc-overview.md)
* [Azure Automation polecenia cmdlet DSC](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Dołączanie maszyn w celu zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [Konfiguracja stanu Azure Automation](automation-dsc-overview.md)
- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
