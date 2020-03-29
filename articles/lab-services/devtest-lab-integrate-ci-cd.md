---
title: Integracja laboratoriów azure devtest z potokami platformy Azure
description: Dowiedz się, jak zintegrować laboratoria DevTest Azure z ciągłą integracją i potokiem dostarczania potoku usługi Azure Pipelines
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293219"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integracja laboratoriów devtest platformy Azure z potokiem ciągłej integracji/dysku CD potoku potoku potoku ciągłej integracji/dysku CD potoku potoku potoku potoku ciągłego wdrażania/dysku CD potoku

Za pomocą rozszerzenia *zadania laboratoriów laboratoriów platformy Azure* można użyć do integracji potoków platformy Azure ciągłej integracji i ciągłego dostarczania (CI/CD) kompilacji i wydawania potoków z platformą Azure DevTest Labs. Rozszerzenie instaluje kilka zadań, w tym: 

- Tworzenie maszyny wirtualnej
- Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej
- Usuwanie maszyny wirtualnej 

Te zadania ułatwiają, na przykład, szybko wdrożyć maszynę wirtualną *złotego obrazu* dla określonego zadania testowego, a następnie usunąć maszynę wirtualną po zakończeniu testu.

W tym artykule pokazano, jak używać zadań laboratorium programu Azure DevTest Labs do tworzenia i wdrażania maszyny Wirtualnej, tworzenia obrazu niestandardowego, a następnie usuwania maszyny Wirtualnej, a wszystko to w ramach jednego potoku wydania. Zwykle można wykonać zadania indywidualnie w własnej kompilacji niestandardowej, testowania i wdrażania potoków.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zarejestruj się lub zaloguj się do swojej organizacji [DevOps platformy Azure](https://dev.azure.com) i [utwórz projekt](/vsts/organizations/projects/create-project) w organizacji. 
  
- Zainstaluj rozszerzenie zadań laboratoriów programu Azure DevTest Labs z witryny Visual Studio Marketplace.
  
  1. Przejdź do [witryny Zadania laboratoriów devtest platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Wybierz **pozycję Pobierz za darmo**.
  1. Wybierz swoją organizację Programu Azure DevOps z listy rozwijanej i wybierz pozycję **Zainstaluj**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Tworzenie szablonu do tworzenia maszyny Wirtualnej platformy Azure 

W tej sekcji opisano sposób tworzenia szablonu usługi Azure Resource Manager, który służy do tworzenia maszyny Wirtualnej platformy Azure na żądanie.

1. Aby utworzyć szablon Menedżera zasobów w ramach subskrypcji, wykonaj procedurę opisaną w aplikacji [Użyj szablonu Menedżera zasobów](devtest-lab-use-resource-manager-template.md).
   
1. Przed wygenerowaniem szablonu Menedżera zasobów dodaj [artefakt WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako część tworzenia maszyny wirtualnej. Zadania *wdrażania, takie* jak Azure File Copy i *PowerShell na komputerach docelowych,* wymagają dostępu do usługi WinRM. Artefakt WinRM wymaga nazwy hosta jako parametru, który powinien być w pełni kwalifikowaną nazwą domeny (FQDN) maszyny Wirtualnej. 
   
   > [!NOTE]
   > Korzystając z usługi WinRM z udostępnionym adresem IP, należy dodać regułę TRANSLATORA adresów sieciowych w celu mapowania portu zewnętrznego do portu WinRM. Nie potrzebujesz reguły NAT, jeśli tworzysz maszynę wirtualną z publicznym adresem IP.
   
   
1. Zapisz szablon na komputerze jako plik o nazwie *CreateVMTemplate.json*.
   
1. Zaewidencjonuj szablon w systemie kontroli źródła.

## <a name="create-a-script-to-get-vm-properties"></a>Tworzenie skryptu w celu uzyskania właściwości maszyny Wirtualnej

Po uruchomieniu kroki zadania, takie jak *Kopia pliku platformy Azure* lub Programu *PowerShell na komputerach docelowych* w potoku wydania, poniższy skrypt zbiera wartości, które należy wdrożyć aplikację na maszynie wirtualnej. Zwykle można użyć tych zadań do wdrożenia aplikacji na maszynie wirtualnej platformy Azure. Zadania wymagają wartości, takich jak nazwa grupy zasobów maszyny Wirtualnej, adres IP i nazwa FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby utworzyć plik skryptu:

1. Otwórz edytor tekstu i wklej do niego następujący skrypt.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Zapisz plik o nazwie takiej jak *GetLabVMParams.ps1*i zaewidencjonuj go w systemie kontroli źródła. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Tworzenie potoku wydania w usłudze Azure Pipelines

Aby utworzyć nowy potok wydania:

1. Na stronie projektu programu Azure DevOps wybierz pozycję **Pipelines** > **Releases** z lewej strony nawigacji.
1. Wybierz pozycję **Nowy potok**.
1. W obszarze **Wybierz szablon**przewiń w dół i wybierz pozycję **Puste zadanie**, a następnie wybierz pozycję **Zastosuj**.

### <a name="add-and-set-variables"></a>Dodawanie i ustawianie zmiennych

Zadania potoku używają wartości przypisanych do maszyny Wirtualnej podczas tworzenia szablonu Menedżera zasobów w witrynie Azure portal. 

Aby dodać zmienne dla wartości: 

1. Na stronie potoku wybierz kartę **Zmienne.**
   
1. Dla każdej zmiennej wybierz **dodaj** i wprowadź nazwę i wartość:
   
   |Nazwa|Wartość|
   |---|---|
   |*Nazwa wirtualna*|Nazwa maszyny Wirtualnej przypisana w szablonie Menedżera zasobów|
   |*Nazwę użytkownika*|Nazwa użytkownika, aby uzyskać dostęp do maszyny Wirtualnej|
   |*hasło*|Hasło do nazwy użytkownika. Wybierz ikonę kłódki, aby ukryć i zabezpieczyć hasło.

### <a name="create-a-devtest-labs-vm"></a>Tworzenie maszyny Wirtualnej Laboratorium DevTest

Następnym krokiem jest utworzenie maszyny Wirtualnej złoty obraz do użycia w przyszłych wdrożeniach. Maszynę wirtualną można utworzyć w wystąpieniu laboratoriów usługi Azure DevTest przy użyciu zadania *Azure DevTest Labs Create VM.*

1. Na karcie **Potok** potoku wydania zaznacz tekst hiperłącza w **etapie 1,** aby **+** **wyświetlić zadania stołu montażowego,** a następnie wybierz znak plus obok zadania **agenta**. 
   
1. W obszarze **Dodawanie zadań**wybierz pozycję **Azure DevTest Labs Create VM**i wybierz pozycję **Dodaj**. 
   
1. Wybierz **pozycję Utwórz maszynę wirtualną w laboratoriach azure devtest labs** w lewym okienku. 

1. W okienku po prawej stronie wypełnij formularz w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Subskrypcja usługi Azure RM**|Wybierz połączenie usługi lub subskrypcję z **dostępnych połączeń usługi Azure** lub dostępnych subskrypcji platformy **Azure** w rozwijaniu i wybierz **pozycję Autoryzuj** w razie potrzeby.<br /><br />**Uwaga:** Aby uzyskać informacje dotyczące tworzenia bardziej ograniczonego połączenia uprawnień z subskrypcją platformy Azure, zobacz [punkt końcowy usługi Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Nazwa laboratorium**|Wybierz nazwę istniejącego laboratorium, w którym zostanie utworzona maszyna wirtualna laboratorium.|
   |**Nazwa szablonu**|Wprowadź pełną ścieżkę i nazwę pliku szablonu zapisanego w repozytorium kodu źródłowego. Można użyć wbudowanych właściwości, aby uprościć ścieżkę, na przykład:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parametry szablonu**|Wprowadź parametry dla wcześniej zdefiniowanych zmiennych:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Identyfikator** > **maszyny Wirtualnej** Laboratorium zmiennych wyjściowych|Wprowadź zmienną dla utworzonego identyfikatora maszyny Wirtualnej w laboratorium. Jeśli używasz domyślnego **labVMId**, można odwołać się do zmiennej w kolejnych zadaniach jako *$(labVMId)*.<br /><br />Można utworzyć nazwę inną niż domyślna, ale należy pamiętać o używaniu poprawnej nazwy w kolejnych zadaniach. Identyfikator maszyny Wirtualnej laboratorium można zapisać w następującej formie:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Zbieranie informacji o maszynie wirtualnej DevTest Labs

Wykonaj skrypt utworzony wcześniej, aby zebrać szczegóły DevTest Labs VM. 

1. Na karcie **Potok** potoku wydania zaznacz tekst hiperłącza w **etapie 1,** aby **+** **wyświetlić zadania stołu montażowego,** a następnie wybierz znak plus obok zadania **agenta**. 
   
1. W obszarze **Dodawanie zadań**wybierz pozycję **Azure PowerShell**i wybierz pozycję **Dodaj**. 
   
1. Wybierz **skrypt programu Azure PowerShell: Ścieżka pliku** w lewym okienku. 
   
1. W okienku po prawej stronie wypełnij formularz w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Typ połączenia platformy Azure**|Wybierz **usługę Azure Resource Manager**.|
   |**Subskrypcja platformy Azure**|Wybierz połączenie usługi lub subskrypcję.| 
   |**Typ skryptu**|Wybierz **ścieżkę pliku skryptu**.|
   |**Ścieżka skryptu**|Wprowadź pełną ścieżkę i nazwę skryptu programu PowerShell zapisanego w repozytorium kodu źródłowego. Można użyć wbudowanych właściwości, aby uprościć ścieżkę, na przykład:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumenty skryptu**|Wprowadź nazwę zmiennej *labVmId,* która została wypełniona przez poprzednie zadanie, na przykład:<br /><br />`-labVmId '$(labVMId)'`|

Skrypt zbiera wymagane wartości i przechowuje je w zmiennych środowiskowych w potoku wydania, dzięki czemu można łatwo odwoływać się do nich w kolejnych krokach.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Tworzenie obrazu maszyny Wirtualnej z maszyny Wirtualnej DevTest Labs

Następnym zadaniem jest utworzenie obrazu nowo wdrożonej maszyny Wirtualnej w wystąpieniu usługi Azure DevTest Labs. Następnie można użyć obrazu do tworzenia kopii maszyny Wirtualnej na żądanie, gdy chcesz wykonać zadanie dewelopera lub uruchomić niektóre testy. 

1. Na karcie **Potok** potoku wydania zaznacz tekst hiperłącza w **etapie 1,** aby **+** **wyświetlić zadania stołu montażowego,** a następnie wybierz znak plus obok zadania **agenta**. 
   
1. W obszarze **Dodawanie zadań**wybierz pozycję **Azure DevTest Labs Create Custom Image**i select **Add**. 
   
1. Skonfiguruj zadanie w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Subskrypcja usługi Azure RM**|Wybierz połączenie usługi lub subskrypcję.|
   |**Nazwa laboratorium**|Wybierz nazwę istniejącego laboratorium, w którym obraz zostanie utworzony.|
   |**Niestandardowa nazwa obrazu**|Wprowadź nazwę obrazu niestandardowego.|
   |**Opis** (opcjonalnie)|Wprowadź opis, aby ułatwić późniejsze wybranie prawidłowego obrazu.|
   |**Source Lab VM** > **Source Lab VM ID**|Jeśli zmieniono domyślną nazwę zmiennej LabVMId, wprowadź ją tutaj. Wartość domyślna to **$(labVMId).**|
   |**Niestandardowy** > identyfikator obrazu zmiennych**wyjściowych**|W razie potrzeby można edytować domyślną nazwę zmiennej.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Wdrażanie aplikacji na maszynie wirtualnej DevTest Labs (opcjonalnie)

Można dodać zadania, aby wdrożyć aplikację do nowej maszyny Wirtualnej DevTest Labs. Zadania, których zwykle używasz do wdrożenia aplikacji, to *Azure File Copy* i *PowerShell na komputerach docelowych.*

Informacje o maszynie wirtualnej potrzebne dla parametrów tych zadań są przechowywane w trzech zmiennych konfiguracyjnych o nazwie **labVmRgName**, **labVMIpAddress**i **labVMFqdn** w potoku wydania. Jeśli chcesz eksperymentować tylko z tworzeniem devtest labs maszyny Wirtualnej i niestandardowego obrazu, bez wdrażania aplikacji do niego, można pominąć ten krok.

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Ostatnim zadaniem jest usunięcie maszyny Wirtualnej, która została wdrożona w wystąpieniu usługi Azure DevTest Labs. Zazwyczaj można usunąć maszynę wirtualną po wykonaniu zadań deweloperskich lub uruchomić testy, które są potrzebne na wdrożonej maszynie wirtualnej. 

1. Na karcie **Potok** potoku wydania zaznacz tekst hiperłącza w **etapie 1,** aby **+** **wyświetlić zadania stołu montażowego,** a następnie wybierz znak plus obok zadania **agenta**. 
   
1. W obszarze **Dodawanie zadań**wybierz pozycję **Azure DevTest Labs Delete VM**i wybierz pozycję **Dodaj**. 
   
1. Skonfiguruj zadanie w następujący sposób:
   
   - W obszarze **Subskrypcja usługi Azure RM**wybierz połączenie lub subskrypcję usługi. 
   - W przypadku **identyfikatora maszyny Wirtualnej laboratorium,** jeśli zmieniono domyślną nazwę zmiennej LabVMId, wprowadź ją tutaj. Wartość domyślna to **$(labVMId).**
   
### <a name="save-the-release-pipeline"></a>Zapisywanie potoku wydania

Aby zapisać nowy potok wydania:

1. Wybierz nazwę **Nowy potok wydania** na stronie potoku wydania i wprowadź nową nazwę potoku. 
   
1. Wybierz ikonę **Zapisz** w prawym górnym rogu. 

## <a name="create-and-run-a-release"></a>Tworzenie i uruchamianie wersji

Aby utworzyć i uruchomić wersję przy użyciu nowego potoku:

1. Wybierz **pozycję Utwórz zwolnienie w** prawym górnym rogu na stronie potoku wydania. 
   
1. W obszarze **Artefakty**wybierz najnowszą kompilację, a następnie wybierz pozycję **Utwórz**.
   
1. Na każdym etapie wydania odśwież widok wystąpienia Laboratoriów Testów Deweloperskich w witrynie Azure Portal, aby wyświetlić tworzenie maszyny Wirtualnej, tworzenie obrazu i usuwanie maszyn wirtualnych.

Obraz niestandardowy służy do tworzenia maszyn wirtualnych w dowolnym momencie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [tworzyć środowiska wielu maszyn wirtualnych za pomocą szablonów Menedżera zasobów](devtest-lab-create-environment-from-arm.md).
- Zapoznaj się z bardziej szybkimi szablonami Menedżera zasobów dla automatyzacji DevTest Labs z [publicznego repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- W razie potrzeby zobacz stronę rozwiązywania problemów z [programem Azure DevOps.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
