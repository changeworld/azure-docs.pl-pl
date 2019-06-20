---
title: Konfigurowanie wymaganego stanu serwerów i zarządzanie odejściem od tego stanu za pomocą usługi Azure Automation
description: Samouczek — Zarządzanie konfiguracjami serwera za pomocą usługi Azure Automation stanu konfiguracji
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 3bcdb667ee649b9bbf32ad33e74e876cdd2b5cbf
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144193"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurowanie serwerów do żądanego stanu i zarządzanie odejściem od tego stanu

Konfiguracja stanu usługi Azure Automation pozwala na określenie konfiguracji dla serwerów i upewnij się, że te serwery znajdują się w określonym stanie wraz z upływem czasu.

> [!div class="checklist"]
> - Dołączanie maszyny Wirtualnej mają być zarządzane przez usługi Azure Automation DSC
> - Przekazywanie konfiguracji do usługi Azure Automation
> - Kompilacja konfiguracji do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdź stan zgodności zarządzanego węzła

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Program Azure PowerShell module w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość Desired State Configuration (DSC). Aby dowiedzieć się, DSC, zobacz [Windows PowerShell Desired State Configuration — omówienie](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Utwórz i przekaż konfiguracji do usługi Azure Automation

W tym samouczku użyjemy prostej konfiguracji DSC, które gwarantuje, że zainstalowanie usług IIS na maszynie Wirtualnej.

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

Wywołaj `Import-AzureRmAutomationDscConfiguration` polecenia cmdlet można przekazać konfiguracji do konta usługi Automation:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilacja konfiguracji do konfiguracji węzła

Konfiguracja DSC muszą być skompilowane w konfiguracji węzła, zanim mogą być przypisane do węzła.

Aby dowiedzieć się, jak kompilowanie konfiguracji, zobacz [konfiguracje DSC](/powershell/dsc/configurations).

Wywołaj `Start-AzureRmAutomationDscCompilationJob` polecenia cmdlet, aby skompilować `TestConfig` konfiguracji do konfiguracji węzła:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Spowoduje to utworzenie konfiguracji węzła o nazwie `TestConfig.WebServer` na koncie usługi Automation.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Zarejestruj maszynę Wirtualną, która zarządza Konfiguracja stanu

Za pomocą usługi Azure Automation stanu konfiguracji do zarządzania maszynami wirtualnymi platformy Azure (wersja klasyczna i usługi Resource Manager), lokalnych maszyn wirtualnych, maszyn systemu Linux, maszyn wirtualnych usług AWS i fizycznych komputerów w środowisku lokalnym. W tym temacie opisano firma Microsoft, jak można zarejestrować tylko usługi Azure Resource Manager dla maszyn wirtualnych. Aby uzyskać informacji na temat rejestrowania innych rodzajów maszyn, zobacz [dołączanie maszyn w celu zarządzania usługi Azure Automation stan konfiguracji](automation-dsc-onboarding.md).

Wywołaj `Register-AzureRmAutomationDscNode` polecenia cmdlet można zarejestrować maszyny Wirtualnej za pomocą usługi Azure Automation stan konfiguracji.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Rejestruje to określoną maszynę Wirtualną jako zarządzany węzeł w stanie konfiguracji.

### <a name="specify-configuration-mode-settings"></a>Określ ustawienia trybu konfiguracji

Po zarejestrowaniu Maszynę wirtualną jako węzeł zarządzany, można również określić właściwości konfiguracji. Na przykład można określić, czy stan maszyny ma być stosowany tylko raz (DSC nie próbuje zastosować konfigurację po początkowej kontroli), określając `ApplyOnly` jako wartość **ConfigurationMode** właściwości :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić, jak często sprawdza stan konfiguracji DSC przy użyciu **ConfigurationModeFrequencyMins** właściwości:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Aby uzyskać więcej informacji na temat ustawiania właściwości konfiguracji dla zarządzanego węzła, zobacz [AzureRmAutomationDscNode rejestru](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz [Konfigurowanie programu Local Configuration Manager](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy można przypisać konfiguracji węzła skompilowanych chcemy, aby skonfigurować maszynę Wirtualną.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

W ten sposób konfiguracji węzła o nazwie `TestConfig.WebServer` na zarejestrowanym węzłem DSC, o nazwie `DscVm`.
Domyślnie węzła DSC jest sprawdzany pod kątem zgodności z konfiguracji węzła co 30 minut.
Aby dowiedzieć się, jak zmienić interwał sprawdzania zgodności, zobacz [Konfigurowanie programu Local Configuration Manager](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdź stan zgodności zarządzanego węzła

Raporty dotyczące stanu zgodności zarządzanych węzłów można uzyskać przez wywołanie metody `Get-AzureRmAutomationDscNodeReport` polecenia cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Usuwanie węzłów z usługi

Podczas dodawania węzła do usługi Azure Automation stan konfiguracji, ustawienia Local Configuration Manager są ustawiane zarejestrować za pomocą konfiguracji usługi ściągania i wypychania i wymaganych modułów, aby skonfigurować maszynę.
Jeśli zdecydujesz się usunąć węzła z usługi, możesz to zrobić przy użyciu witryny Azure portal lub poleceń cmdlet Az.

> [!NOTE]
> Wyrejestrowywanie węzła z usługi tylko Ustawia ustawienia Local Configuration Manager, więc węzeł nie jest już połączenia z usługą.
> Nie ma wpływu konfiguracji, który jest aktualnie używany do węzła.
> Aby usunąć bieżącą konfigurację, użyj [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) lub usunięcie pliku konfiguracji lokalnej (jest to jedyna opcja w przypadku węzłów systemu Linux).

### <a name="azure-portal"></a>Azure Portal

Usługi Azure Automation, kliknij pozycję **State configuration (DSC)** w spisie treści.
Następnie kliknij przycisk **węzłów** Aby wyświetlić listę węzłów, które są zarejestrowane w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku węzła kliknij **Wyrejestruj**.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzła z usługi Azure Automation stan konfiguracji przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [AzAutomationDscNode Wyrejestruj](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dodawanie węzłów, zobacz [dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
