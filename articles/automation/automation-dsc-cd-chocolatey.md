---
title: Ciągłe wdrażanie usługi Konfiguracja DSC usługi Automatyzacja Azure z Chocolatey
description: DevOps ciągłe wdrażanie przy użyciu usługi Konfiguracja DSC automatyzacji Azure i Menedżer pakietów Chocolatey.  Przykład pełny szablon JSON ARM i źródła programu PowerShell.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f695eaadc0aa2d01473262c478a3b184d89d882c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195271"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Przykład użycia: Ciągłe wdrażanie maszyn wirtualnych przy użyciu usługi Konfiguracja DSC automatyzacji i Chocolatey
W świecie DevOps istnieje wiele narzędzi do pomagać w różnych punktach w potoku ciągłej integracji.  Konfiguracji usługi Azure Automation pożądanej stanu (DSC) to powitalnej oprócz nowe opcje, które zespoły opracowywania oprogramowania można wdrożyć.  W tym artykule przedstawiono ustawienia zapasowej ciągłego wdrażania (CD) na komputerze z systemem Windows.  Metoda zawierać dowolną liczbę komputerów z systemem Windows zgodnie z potrzebami w roli (na przykład witryną sieci web), a następnie do również dodatkowych ról można z łatwością rozszerzyć.

