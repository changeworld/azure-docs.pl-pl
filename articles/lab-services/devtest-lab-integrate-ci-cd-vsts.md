---
title: Integrowanie usługi Azure DevTest Labs z potokiem Azure potoków ciągłej integracji i dostarczania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować usługi Azure DevTest Labs w sieci Web Azure potoków ciągłej integracji i potoku dostaw
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7806599c1a2f1396ff4b07d6f0538057654029d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157128"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Integrowanie usługi Azure DevTest Labs usługi Azure DevOps potok ciągłej integracji i dostarczania
Możesz użyć *Azure DevTest Labs zadania* rozszerzenia, które jest instalowane w DevOps platformy Azure można łatwo zintegrować potokiem ciągłej integracji/ciągłego Dostarczania kompilacji i wydania przy użyciu usługi Azure DevTest Labs. Rozszerzenie instaluje trzy zadania: 
* Tworzenie maszyny wirtualnej
* Tworzenie niestandardowego obrazu maszyny wirtualnej
* Usuwanie maszyny wirtualnej 

Ten proces sprawia, że ułatwia, na przykład, szybkie wdrożenie "złotego obrazu" dla zadania określonego testu i usunąć go po zakończeniu testu.

Ten artykuł pokazuje, jak utworzyć i wdrożyć Maszynę wirtualną, utworzyć niestandardowy obraz, a następnie usuń maszynę Wirtualną, a wszystkie jako jeden, kompletny potok. Zazwyczaj przeprowadza się każde zadanie podrzędne indywidualnie w własnej niestandardowej kompilacji, testowania i wdrażania potoku.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed potokiem ciągłej integracji/ciągłego Dostarczania można zintegrować z usługą Azure DevTest Labs, należy zainstalować rozszerzenie programu Visual Studio Marketplace.
1. Przejdź do [usługi Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Wybierz pozycję **Zainstaluj**.
1. Ukończ pracę kreatora.

## <a name="create-a-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
W tej sekcji opisano sposób tworzenia szablonu usługi Azure Resource Manager, którego używasz do tworzenia maszyny wirtualnej platformy Azure na żądanie.

1. Aby utworzyć szablon usługi Resource Manager w ramach subskrypcji, wykonaj procedurę opisaną w [korzystanie z szablonu usługi Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Przed wygenerowaniem szablonu usługi Resource Manager, należy dodać [artefaktu WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako część tworzenia maszyny Wirtualnej.

   Dostępu WinRM jest wymagana do używania zadania wdrażania, takie jak *kopiowania plików na platformę Azure* i *programu PowerShell na komputerach docelowych*.

   > [!NOTE]
   > Gdy używasz usługi WinRM przy użyciu współużytkowanego adresu IP, należy dodać regułę translatora adresów Sieciowych w celu mapowania portu zewnętrznego port usługi WinRM. Ten krok nie jest wymagane, jeśli utworzysz maszynę Wirtualną z publicznym adresem IP.
   >
   >

1. Zapisz szablon jako plik na komputerze. Nadaj plikowi nazwę **CreateVMTemplate.json**.
1. Sprawdź szablon systemie kontroli źródła.
1. Otwórz Edytor tekstów, a następnie wklej poniższy skrypt.

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

1. Sprawdź skrypt systemie kontroli źródła. Nadaj mu nazwę podobną **GetLabVMParams.ps1**.

   Po uruchomieniu tego skryptu w agencie jako część potoku tworzenia wersji i korzystając z kroków zadań takich jak *kopiowania plików na platformę Azure* lub *programu PowerShell na komputerach docelowych*, skrypt służy do zbierania wartości, które trzeba Wdrażanie aplikacji na maszynie Wirtualnej. Zazwyczaj należy użyć tych zadań do wdrażania aplikacji na Maszynie wirtualnej platformy Azure. Zadania wymaga wartości, takie jak nazwa grupy zasobów maszyny Wirtualnej, adres IP i w pełni kwalifikowaną nazwę domeny (FQDN).

## <a name="create-a-release-pipeline-in-release-management"></a>Tworzenie potoku tworzenia wersji w programie Release Management
Aby utworzyć potok wydania, należy wykonać następujące czynności:

1. Na **wersji** karcie **kompilowanie i wydawanie** Centrum, wybierz przycisk ze znakiem plus (+).
1. W **Tworzenie definicji wydania** wybierz **pusty** szablonu, a następnie wybierz **dalej**.
1. Wybierz **Wybierz później**, a następnie wybierz pozycję **Utwórz** tworzenia nowego potoku wydania z jednym domyślnym środowisku i nie połączonych artefaktów.
1. Aby otworzyć menu skrótów w nowy potok wersji, wybierz wielokropek (...) obok nazwy środowiska, a następnie wybierz **skonfigurować zmienne**. 
1. W **Konfiguruj - środowiska** okno zmiennych, korzystających z zadań, potok wydania, wprowadź następujące wartości:

   a. Aby uzyskać **vmName**, wprowadź nazwę która jest przypisana do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w witrynie Azure portal.

   b. Aby uzyskać **userName**, wprowadź nazwę użytkownika, przypisana do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w witrynie Azure portal.

   c. Aby uzyskać **hasło**, wprowadź hasło, które są przypisane do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w witrynie Azure portal. Użyj ikony "kłódki", aby ukryć i bezpieczeństwo hasła.

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Następny etap wdrożenia to utworzenie maszyny Wirtualnej do użycia jako "złotego obrazu" w przypadku kolejnych wdrożeń. Możesz utworzyć maszynę Wirtualną w ramach swojego wystąpienia usługi Azure DevTest Lab przy użyciu zadania, które opracowano specjalnie do tego celu. 

1. W potoku wydań wybierz **dodawać zadania**.
1. Na **Wdróż** kartę, należy dodać *Utwórz maszynę Wirtualną programu Azure DevTest Labs* zadania. Skonfiguruj zadania w następujący sposób:

   > [!NOTE]
   > Aby utworzyć maszynę Wirtualną do użycia w przypadku kolejnych wdrożeń, zobacz [zadania usługi Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Dla **subskrypcji Menedżera zasobów platformy Azure**, wybierając pozycję Podłączanie w **dostępne połączenia usługi Azure Service** listy lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Aby uzyskać **Nazwa laboratorium**, wybierz nazwę wystąpienia, który został utworzony wcześniej.

   c. Aby uzyskać **Nazwa szablonu**, wprowadź pełną ścieżkę i nazwę pliku szablonu, który został zapisany do repozytorium kodu źródłowego. Wbudowane właściwości aplikacji Release Management umożliwia uproszczenie ścieżki, na przykład:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Aby uzyskać **parametry szablonu**, wprowadź parametry dla zmiennych, które są zdefiniowane w szablonie. Użyj nazwy zmiennych, które zostały zdefiniowane w środowisku, na przykład:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Dla **zmienne wyjściowe — identyfikator maszyny Wirtualnej w laboratorium**, potrzebny jest identyfikator nowo utworzonej maszyny wirtualnej do kolejnych kroków. Ustaw domyślną nazwę zmiennej środowiskowej, która jest automatycznie wypełniane przy użyciu tego Identyfikatora w **zmienne wyjściowe** sekcji. Możesz edytować zmiennej, jeśli to konieczne, ale pamiętaj, aby zawierał prawidłową nazwę w kolejnych zadaniach. Identyfikator maszyny Wirtualnej w laboratorium są zapisywane w następującej postaci:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. Uruchom skrypt został utworzony wcześniej, aby zbierać szczegółowe informacje o maszynie Wirtualnej DevTest Labs. 
1. W potoku wydań wybierz **dodawać zadania** a następnie na **Wdróż** kartę, należy dodać *programu Azure PowerShell* zadania. Skonfiguruj zadania w następujący sposób:

   > [!NOTE]
   > Aby zbierać szczegółowe informacje o maszynie Wirtualnej DevTest Labs, zobacz [wdrażania: Program Azure PowerShell](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3) i uruchom skrypt.

   a. Aby uzyskać **typu połączenia platformy Azure**, wybierz opcję **usługi Azure Resource Manager**.

   b. Aby uzyskać **subskrypcji Menedżera zasobów platformy Azure**, wybierz połączenie z listy w obszarze **dostępne połączenia usługi Azure Service**, lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   c. Aby uzyskać **typ skryptu**, wybierz opcję **pliku skryptu**.
 
   d. Aby uzyskać **ścieżka skryptu**, wprowadź pełną ścieżkę i nazwę skryptu, który został zapisany do repozytorium kodu źródłowego. Wbudowane właściwości aplikacji Release Management umożliwia uproszczenie ścieżki, na przykład:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Aby uzyskać **argumenty skryptu**, wprowadź nazwę zmiennej środowiskowej, która została automatycznie wypełniona identyfikator maszynę Wirtualną laboratorium przez poprzednie zadanie, na przykład: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skrypt umożliwia zbieranie informacji o wymaganych wartości i przechowuje je w zmiennych środowiskowych w potoku tworzenia wersji, tak aby użytkownik mógł łatwo znaleźć je w kolejnych krokach.

1. Wdrażanie aplikacji na nowej maszynie Wirtualnej laboratoria DevTest. Zadania zazwyczaj umożliwiają wdrożenie aplikacji to *kopiowania plików na platformę Azure* i *programu PowerShell na komputerach docelowych*.
   Informacje o maszynie Wirtualnej, należy dla parametrów tych zadań są przechowywane w trzech zmiennych konfiguracji o nazwie **labVmRgName**, **labVMIpAddress**, i **labVMFqdn**w potoku tworzenia wersji. Jeśli chcesz wypróbować tworzenie maszyny Wirtualnej usługi DevTest Labs i obraz niestandardowy bez wdrażanie aplikacji, możesz pominąć ten krok.

### <a name="create-an-image"></a>Tworzenie obrazu

Następny etap to aby utworzyć obraz maszyny wirtualnej w nowo wdrożonym w wystąpieniu usługi Azure DevTest Labs. Obrazu można następnie użyć do tworzenia kopii maszyny wirtualnej na żądanie, w każdym przypadku, gdy chcesz wykonać zadanie deweloperów lub niektóre testy. 

1. W potoku wydań wybierz **dodawać zadania**.
1. Na **Wdróż** kartę, należy dodać **utworzyć obraz niestandardowy usługi Azure DevTest Labs** zadania. Skonfiguruj w następujący sposób:

   > [!NOTE]
   > Aby utworzyć obraz, zobacz [zadania usługi Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Aby uzyskać **subskrypcji Menedżera zasobów platformy Azure**w **dostępne połączenia usługi Azure Service** listy, wybierz połączenie z listy lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Aby uzyskać **Nazwa laboratorium**, wybierz nazwę utworzone wcześniej wystąpienie.

   c. Aby uzyskać **niestandardową nazwę obrazu**, wprowadź nazwę dla niestandardowego obrazu.

   d. (Opcjonalnie) Aby uzyskać **opis**, podaj opis, aby ułatwić później wybrać prawidłowy obraz.

   e. Aby uzyskać **źródłowej maszyny Wirtualnej laboratorium — identyfikator maszyny Wirtualnej laboratorium źródła**, jeśli zmienisz domyślną nazwę zmiennej środowiskowej, która została automatycznie wypełniona o identyfikatorze maszynę Wirtualną laboratorium przez wcześniejsze zadanie tutaj edytować. Wartość domyślna to **$(labVMId)** .

   f. Aby uzyskać **zmienne wyjściowe — identyfikator obrazu niestandardowego**, potrzebny jest identyfikator nowo utworzonego obrazu zarządzać lub usuń go. Ustawiono domyślną nazwę zmiennej środowiskowej, która jest automatycznie wypełniane przy użyciu tego Identyfikatora **zmienne wyjściowe** sekcji. Zmienną można edytować, jeśli to konieczne.

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Końcowy etap polega na usunięciu maszyny Wirtualnej, która została wdrożona w wystąpieniu usługi Azure DevTest Labs. Zazwyczaj można usuwać maszyny Wirtualnej po wykonywanie zadań deweloperskich lub uruchomić testy, które należy wdrożonej maszyny Wirtualnej. 

1. W potoku wydań, wybierz **dodawać zadania** a następnie na **Wdróż** kartę, należy dodać *usuwania maszyny Wirtualnej programu platformy Azure DevTest Labs* zadania. Skonfiguruj w następujący sposób:

      > [!NOTE]
      > Aby usunąć maszynę Wirtualną, zobacz [Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Dla **subskrypcji Menedżera zasobów platformy Azure**, wybierając pozycję Podłączanie w **dostępne połączenia usługi Azure Service** listy lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).
 
   b. Aby uzyskać **identyfikator maszyny Wirtualnej w laboratorium**, jeśli zmienisz domyślną nazwę zmiennej środowiskowej, która została automatycznie wypełniona o identyfikatorze maszynę Wirtualną laboratorium przez wcześniejsze zadanie tutaj edytować. Wartość domyślna to **$(labVMId)** .

1. Wprowadź nazwę dla potoku tworzenia wersji, a następnie zapisz go.
1. Tworzenie nowej wersji, wybierz najnowszą kompilację i wdrożyć ją na jednym środowisku w potoku.

Na każdym etapie Odśwież widok wystąpienia usługi DevTest Labs w witrynie Azure portal, aby wyświetlić maszyny Wirtualnej i obrazów, które są tworzone i maszynę Wirtualną, która jest usuwana ponownie.

Można teraz używać obrazu niestandardowego do tworzenia maszyn wirtualnych, gdy są one wymagane.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [tworzenie środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
* Poznaj więcej szablonów szybkiego startu usługi Resource Manager, związane z automatyzacją DevTest Labs z [publicznego repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-quickstart-templates).
* Jeśli to konieczne, zobacz [Azure DevOps Rozwiązywanie problemów z](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) strony.
 
