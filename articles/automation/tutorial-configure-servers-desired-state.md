---
title: Konfigurowanie wymaganego stanu serwerów i zarządzanie odejściem od tego stanu za pomocą usługi Azure Automation
description: Samouczek — zarządzanie konfiguracjami serwerów za pomocą konfiguracji stanu automatyzacji platformy Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416594"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurowanie serwerów do żądanego stanu i zarządzanie dryftem

Konfiguracja stanu automatyzacji usługi Azure umożliwia określenie konfiguracji dla serwerów i zapewnienie, że te serwery są w określonym stanie w czasie.

> [!div class="checklist"]
> - Wbudowana maszyna wirtualna, która ma być zarządzana przez usługę Azure Automation DSC
> - Przekazywanie konfiguracji do usługi Azure Automation
> - Kompilowanie konfiguracji do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdzanie stanu zgodności węzła zarządzanego

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna usługi Azure Resource Manager (nie klasyczna) z systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Moduł programu Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość konfiguracji żądanego stanu (DSC). Aby uzyskać informacje o dsc, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Tworzenie i przekazywanie konfiguracji do usługi Azure Automation

W tym samouczku użyjemy prostej konfiguracji DSC, która zapewnia, że usługi IIS są zainstalowane na maszynie Wirtualnej.

Aby uzyskać informacje o konfiguracjach DSC, zobacz [Konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

W edytorze tekstów wpisz poniższe i zapisz go lokalnie, jako `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> W bardziej zaawansowanych scenariuszach, w których wymagane jest importowanie wielu modułów, `Import-DscResource` które zapewniają zasoby DSC, upewnij się, że każdy moduł ma unikatową linię w konfiguracji.

Zadzwoń `Import-AzureRmAutomationDscConfiguration` do polecenia cmdlet, aby przekazać konfigurację do konta automatyzacji:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilowanie konfiguracji do konfiguracji węzła

Konfiguracja DSC musi zostać skompilowana do konfiguracji węzła, zanim będzie można ją przypisać do węzła.

Aby uzyskać informacje na temat kompilowania konfiguracji, zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

Wywołanie `Start-AzureRmAutomationDscCompilationJob` polecenia cmdlet `TestConfig` w celu skompilowania konfiguracji w konfiguracji węzła:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Spowoduje to utworzenie `TestConfig.WebServer` konfiguracji węzła o nazwie na koncie automatyzacji.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Rejestrowanie maszyny wirtualnej, która ma być zarządzana przez konfigurację stanu

Za pomocą konfiguracji stanu automatyzacji platformy Azure można zarządzać maszynami wirtualnymi platformy Azure (zarówno klasycznymi, jak i Menedżerem zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, maszynami wirtualnymi AWS i lokalnymi komputerami fizycznymi. W tym temacie opisano sposób rejestrowania tylko maszyn wirtualnych usługi Azure Resource Manager. Aby uzyskać informacje dotyczące rejestrowania innych typów maszyn, zobacz [Dołączanie maszyn do zarządzania przez konfigurację stanu automatyzacji platformy Azure.](automation-dsc-onboarding.md)

Wywołanie `Register-AzureRmAutomationDscNode` polecenia cmdlet, aby zarejestrować maszynę wirtualną w konfiguracji stanu automatyzacji platformy Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Rejestruje określoną maszynę wirtualną jako węzeł zarządzany w konfiguracji stanu.

### <a name="specify-configuration-mode-settings"></a>Określanie ustawień trybu konfiguracji

Podczas rejestrowania maszyny Wirtualnej jako węzła zarządzanego, można również określić właściwości konfiguracji. Na przykład można określić, że stan komputera ma być stosowany tylko raz (DSC nie próbuje zastosować konfiguracji `ApplyOnly` po wstępnym czeku), określając jako wartość **ConfigurationMode** właściwości:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić, jak często DSC sprawdza stan konfiguracji przy użyciu **ConfigurationModeFrequencyMins** właściwości:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Aby uzyskać więcej informacji na temat ustawiania właściwości konfiguracji dla węzła zarządzanego, zobacz [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy przypisać skompilowaną konfigurację węzła do maszyny Wirtualnej, którą chcemy skonfigurować.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Spowoduje to przypisanie `TestConfig.WebServer` konfiguracji węzła o `DscVm`nazwie do zarejestrowanego węzła DSC o nazwie .
Domyślnie węzeł DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut.
Aby uzyskać informacje dotyczące zmieniania interwału sprawdzania zgodności, zobacz [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Praca z konfiguracjami częściowymi

Konfiguracja stanu automatyzacji platformy Azure obsługuje użycie [konfiguracji częściowych.](/powershell/scripting/dsc/pull-server/partialconfigs)
W tym scenariuszu DSC jest skonfigurowany do zarządzania wieloma konfiguracjami niezależnie, a każda konfiguracja jest pobierana z usługi Azure Automation.
Jednak tylko jedna konfiguracja może być przypisana do węzła na konto automatyzacji.
Oznacza to, że jeśli używasz dwóch konfiguracji dla węzła, będziesz potrzebować dwóch kont automatyzacji.

Szczegółowe informacje na temat rejestrowania częściowej konfiguracji z usługi ściągania można znaleźć w dokumentacji [konfiguracji częściowej.](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

Aby uzyskać więcej informacji na temat współpracy zespołów w celu wspólnego zarządzania serwerami przy użyciu konfiguracji jako kodu, zobacz [Opis roli DSC w potoku ciągłej integracji/ciągłego wdrażania.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdzanie stanu zgodności węzła zarządzanego

Raporty dotyczące stanu zgodności węzła zarządzanego można `Get-AzureRmAutomationDscNodeReport` uzyskać, wywołując polecenie cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Usuwanie węzłów z usługi

Po dodaniu węzła do konfiguracji stanu automatyzacji usługi Azure, ustawienia w programie Local Configuration Manager są ustawione do rejestrowania się w usłudze i ściągania konfiguracji i wymaganych modułów do skonfigurowania komputera.
Jeśli zdecydujesz się usunąć węzeł z usługi, można to zrobić za pomocą witryny Azure portal lub polecenia cmdlet Az.

> [!NOTE]
> Wyrejestrowanie węzła z usługi ustawia tylko ustawienia lokalnego programu Menedżer konfiguracji, dzięki czemu węzeł nie łączy się już z usługą.
> Nie ma to wpływu na konfigurację, która jest aktualnie stosowana do węzła.
> Aby usunąć bieżącą konfigurację, użyj programu [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) lub usuń lokalny plik konfiguracyjny (jest to jedyna opcja dla węzłów systemu Linux).

### <a name="azure-portal"></a>Portal Azure

W usłudze Azure Automation kliknij **na konfigurację stanu (DSC)** w spisie treści.
Następnie kliknij **węzły,** aby wyświetlić listę węzłów, które są zarejestrowane w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku węzła, który zostanie otwarty, kliknij pozycję **Wyrejestruj**.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzeł z usługi Konfiguracji stanu automatyzacji usługi Azure Automation przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać wprowadzenie, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji usługi Azure](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączać węzły, zobacz [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md)
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [Polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Ceny konfiguracji stanu automatyzacji platformy Azure](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md)
