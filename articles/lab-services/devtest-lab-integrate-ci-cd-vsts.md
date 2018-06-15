---
title: Integrowanie Azure DevTest Labs programu VSTS ciągłej integracji i dostarczania potoku | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować Azure DevTest Labs programu VSTS ciągłej integracji i dostarczania potoku
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
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787668"
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrowanie Azure DevTest Labs programu VSTS ciągłej integracji i dostarczania potoku
Można użyć *Azure DevTest Labs zadania* rozszerzenia zainstalowanego w Visual Studio Team Services (VSTS) można łatwo zintegrować planowaną kompilacji i wersji elementu konfiguracji/CD z Azure DevTest Labs. Rozszerzenie instaluje trzech zadań: 
* Tworzenie maszyny wirtualnej
* Tworzenie niestandardowego obrazu z maszyny Wirtualnej
* Usuwanie maszyny wirtualnej 

Proces ułatwia, na przykład szybko wdrożyć "złotego obrazu" dla zadania specyficznego testu i usunąć go po zakończeniu testu.

W tym artykule pokazano, jak utworzyć i wdrożyć maszynę Wirtualną, utworzyć niestandardowy obraz, a następnie usuń maszynę Wirtualną, a wszystkie jako jeden pełny potok. Zazwyczaj przeprowadza każdego zadania indywidualnie w potoku kompilacji — Testowanie wdrażania własnych niestandardowych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed planowaną CI/CD można zintegrować z Azure DevTest Labs, należy zainstalować rozszerzenie z Visual Studio Marketplace.
1. Przejdź do [Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Wybierz **zainstalować**.
1. Ukończ pracę kreatora.

## <a name="create-a-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
W tej sekcji opisano sposób tworzenia szablonu usługi Azure Resource Manager, który jest używany do utworzenia maszyny wirtualnej platformy Azure na żądanie.

1. Aby utworzyć szablon usługi Resource Manager w ramach subskrypcji, wykonaj procedurę w [użyć szablonu usługi Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Przed wygenerowaniem szablonu usługi Resource Manager Dodaj [artefaktu WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) podczas tworzenia maszyny Wirtualnej.

   Dostęp usługi WinRM jest wymagany do użycia zadania wdrażania, takie jak *Azure File Copy* i *programu PowerShell na komputerach docelowych*.

   > [!NOTE]
   > Gdy używasz usługi WinRM za pomocą udostępnionego adresu IP, należy dodać regułę NAT do mapowania portu zewnętrznego portu usługi WinRM. Ten krok nie jest wymagane, jeśli tworzenie maszyny Wirtualnej z publicznym adresem IP.
   >
   >

1. Zapisz szablon jako plik na komputerze. Nadaj nazwę plikowi **CreateVMTemplate.json**.
1. Sprawdź szablon systemie kontroli źródła.
1. Otwórz Edytor tekstu, a następnie wklej poniższy skrypt do niego.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Sprawdź skrypt systemie kontroli źródła. Nazwa przypominać **GetLabVMParams.ps1**.

   Po uruchomieniu tego skryptu po stronie agenta jako część definicji wersji, a jeśli używasz kroków zadań takich jak *Azure File Copy* lub *programu PowerShell na komputerach docelowych*, skrypt zbiera wartości, które należy Wdrażanie aplikacji na maszynie wirtualnej. Zazwyczaj użyje tych zadań do wdrażania aplikacji na maszynie Wirtualnej platformy Azure. Zadania wymagają wartości, takie jak nazwa grupy zasobów maszyny Wirtualnej, adres IP i nazwy FQDN (FDQN).

## <a name="create-a-release-definition-in-release-management"></a>Tworzenie definicji wersji w Release Management
Do tworzenia definicji wersji, wykonaj następujące czynności:

1. Na **wersje** karcie **kompilacji i wydania** Centrum, wybierz przycisk ze znakiem plus (+).
2. W **Tworzenie wersji definicji** wybierz **pusty** szablonu, a następnie wybierz **dalej**.
3. Wybierz **Wybierz później**, a następnie wybierz **Utwórz** do tworzenia nowej definicji wersji ze środowiskiem jeden domyślny i nie ma połączonego artefaktów.
4. Aby otworzyć menu skrótów, w nowej wersji definicji, wybierz wielokropek (...) obok nazwy środowiska, a następnie wybierz **skonfigurować zmienne**. 
5. W **Konfiguruj - środowiska** okno zmiennych, korzystających z wersji definicji zadań, wprowadź następujące wartości:

   a. Aby uzyskać **vmName**, wprowadź nazwę, która jest przypisana do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w portalu Azure.

   b. Aby uzyskać **userName**, wprowadź nazwę użytkownika, przypisana do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w portalu Azure.

   c. Aby uzyskać **hasło**, wprowadź hasło, które są przypisane do maszyny Wirtualnej, podczas tworzenia szablonu usługi Resource Manager w portalu Azure. Użyj ikony "kłódki", aby ukryć i bezpieczne hasło.

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Następny etap wdrażania jest aby utworzyć maszynę Wirtualną do użycia jako "złotego obrazu" kolejnych wdrożeń. Możesz utworzyć maszynę Wirtualną w wystąpieniu usługi Azure DevTest Lab za pomocą zadania opracowanym specjalnie do tego celu. 

1. W definicji wersji, wybierz **dodawać zadania**.
2. Na **Wdróż** , Dodaj *Utwórz maszynę Wirtualną programu Azure DevTest Labs* zadań. Skonfiguruj zadania w następujący sposób:

   > [!NOTE]
   > Aby utworzyć maszynę Wirtualną do użycia we wdrożeniach kolejne, zobacz [zadań Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Aby uzyskać **subskrypcji Menedżera zasobów Azure**, wybierz połączenie w **dostępne połączenia usługi Azure** listy lub Utwórz połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Aby uzyskać **Nazwa laboratorium**, wybierz nazwę wystąpienia, który został utworzony wcześniej.

   c. Aby uzyskać **nazwy szablonu**, wprowadź pełną ścieżkę i nazwę pliku szablonu, który został zapisany do repozytorium kodu źródłowego. Wbudowane właściwości zarządzania zleceniami służy do uproszczenia ścieżki, na przykład:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Aby uzyskać **parametrów szablonu**, wprowadź parametry dla zmiennych, które są zdefiniowane w szablonie. Użyj nazwy zmiennych zdefiniowanych w środowisku, na przykład:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Aby uzyskać **zmienne wyjściowe — identyfikator maszyny Wirtualnej laboratorium**, potrzebne są identyfikator nowo utworzony maszyny wirtualnej dla kolejnych krokach. Ustaw domyślną nazwę zmiennej środowiskowej, która jest automatycznie wypełniane przy użyciu tego Identyfikatora w **zmienne wyjściowe** sekcji. Można edytować zmiennej, jeśli to konieczne, ale pamiętaj, aby używać poprawnej nazwy w kolejnych zadań. Identyfikator maszyny Wirtualnej laboratorium są zapisywane w następującym formacie:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Uruchom skrypt został utworzony wcześniej, aby zbierać dane maszyny wirtualnej DevTest Labs. 
4. W definicji wersji, wybierz **dodawać zadania** , a następnie na **Wdróż** , Dodaj *programu Azure PowerShell* zadań. Skonfiguruj zadania w następujący sposób:

   > [!NOTE]
   > Aby zbierać dane maszyny wirtualnej DevTest Labs, zobacz [Wdróż: programu Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) i uruchom skrypt.

   a. Aby uzyskać **typu połączenia usługi Azure**, wybierz pozycję **usługi Azure Resource Manager**.

   b. Dla **subskrypcji Menedżera zasobów Azure**, wybierz połączenie z listy w obszarze **dostępne połączenia usługi Azure**, lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. Aby uzyskać **typ skryptu**, wybierz pozycję **pliku skryptu**.
 
   d. Aby uzyskać **ścieżka skryptu**, wprowadź pełną ścieżkę i nazwę skryptu, który został zapisany do repozytorium kodu źródłowego. Wbudowane właściwości zarządzania zleceniami służy do uproszczenia ścieżki, na przykład:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Aby uzyskać **argumenty skryptu**, wprowadź nazwę zmiennej środowiskowej, która została automatycznie wypełniane przy użyciu Identyfikator laboratorium maszyny Wirtualnej przez poprzednie zadanie, na przykład: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skrypt zbiera wymagane wartości i przechowuje je w zmiennych środowiskowych w ramach definicji wersji, dzięki czemu można łatwo udostępnić im w kolejnych krokach.

5. Wdrażanie aplikacji do nowej maszyny Wirtualnej DevTest Labs. Zadania zwykle używana do wdrażania aplikacji są *Azure File Copy* i *programu PowerShell na komputerach docelowych*.
   Informacje o maszynie Wirtualnej, należy dla parametrów tych zadań są przechowywane w trzech zmiennych konfiguracji o nazwie **labVmRgName**, **labVMIpAddress**, i **labVMFqdn**w ramach definicji wersji. Jeśli chcesz wypróbować tworzenia maszyny Wirtualnej DevTest Labs i obraz niestandardowy bez wdrażania aplikacji, możesz pominąć ten krok.

### <a name="create-an-image"></a>Tworzenie obrazu

Następny etap jest można utworzyć obrazu maszyny wirtualnej w nowo wdrożonym w usłudze Azure DevTest Labs wystąpienia. Obraz można następnie używać do tworzenia kopii maszyny Wirtualnej na żądanie, zawsze, gdy chcesz wykonać zadanie deweloperów lub uruchamianie niektórych testów. 

1. W definicji wersji, wybierz **dodawać zadania**.
2. Na **Wdróż** , Dodaj **utworzyć obraz niestandardowy Azure DevTest Labs** zadań. Skonfiguruj w następujący sposób:

   > [!NOTE]
   > Aby utworzyć obraz, zobacz [zadań Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Dla **subskrypcji Menedżera zasobów Azure**w **dostępne połączenia usługi Azure** listy, wybierz połączenie z listy lub Utwórz połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Aby uzyskać **Nazwa laboratorium**, wybierz nazwę wystąpienia utworzone wcześniej.

   c. Aby uzyskać **nazwy niestandardowego obrazu**, wprowadź nazwę dla niestandardowego obrazu.

   d. (Opcjonalnie) Aby uzyskać **opis**, podaj opis, który ułatwia później wybierz poprawny obraz.

   e. Dla **źródła laboratorium VM — identyfikator maszyny Wirtualnej laboratorium źródła**, jeśli zmienić domyślną nazwę zmiennej środowiskowej, która została automatycznie wypełniona o identyfikatorze laboratorium maszyny Wirtualnej przez wcześniejszych zadań go edytować w tym miejscu. Wartość domyślna to **$(labVMId)**.

   f. Aby uzyskać **zmienne wyjściowe — identyfikator obrazu niestandardowego**, należy identyfikator nowo utworzony obraz, jeśli chcesz zarządzać, lub usuń go. Ustawiono domyślną nazwę zmiennej środowiskowej, która jest automatycznie wypełniane przy użyciu tego Identyfikatora **zmienne wyjściowe** sekcji. W razie potrzeby można edytować zmiennej.

### <a name="delete-the-vm"></a>Usuń maszynę Wirtualną

Ostatnim krokiem jest, aby usunąć maszynę Wirtualną, która została wdrożona w wystąpieniu usługi Azure DevTest Labs. Zazwyczaj można usuwać maszyny Wirtualnej po wykonanie zadań deweloperów lub uruchom testy, które są potrzebne w przypadku wdrożonej maszyny Wirtualnej. 

1. W definicji wersji, wybierz **dodawać zadania** , a następnie na **Wdróż** , Dodaj *usunąć wirtualna Azure DevTest Labs* zadań. Skonfiguruj w następujący sposób:

      > [!NOTE]
      > Aby usunąć maszynę Wirtualną, zobacz [Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Aby uzyskać **subskrypcji Menedżera zasobów Azure**, wybierz połączenie w **dostępne połączenia usługi Azure** listy lub Utwórz połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. Dla **identyfikator maszyny Wirtualnej laboratorium**, jeśli zmienić domyślną nazwę zmiennej środowiskowej, która została automatycznie wypełniona o identyfikatorze laboratorium maszyny Wirtualnej przez wcześniejszych zadań go edytować w tym miejscu. Wartość domyślna to **$(labVMId)**.

2. Wprowadź nazwę definicji wersji, a następnie zapisz go.
3. Utworzyć nową wersję, wybierz ostatniej kompilacji, a następnie wdrożyć go na jednym środowiskiem w definicji.

Na każdym etapie Odśwież widok wystąpienia DevTest Labs w portalu Azure, aby wyświetlić maszyny Wirtualnej i obrazów, które są tworzone i maszynę Wirtualną, która jest usuwany ponownie.

Obraz niestandardowy można teraz używać do tworzenia maszyn wirtualnych, gdy są one wymagane.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [utworzyć środowiska wielu maszyn wirtualnych z szablonami usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
* Poznaj więcej szablonów usługi Resource Manager szybkiego startu DevTest Labs automatyzacji z [publicznego repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
* W razie potrzeby zajrzyj [VSTS Rozwiązywanie problemów z](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) strony.
 