![Ciągłe wdrażanie dla maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie
Jest dość bit przejściem tutaj, ale na szczęście można można podzielić na dwie główne procesy: 

* Pisanie kodu i testowanie go, następnie tworzenia i publikowania pakietów instalacyjnych wersję główną i pomocniczą systemu. 
* Tworzenie i zarządzanie nimi maszyn wirtualnych, które zainstaluje i wykonywania kodu w pakietach.  

Po obu tych procesów core znajdują się w miejscu, jest krótki krok do automatycznego aktualizowania pakietu uruchomionych na żadnej określonej maszyny Wirtualnej jak nowe wersje są tworzone i wdrażane.

## <a name="component-overview"></a>Informacje o składniku
Takich jak pakiet menedżerów [stanie get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) są bardzo dobrze znane w świecie systemów Linux, ale nie tak wiele na świecie systemu Windows.  [Chocolatey](https://chocolatey.org/) jest takie operacją i Scott Hanselman [blogu](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) tematu jest doskonałym wprowadzenie.  Mówiąc Chocolatey umożliwia zainstalowanie pakietów z centralnym repozytorium pakietów w systemie Windows przy użyciu wiersza polecenia.  Można tworzyć i zarządzać własną repozytorium i Chocolatey mogą instalować pakiety z dowolnej liczby repozytoria, które określisz.

Żądany stan konfiguracji (DSC) ([omówienie](https://technet.microsoft.com/library/dn249912.aspx)) jest narzędziem programu PowerShell, które służy do deklarowania konfiguracji, które mają dla maszyny.  Na przykład można powiedzieć "Chcę Chocolatey zainstalowany, I ma zainstalowane usługi IIS, chcę, aby otworzyć port 80, ma wersję 1.0.0 witryny sieci Web zainstalowany."  Menedżer konfiguracji lokalnego (LCM) DSC implementuje tę konfigurację. Serwerem ściągania usługi Konfiguracja DSC przechowuje repozytorium konfiguracji maszyn. LCM na każdej maszynie, sprawdza okresowo jeśli jego konfiguracja odpowiada przechowywanej konfiguracji. Jego zgłoszenia stanu lub próba przywrócenia komputera do dostosowania przechowywanej konfiguracji. Można edytować konfiguracji przechowywanej na serwerze ściągania spowodować maszyny lub zestaw maszyn do dostosowania zmiany konfiguracji.

Automatyzacja Azure jest usługą zarządzanej platformie Microsoft Azure, która pozwala zautomatyzować różne zadania przy użyciu elementów runbook, węzłów, poświadczenia, zasobów i zasoby, takie jak harmonogramów i zmiennych globalnych. Konfiguracja DSC automatyzacji Azure rozszerza tej możliwości automatyzacji, aby dołączyć narzędzia do konfiguracji DSC środowiska PowerShell.  W tym miejscu jest doskonałym [omówienie](automation-dsc-overview.md).

Zasób DSC jest modułu kodu, który ma określonych funkcji, takich jak zarządzanie sieci, w usłudze Active Directory lub programu SQL Server.  Chocolatey zasobów DSC wie, jak uzyskać dostęp do serwera NuGet (między innymi), pakiety pobrać, zainstalować pakiety i tak dalej.  Istnieje wiele innych zasobów usługi Konfiguracja DSC w [galerii programu PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Te moduły są zainstalowane na serwerze ściągania usługi Konfiguracja DSC automatyzacji Azure (przez użytkownika) dzięki mogą być używane w konfiguracji.

Szablony Menedżera zasobów zapewnić deklaratywne generowania infrastruktury - np. sieci, podsieci, zabezpieczeń sieci i routingu, obciążenia równoważenia, karty sieciowe, maszyn wirtualnych i tak dalej.  Oto [artykułu](../azure-resource-manager/resource-manager-deployment-model.md) który porównuje modelu wdrażania usługi Resource Manager (deklaratywne) z usługą Azure Service Management (ASM lub classic) wdrożenia modelu (imperatywne) i w tym artykule omówiono podstawowe dostawców zasobów, obliczeń, magazynu i sieci.

Jedną z kluczowych funkcji szablonu usługi Resource Manager jest możliwość zainstalowania rozszerzenia maszyny Wirtualnej w Maszynie wirtualnej, zgodnie z jego obsługa została zainicjowana.  Rozszerzenia maszyny Wirtualnej ma określone funkcje takie jak uruchamianie skryptu niestandardowego, instalowanie oprogramowania antywirusowego lub uruchomienie skryptu konfiguracji DSC.  Istnieje wiele typów rozszerzeń maszyny Wirtualnej.

## <a name="quick-trip-around-the-diagram"></a>Szybkie podróży wokół diagramu
Począwszy od góry, wpisz swój kod, kompilacji i testowania, a następnie utworzyć pakiet instalacji.  Chocolatey może obsługiwać różne typy pakietów instalacyjnych, takich jak ZIP MSI, MSU.  I masz pełną moc PowerShell czy rzeczywista instalacja Chocolatey w macierzystym możliwości nie są dość poprzedzającym go.  Pakiet należy umieścić w innym dostępny — z repozytorium pakietów.  W tym przykładzie użycie używane folderu publicznego w ramach konta magazynu obiektów blob platformy Azure, ale może być dowolnym miejscu.  Chocolatey natywnie współpracuje z serwerów NuGet i kilka innych metadane pakietów zarządzania.  [W tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje.  W tym przykładzie użycie używane NuGet.  Plik Nuspec jest metadane dotyczące pakietów.  Nuspec "kompilowania", do jego NuPkg i zapisane na serwerze NuGet.  Podczas konfiguracji żądania pakietu według nazwy i odwołuje się do serwera NuGet, Chocolatey zasobów DSC (teraz na Maszynie wirtualnej) grabs pakietu i instaluje je automatycznie.  Możesz również poprosić o określonej wersji pakietu.

W lewej dolnej części obrazu ma szablonu usługi Azure Resource Manager (ARM).  W tym przykładzie użycie rozszerzenia maszyny Wirtualnej rejestruje maszyny Wirtualnej z serwera ściągania usługi Konfiguracja DSC automatyzacji Azure (czyli serwera ściągania) jako węzeł.  Konfiguracja jest przechowywana na serwerze ściągania.  W rzeczywistości jest ona przechowywana dwa razy: raz w postaci zwykłego tekstu i po skompilowany jako plik MOF (dla tych, które wiedzieć o takich elementów.)  W portalu MOF jest "konfiguracja węzła" (zamiast po prostu "Konfiguracja").  Jest artefaktu, który został skojarzony z węzłem, więc węzeł będzie wiadomo, jego konfiguracji.  Poniżej pokazano, jak można przypisać konfiguracji węzła do węzła.

Prawdopodobnie już robimy bit u góry lub większość z nich.  Tworzenie plik nuspec, kompilowania i przechowywanie ich w serwerze NuGet jest mała operacją.  I będziesz już zarządzać maszynami wirtualnymi.  Przejściem do następnego kroku do ciągłego wdrażania wymaga konfigurowania (raz) z serwerem ściągania, zarejestrowani węzły (raz), tworzenie i przechowywanie konfiguracji istnieje (początkowo).  Następnie pakiety są uaktualnione i wdrażane do repozytorium Odśwież konfiguracji i konfiguracja węzła na serwerze ściągania (Powtórz w razie potrzeby).

Jeśli nie jest uruchamiany z szablonu usługi ARM, który również jest OK.  Istnieją polecenia cmdlet programu PowerShell ułatwiających rejestrowania maszyn wirtualnych z serwera ściągania i wszystkie pozostałe. Aby uzyskać więcej informacji, zobacz ten artykuł: [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Konfigurowanie konta serwera i automatyzacji ściągania
Uwierzytelniony wiersza polecenia programu PowerShell (Connect-AzureRmAccount): (może potrwać kilka minut, gdy skonfigurowano z serwerem ściągania)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Możesz też zaznaczyć Twoje konto usługi Automatyzacja, do żadnego z następujących regionach (alias lokalizacja): wschodnie stany USA 2, południowo-środkowe stany Virginia nam wersji dla instytucji rządowych, Europa Zachodnia, Azja południowo-wschodnia, Japonia Wschodnia, Indie środkowe i Australia Południowo-Wschodnia, Kanada centralnej, Europa Północna, Europa.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Krok 2: Maszyny Wirtualnej rozszerzeniu ulepszeń do szablonu ARM
Szczegóły rejestracji maszyny Wirtualnej (przy użyciu rozszerzenia maszyny Wirtualnej DSC środowiska PowerShell) podane w tym [szablonie Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Ten krok rejestruje nowej maszyny Wirtualnej z serwera ściągania na liście węzłów DSC.  Część tej rejestracji jest określenie konfiguracji węzła, który ma zostać zastosowany do węzła.  Ta konfiguracja węzła nie ma jeszcze istnieje na serwerze ściągania, tak aby zawierała czy kroku 4 jest, gdzie odbywa się po raz pierwszy.  Jednak w tym miejscu w kroku 2 musisz zdecydował nazwy węzła i nazwa konfiguracji.  W tym przykładzie użycia węzła jest "isvbox" i konfiguracja jest "ISVBoxConfig".  Dlatego nazwa konfiguracji węzła (określonych w DeploymentTemplate.json) jest "ISVBoxConfig.isvbox".  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Dodawanie wymaganych zasobów DSC na serwerze ściągania
Aby zainstalować DSC zasobów na koncie usługi Automatyzacja Azure został zinstrumentowany na galerii programu PowerShell.  Przejdź do zasobu i kliknij przycisk "Wdrażanie do usługi Automatyzacja Azure".

![Przykład galerii programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Innej techniki ostatnio dodany do portalu Azure umożliwia ściągnięcia w nowych modułów lub zaktualizować istniejący modułów. Kliknij zasób konta automatyzacji, kafelka zasoby, a na końcu kafelka modułów.  Ikona galerii przeglądania umożliwia wyświetlić listę modułów w galerii, przejść do szczegółów i ostatecznie zaimportować na koncie automatyzacji. Jest to dobry sposób na aktualizowanie moduły od czasu do czasu. I zależności z innymi modułami, aby upewnić się, że nic nie jest niezsynchronizowana sprawdza importu funkcji.

Lub jest metoda ręczna.  Struktura folderów modułu integracji programu PowerShell na komputerze z systemem Windows jest nieco inny niż oczekiwany przez automatyzacji Azure struktury folderów.  Wymaga to niewielkiej je ze strony użytkownika.  Ale nie jest trudne i wykonywane tylko raz dla zasobów (chyba że chcesz uaktualnić go w przyszłości.)  Aby uzyskać więcej informacji dotyczących tworzenia moduły integracji programu PowerShell, zobacz ten artykuł: [tworzenia moduły integracji dla usługi Automatyzacja Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Zainstaluj moduł, który należy na stacji roboczej w następujący sposób:
  * Zainstaluj [Windows Management Framework v5](http://aka.ms/wmf5latest) (nie wymagane dla systemu Windows 10)
  * `Install-Module –Name MODULE-NAME`    < — grabs modułu z galerii programu PowerShell 
* Skopiuj folder modułu z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do folderu tymczasowego 
* Usuń z folderu głównego przykłady i dokumentacja 
* ZIP głównego folderu nazewnictwa pliku ZIP, dokładnie tak samo jak folder 
* Umieść plik ZIP do dostępny lokalizacji HTTP, takie jak magazyn obiektów blob na koncie magazynu Azure.
* Uruchom to środowiska PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

Przykład dołączone wykonuje te kroki dla cChoco i xNetworking. Zobacz [uwagi](#notes) dla specjalnej obsługi dla cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Dodawanie konfiguracji węzła na serwerze ściągania
Nie ma nic specjalne dotyczące importowania konfiguracji na serwerze ściągania i kompilacji po raz pierwszy.  Wszystkie kolejne import/kompiluje o tej samej konfiguracji dokładnie wyglądają tak samo.  Zawsze aktualizacji pakietu i trzeba kopiować go do produkcji, należy wykonać ten krok, po upewnieniu się, że plik konfiguracji jest prawidłowy — łącznie z nowej wersji pakietu.  Poniżej przedstawiono plik konfiguracji i programu PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

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

Wynik te czynności w nowej konfiguracji węzła o nazwie "ISVBoxConfig.isvbox" na serwerze ściągania.  Nazwa konfiguracji węzła jest tworzone w postaci "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: Tworzenie i obsługę metadane pakietów
Dla każdego pakietu, który można umieścić w repozytorium pakietów należy nuspec, zawierającego jego opis.  Ten plik nuspec musi być skompilowany i przechowywane na serwerze NuGet. Ten proces jest opisany [tutaj](http://docs.nuget.org/create/creating-and-publishing-a-package).  MyGet.org służy jako serwer NuGet.  One sprzedaży tej usługi, ale ma początkowego jednostka SKU, które jest aktualnie wolne.  W NuGet.org znajdują się instrukcje dotyczące instalowania NuGet serwera dla pakietów prywatnych.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Łącząc go wszystkie razem
Za każdym razem wersję przekazuje odpowiedzi na pytania i została zatwierdzona wdrożenia, zostaje utworzony pakiet, nuspec i nupkg zaktualizowany i wdrożone na serwerze NuGet.  Ponadto należy zaktualizować konfiguracji (krok 4 powyżej), aby uzgodnić z nowego numeru wersji.  Musi być wysyłane do serwera ściągania i skompilowany.  Od tego momentu jest maszyn wirtualnych, które są zależne od tej konfiguracji do pobierania aktualizacji, a następnie zainstaluj go.  Wszystkie te aktualizacje są proste — tylko wiersz lub dwóch programu PowerShell.  W przypadku programu Visual Studio Team Services niektóre z nich są hermetyzowane w zadania kompilacji, które można połączyć ze sobą w kompilacji.  To [artykułu](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółowych informacji.  To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) szczegóły różne zadania dostępne kompilacji.

## <a name="notes"></a>Uwagi
W tym przykładzie użycie rozpoczyna się od maszyny Wirtualnej z ogólny obraz systemu Windows Server 2012 R2 z poziomu galerii Azure.  Możesz rozpocząć od żadnego obrazu przechowywanych, a następnie dostosować stamtąd pomocą konfiguracji DSC.  Jednak zmiany konfiguracji, który jest rozszerzania obrazu jest trudniejsze niż dynamiczne aktualizowanie konfiguracji przy użyciu usługi Konfiguracja DSC.

Nie trzeba użyć tej metody z maszyn wirtualnych za pomocą szablonu usługi ARM i rozszerzenia maszyny Wirtualnej.  I maszyny wirtualne nie muszą być na platformie Azure jako zarządzana dysku CD.  Niezbędne jest zainstalowanie Chocolatey i LCM skonfigurowane na maszynie Wirtualnej będzie wówczas traktował, gdzie jest serwerem ściągania.  

Oczywiście po zaktualizowaniu pakietu na maszynie Wirtualnej, który znajduje się w środowisku produkcyjnym, należy wykonać tej maszyny Wirtualnej poza obrotu podczas instalowania aktualizacji.  Jak to zrobić różni.  Na przykład z maszyną Wirtualną za usługą równoważenia obciążenia Azure, możesz dodać, niestandardowe sondowania.  Podczas aktualizowania maszyny Wirtualnej, należy mieć punktu końcowego sondowania zwracać 400.  Dostrajał należy spowodować, że ta zmiana może być wewnątrz konfiguracji, jak dostrajał, aby przełączyć się do zwracania 200 po ukończeniu aktualizacji.

Pełne źródło w tym przykładzie użycie znajduje się w [tego projektu programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w witrynie GitHub.

## <a name="related-articles"></a>Pokrewne artykuły
* [Przegląd usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-overview.md)
* [Polecenia cmdlet systemu Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
* [Dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md)

