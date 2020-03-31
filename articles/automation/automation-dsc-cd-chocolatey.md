---
title: Ciągłe wdrażanie konfiguracji stanu automatyzacji platformy Azure z czekoladą
description: Ciągłe wdrażanie devops przy użyciu konfiguracji stanu automatyzacji platformy Azure, DSC i Chocolatey menedżera pakietów.  Przykład z pełnym szablonem Menedżera zasobów JSON i źródłem programu PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966741"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Przykład użycia: Ciągłe wdrażanie maszyn wirtualnych przy użyciu konfiguracji stanu automatyzacji i czekoladowej

W świecie DevOps istnieje wiele narzędzi, aby pomóc w różnych punktach w potoku ciągłej integracji. Konfiguracja stanu automatyzacji platformy Azure to nowy, powitalnym dodatkiem do opcji, które mogą stosować zespoły DevOps. W tym artykule przedstawiono konfigurowanie ciągłego wdrażania (CD) dla komputera z systemem Windows. Technikę można łatwo rozszerzyć, aby uwzględnić dowolną liczbę komputerów z systemem Windows w roli (na przykład witrynę sieci Web), a stamtąd również o dodatkowe role.

![Ciągłe wdrażanie maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie

Dzieje się tu sporo, ale na szczęście można go podzielić na dwa główne procesy:

- Pisanie kodu i testowanie go, a następnie tworzenie i publikowanie pakietów instalacyjnych dla głównych i pomocniczych wersji systemu.
- Tworzenie maszyn wirtualnych i zarządzanie nimi, które zainstalują i wykonają kod w pakietach.  

Po obu tych podstawowych procesów są w miejscu, jest to krótki krok, aby automatycznie zaktualizować pakiet uruchomiony na dowolnej określonej maszynie Wirtualnej, jak nowe wersje są tworzone i wdrażane.

## <a name="component-overview"></a>Omówienie komponentów

Menedżery pakietów, takie jak [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) są dość dobrze znane w świecie Linuksa, ale nie tak bardzo w świecie systemu Windows.
[Chocolatey](https://chocolatey.org/) jest takie coś, a [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) Scott Hanselman na ten temat jest wielkim intro. W skrócie, Chocolatey pozwala na zainstalowanie pakietów z centralnego repozytorium pakietów do systemu Windows za pomocą wiersza polecenia. Możesz tworzyć własne repozytorium i zarządzać nim, a Chocolatey może instalować pakiety z dowolnej liczby wyznaczonych repozytoriów.

Konfiguracja żądanego stanu (DSC) ([przegląd](/powershell/scripting/dsc/overview/overview)) to narzędzie programu PowerShell, które umożliwia zadeklarowanie żądanej konfiguracji dla komputera. Na przykład, można powiedzieć: "Chcę Chocolatey zainstalowany, chcę IIS zainstalowany, chcę port 80 otwarty, chcę wersję 1.0.0 mojej stronie zainstalowanej." Dsc local configuration manager (LCM) implementuje tę konfigurację. Serwer ściągania DSC przechowuje repozytorium konfiguracji dla komputerów. LCM na każdym komputerze sprawdza się okresowo, aby sprawdzić, czy jego konfiguracja jest zgodna z zapisaną konfiguracją. Można zgłosić stan lub spróbować doprowadzić komputer z powrotem do wyrównania z przechowywanej konfiguracji. Można edytować zapisaną konfigurację na serwerze ściągania, aby spowodować wyrównanie komputera lub zestawu maszyn ze zmienioną konfiguracją.

Usługa Azure Automation to usługa zarządzana na platformie Microsoft Azure, która umożliwia automatyzację różnych zadań przy użyciu śmięty, węzłów, poświadczeń, zasobów i zasobów, takich jak harmonogramy i zmienne globalne.
Konfiguracja stanu automatyzacji platformy Azure rozszerza tę funkcję automatyzacji o narzędzia DSC programu PowerShell. Oto świetny [przegląd](automation-dsc-overview.md).

Zasób DSC to moduł kodu, który ma określone możliwości, takie jak zarządzanie siecią, usługą Active Directory lub programem SQL Server. Chocolatey DSC Resource wie, jak uzyskać dostęp do serwera NuGet (między innymi), pobrać pakiety, zainstalować pakiety i tak dalej. W [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)znajduje się wiele innych zasobów DSC.
Moduły te są instalowane w serwerze ściągania konfiguracji usługi Azure Automation State (przez Ciebie), dzięki czemu mogą być używane przez konfiguracje.

Szablony Menedżera zasobów zapewniają deklaratywny sposób generowania infrastruktury — takie rzeczy jak sieci, podsieci, zabezpieczenia sieci i routing, moduły równoważenia obciążenia, karty sieciowe, maszyny wirtualne i tak dalej. Oto [artykuł,](../azure-resource-manager/management/deployment-models.md) który porównuje model wdrażania Menedżera zasobów (deklaratywny) z modelem wdrażania usługi Azure Service Management (ASM lub klasyczny) (imperatywem) i omawia dostawców podstawowych zasobów, obliczeń, magazynu i sieci.

Jedną z kluczowych cech szablonu Menedżera zasobów jest jego możliwość zainstalowania rozszerzenia maszyny Wirtualnej na maszynie Wirtualnej w miarę jego obsługi. Rozszerzenie maszyny Wirtualnej ma określone możliwości, takie jak uruchamianie skryptu niestandardowego, instalowanie oprogramowania antywirusowego lub uruchamianie skryptu konfiguracji DSC. Istnieje wiele innych typów rozszerzeń maszyn wirtualnych.

## <a name="quick-trip-around-the-diagram"></a>Szybka podróż po diagramie

Począwszy od góry, można napisać kod, kompilacji i testowania, a następnie utworzyć pakiet instalacyjny.
Chocolatey może obsługiwać różnego rodzaju pakiety instalacyjne, takie jak MSI, MSU, ZIP. I masz pełną moc PowerShell do rzeczywistej instalacji, jeśli Chocolateys rodzimych możliwości nie są całkiem do niego. Umieść paczkę w jakimś miejscu osiągalnym – repozytorium pakietów. W tym przykładzie użycia użyto folderu publicznego na koncie magazynu obiektów blob platformy Azure, ale może on znajdować się w dowolnym miejscu. Chocolatey działa natywnie z serwerami NuGet i kilka innych do zarządzania metadanych pakietu. [W tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. W tym przykładzie użycia użytek NuGet. Nuspec to metadane dotyczące pakietów. Nuspec są "skompilowane" do NuPkg i przechowywane na serwerze NuGet. Gdy konfiguracja żąda pakietu według nazwy i odwołuje się do serwera NuGet, Chocolatey DSC Resource (teraz na maszynie Wirtualnej) pobiera pakiet i instaluje go dla Ciebie. Można również zażądać określonej wersji pakietu.

W lewej dolnej części obrazu znajduje się szablon usługi Azure Resource Manager. W tym przykładzie użycia rozszerzenie maszyny Wirtualnej rejestruje maszynę wirtualną z serwerem ściągania konfiguracji stanu automatyzacji platformy Azure (czyli serwerem ściągania) jako węzeł. Konfiguracja jest przechowywana na serwerze ściągania.
W rzeczywistości jest przechowywany dwa razy: raz jako zwykły tekst i raz skompilowany jako plik MOF (dla tych, którzy wiedzą o takich rzeczach.) W portalu MOF jest "konfiguracja węzła" (w przeciwieństwie do po prostu "konfiguracja"). Jest to artefakt, który jest skojarzony z węzłem, więc węzeł będzie znał jego konfigurację. Szczegóły poniżej pokazują, jak przypisać konfigurację węzła do węzła.

Przypuszczalnie już robisz trochę na górze, lub większość z nich. Tworzenie nuspec, kompilowanie i przechowywanie go na serwerze NuGet jest małą rzeczą. A ty już zarządzasz maszynami wirtualnymi. Następnym krokiem do ciągłego wdrażania wymaga skonfigurowania serwera ściągania (raz), rejestrowania węzłów z nim (raz) i tworzenia i przechowywania konfiguracji tam (początkowo). Następnie, gdy pakiety są uaktualniane i wdrażane do repozytorium, odśwież konfigurację i konfigurację węzła na serwerze ściągania (powtórz w razie potrzeby).

Jeśli nie zaczynasz od szablonu Menedżera zasobów, jest to również ok. Istnieją polecenia cmdlet programu PowerShell zaprojektowane w celu rejestrowania maszyn wirtualnych za pomocą serwera ściągania i całej pozostałej części. Aby uzyskać więcej informacji, zobacz ten artykuł: [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Konfigurowanie serwera ściągania i konta automatyzacji

Przy uwierzytelnionym`Connect-AzureRmAccount`( ) wiersz polecenia programu PowerShell: (może potrwać kilka minut, podczas gdy serwer ściągania jest skonfigurowany)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Możesz umieścić swoje konto automatyzacji w dowolnym z następujących regionów (aka lokalizacja): East US 2, South Central US, US Gov Virginia, Europa Zachodnia, Azja Południowo-Wschodnia, Japonia Wschodnia, Indie Środkowe i Australia Południowo-Wschodnia, Kanada Środkowa, Europa Północna.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Poprawki rozszerzenia maszyny wirtualnej do szablonu Menedżera zasobów

Szczegółowe informacje dotyczące rejestracji maszyn wirtualnych (przy użyciu rozszerzenia maszyny Wirtualnej DSC programu PowerShell) podane w tym [szablonie szybkiego startu platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Ten krok rejestruje nową maszynę wirtualną z serwerem ściągania na liście węzłów konfiguracji stanu. Częścią tej rejestracji jest określenie konfiguracji węzła, który ma zostać zastosowany do węzła. Ta konfiguracja węzła nie musi jeszcze istnieć na serwerze ściągania, więc jest ok, że krok 4 jest, gdzie odbywa się to po raz pierwszy. Ale tutaj w kroku 2 musisz zdecydować nazwę węzła i nazwę konfiguracji. W tym przykładzie użycia węzeł jest "isvbox", a konfiguracja jest "ISVBoxConfig". Tak więc nazwa konfiguracji węzła (do określenia w DeploymentTemplate.json) to 'ISVBoxConfig.isvbox'.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Dodawanie wymaganych zasobów DSC do serwera ściągania

Galeria programu PowerShell jest instrumentowana do instalowania zasobów DSC na koncie usługi Azure Automation.
Przejdź do odpowiedniego zasobu i kliknij przycisk "Wdrażanie w usłudze Azure Automation".

![Przykład galerii programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika niedawno dodana do witryny Azure Portal umożliwia ściąganie nowych modułów lub aktualizowanie istniejących modułów. Kliknij zasób Konto automatyzacji, kafelek Zasoby i na koniec kafelek Moduły. Ikona Galeria przeglądania umożliwia wyświetlenie listy modułów w galerii, szczegółowe informacje i ostateczne zaimportowanie do konta automatyzacji. Jest to świetny sposób, aby od czasu do czasu aktualizować moduły. Funkcja importu sprawdza zależności z innymi modułami, aby upewnić się, że nic nie zostanie zsynchronizowane.

Istnieje też podejście ręczne. Struktura folderów modułu integracji programu PowerShell dla komputera z systemem Windows różni się nieco od struktury folderów oczekiwanej przez usługę Azure Automation.
Wymaga to trochę szczypanie z Twojej strony. Ale to nie jest trudne, i to zrobić tylko raz na zasób (chyba że chcesz uaktualnić go w przyszłości.) Aby uzyskać więcej informacji na temat tworzenia modułów integracji programu PowerShell, zobacz ten artykuł: [Tworzenie modułów integracji dla usługi Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Zainstaluj potrzebny moduł na stacji roboczej w następujący sposób:
  - Instalowanie [programu Windows Management Framework w wersji 5](https://aka.ms/wmf5latest) (nie jest wymagane dla systemu Windows 10)
  - `Install-Module –Name MODULE-NAME`< — pobiera moduł z galerii programu PowerShell
- Kopiowanie folderu `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` modułu z folderu tymczasowego
- Usuwanie próbek i dokumentacji z folderu głównego
- Skompresuj folder główny, nazywając plik ZIP dokładnie tak samo jak folder 
- Umieść plik ZIP w lokalizacji dostępnej HTTP, takiej jak magazyn obiektów blob na koncie usługi Azure Storage.
- Uruchom ten program PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

W uwzględniony przykład wykonuje te kroki dla cChoco i xNetworking. Zobacz [uwagi](#notes) do specjalnej obsługi dla cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Dodawanie konfiguracji węzła do serwera ściągania

Nie ma nic specjalnego w pierwszym zaimportowaniu konfiguracji do serwera ściągania i kompilacji. Wszystkie kolejne import/kompilacje tej samej konfiguracji wyglądają dokładnie tak samo. Za każdym razem, gdy aktualizujesz pakiet i musisz wypchnąć go do produkcji, wykonaj ten krok po upewnieniu się, że plik konfiguracyjny jest poprawny — w tym nowa wersja pakietu. Oto plik konfiguracyjny i program PowerShell:

Plik ISVBoxConfig.ps1:

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

Te kroki powodują, że nowa konfiguracja węzła o nazwie "ISVBoxConfig.isvbox" jest umieszczana na serwerze ściągania. Nazwa konfiguracji węzła jest zbudowana jako "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: Tworzenie i obsługa metadanych pakietu

Dla każdego pakietu, który można umieścić w repozytorium pakietów, należy nuspec, który opisuje go.
Ten nuspec musi być skompilowany i przechowywany na serwerze NuGet. Proces ten opisano [tutaj](https://docs.nuget.org/create/creating-and-publishing-a-package). MyGet.org można używać jako serwera NuGet. Sprzedają tę usługę, ale mają starter SKU, który jest bezpłatny. W NuGet.org znajdziesz instrukcje dotyczące instalowania własnego serwera NuGet dla pakietów prywatnych.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Wiązanie wszystkiego razem

Za każdym razem, gdy wersja przechodzi qa i jest zatwierdzony do wdrożenia, pakiet jest tworzony, nuspec i nupkg aktualizowane i wdrażane na serwerze NuGet. Ponadto konfiguracja (krok 4 powyżej) musi zostać zaktualizowana, aby uzgodnić nowy numer wersji. Musi być wysłany do serwera ściągania i skompilowany.
Od tego momentu to do maszyn wirtualnych, które zależą od tej konfiguracji, aby wyciągnąć aktualizację i zainstalować go. Każda z tych aktualizacji są proste — tylko wiersz lub dwa programu PowerShell. W przypadku usługi Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które mogą być połączone w połączeniu w kompilacji. Ten [artykuł](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółów. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) szczegółowo różnych dostępnych zadań kompilacji.

## <a name="notes"></a>Uwagi

W tym przykładzie użycia rozpoczyna się maszyna wirtualna z ogólnego obrazu systemu Windows Server 2012 R2 z galerii platformy Azure. Możesz zacząć od dowolnego zapisanego obrazu, a następnie dostosować stamtąd z konfiguracją DSC.
Jednak zmiana konfiguracji, która jest pieczona w obrazie jest znacznie trudniejsze niż dynamicznie aktualizowanie konfiguracji przy użyciu DSC.

Nie trzeba używać szablonu Menedżera zasobów i rozszerzenia maszyny Wirtualnej, aby użyć tej techniki z maszynami wirtualnymi. A maszyny wirtualne nie muszą być na platformie Azure, aby być pod zarządzaniem dyskami CD. Wszystko, co jest konieczne jest to, że Chocolatey być zainstalowane i LCM skonfigurowany na maszynie Wirtualnej, więc wie, gdzie jest serwer ściągania.

Oczywiście po zaktualizowaniu pakietu na maszynie Wirtualnej, która jest w produkcji, należy wyjąć tę maszynę wirtualną z rotacji, gdy aktualizacja jest zainstalowana. Sposób, w jaki to robisz, jest bardzo zróżnicowany. Na przykład za maszynę wirtualną za moduł równoważenia obciążenia platformy Azure, można dodać sondy niestandardowej. Podczas aktualizowania maszyny Wirtualnej, mają punkt końcowy sondy zwraca 400. Tweak niezbędne do spowodowania tej zmiany może być wewnątrz konfiguracji, jak można dostosować, aby przełączyć go z powrotem do powrotu 200 po zakończeniu aktualizacji.

Pełne źródło dla tego przykładu użycia znajduje się w [tym projekcie programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w usłudze GitHub.

## <a name="related-articles"></a>Powiązane artykuły
* [Omówienie usługi Azure Automation DSC](automation-dsc-overview.md)
* [Polecenia cmdlet usługi Azure Automation DSC](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Maszyny dołączające do zarządzania przez usługę Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Konfiguracja stanu automatyzacji platformy Azure](automation-dsc-overview.md)
- Aby uzyskać wprowadzenie, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji usługi Azure](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md)
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [Polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Ceny konfiguracji stanu automatyzacji platformy Azure](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md)
