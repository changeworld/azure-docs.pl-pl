---
title: Konfigurowanie wymaganego stanu serwerów i zarządzanie odejściem od tego stanu za pomocą usługi Azure Automation
description: Samouczek — Zarządzanie konfiguracjami serwera z konfiguracją stanu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 0d877dafc4ab4f8ec4edb0a94450fa9c5dfcd0bb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850242"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurowanie żądanego stanu serwerów i zarządzanie dryfem

Azure Automation konfiguracja stanu pozwala określić konfiguracje dla serwerów i upewnić się, że te serwery są w określonym stanie z upływem czasu.

> [!div class="checklist"]
> - Dołączanie maszyny wirtualnej, która ma być zarządzana przez Azure Automation DSC
> - Przekaż konfigurację do Azure Automation
> - Kompiluj konfigurację do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdź stan zgodności zarządzanego węzła

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna w Azure Resource Manager (nieklasyczny) z systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell module w wersji 3,6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość konfiguracji żądanego stanu (DSC). Informacje o konfiguracji DSC można znaleźć w temacie [Omówienie żądanego stanu programu Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview) .

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Utwórz i przekaż konfigurację do Azure Automation

W tym samouczku zostanie użyta prosta Konfiguracja DSC, która zapewnia, że usługi IIS są zainstalowane na maszynie wirtualnej.

Aby uzyskać informacje o konfiguracjach DSC, zobacz [Konfiguracje DSC](/powershell/dsc/configurations).

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
> W bardziej zaawansowanych scenariuszach, w których wymagane jest zaimportowanie wielu modułów, które udostępniają zasoby DSC, upewnij się, `Import-DscResource` że każdy moduł ma unikatowy wiersz w konfiguracji.

Wywołaj `Import-AzureRmAutomationDscConfiguration` polecenie cmdlet, aby przekazać konfigurację do konta usługi Automation:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompiluj konfigurację do konfiguracji węzła

Konfiguracja DSC musi zostać skompilowana do konfiguracji węzła, aby można było ją przypisać do węzła.

Informacje o kompilowaniu konfiguracji można znaleźć w temacie [konfiguracje DSC](/powershell/dsc/configurations).

Wywołaj `Start-AzureRmAutomationDscCompilationJob` polecenie cmdlet, aby `TestConfig` skompilować konfigurację do konfiguracji węzła:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Spowoduje to utworzenie konfiguracji węzła o `TestConfig.WebServer` nazwie na koncie usługi Automation.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Rejestrowanie maszyny wirtualnej tak, aby była zarządzana przez konfigurację stanu

Konfiguracja stanu Azure Automation służy do zarządzania maszynami wirtualnymi platformy Azure (klasycznymi i Menedżer zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, AWS maszynami wirtualnymi i lokalnymi maszynami fizycznymi. W tym temacie omówiono sposób rejestrowania tylko Azure Resource Manager maszyn wirtualnych. Aby uzyskać informacje na temat rejestrowania innych typów maszyn, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

Wywołaj `Register-AzureRmAutomationDscNode` polecenie cmdlet, aby zarejestrować maszynę wirtualną z konfiguracją stanu Azure Automation.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Spowoduje to zarejestrowanie określonej maszyny wirtualnej jako węzła zarządzanego w konfiguracji stanu.

### <a name="specify-configuration-mode-settings"></a>Określ ustawienia trybu konfiguracji

Po zarejestrowaniu maszyny wirtualnej jako węzła zarządzanego można także określić właściwości konfiguracji. Można na przykład określić, że stan komputera ma być stosowany tylko raz (Konfiguracja DSC nie próbuje zastosować konfiguracji po wstępnym sprawdzeniu), określając `ApplyOnly` jako wartość właściwości **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić częstotliwość sprawdzania stanu konfiguracji przez DSC przy użyciu właściwości **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Aby uzyskać więcej informacji na temat ustawiania właściwości konfiguracji dla zarządzanego węzła, zobacz [register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz [Konfigurowanie lokalnego Configuration Manager](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy przypisać konfigurację skompilowanego węzła do maszyny wirtualnej, którą chcemy skonfigurować.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Spowoduje to przypisanie konfiguracji węzła o `TestConfig.WebServer` nazwie do zarejestrowanego węzła `DscVm`DSC o nazwie.
Domyślnie węzeł DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut.
Informacje o sposobie zmiany interwału sprawdzania zgodności znajdują się w temacie [konfigurowanie Configuration Manager lokalnego](/PowerShell/DSC/metaConfig).

## <a name="working-with-partial-configurations"></a>Praca z konfiguracjami częściowymi

Azure Automation konfiguracja stanu obsługuje użycie [konfiguracji częściowych](/powershell/dsc/pull-server/partialconfigs).
W tym scenariuszu Konfiguracja DSC jest konfigurowana w taki sposób, aby zarządzać wieloma konfiguracjami niezależnie, a każda z nich jest pobierana z Azure Automation.
Tylko jedną konfigurację można jednak przypisać do węzła na konto usługi Automation.
Oznacza to, że w przypadku korzystania z dwóch konfiguracji dla węzła wymagane są dwa konta usługi Automation.

Aby uzyskać szczegółowe informacje na temat rejestrowania częściowej konfiguracji z usługi ściągania, zapoznaj się z dokumentacją [częściową konfiguracje](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Aby uzyskać więcej informacji o tym, jak zespoły mogą współdziałać, aby wspólnie zarządzać serwerami przy użyciu konfiguracji jako kodu, zobacz [Opis roli DSC w potoku](/powershell/dsc/overview/authoringadvanced)ciągłej integracji/ciągłego wdrażania.

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdź stan zgodności zarządzanego węzła

Możesz uzyskać raporty dotyczące stanu zgodności zarządzanego węzła, wywołując `Get-AzureRmAutomationDscNodeReport` polecenie cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Usuwanie węzłów z usługi

Po dodaniu węzła do Azure Automation konfiguracji stanu ustawienia w lokalnych Configuration Manager są skonfigurowane do rejestracji w ramach usługi i konfiguracji ściągania oraz wymaganych modułów w celu skonfigurowania maszyny.
Jeśli zdecydujesz się usunąć węzeł z usługi, możesz to zrobić przy użyciu Azure Portal lub polecenia AZ cmdlets.

> [!NOTE]
> Wyrejestrowanie węzła z usługi ustawia tylko ustawienia Configuration Manager lokalnego, aby węzeł nie był już połączony z usługą.
> Nie wpływa to na konfigurację, która jest aktualnie stosowana do węzła.
> Aby usunąć bieżącą konfigurację, użyj programu [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) lub Usuń lokalny plik konfiguracji (jest to jedyna opcja dla węzłów systemu Linux).

### <a name="azure-portal"></a>Azure Portal

W obszarze Azure Automation kliknij pozycję **Konfiguracja stanu (DSC)** w spisie treści.
Kliknij pozycję **węzły** , aby wyświetlić listę węzłów, które są zarejestrowane w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku węzła, który zostanie otwarty,kliknij pozycję Wyrejestruj.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzeł z usługi konfiguracji stanu Azure Automation przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączyć węzły, zobacz sekcję dołączanie [maszyn w celu zarządzania według konfiguracji stanu Azure Automation](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
