---
title: Konfigurowanie wymaganego stanu serwerów i zarządzanie odejściem od tego stanu za pomocą usługi Azure Automation
description: Samouczek — Zarządzanie konfiguracjami serwera w usłudze Konfiguracja DSC automatyzacji Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
manager: carmonm
ms.topic: conceptual
ms.date: 09/25/2017
ms.openlocfilehash: 6203cc8679561fa342a2e15a3819dd5513774dde
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195213"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurowanie serwerów do pożądanego stanu i Zarządzaj odejście

Konfiguracji usługi Azure Automation pożądanej stanu (DSC) pozwala na określanie konfiguracji dla serwerów i upewnij się, że te serwery są w określonym stanie wraz z upływem czasu.



> [!div class="checklist"]
> * Dołączyć Maszynę wirtualną na potrzeby zarządzania przez Konfiguracja DSC automatyzacji Azure
> * Przekazywanie konfiguracji do usługi Automatyzacja Azure
> * Kompiluj konfiguracji w konfiguracji węzła
> * Przypisać konfiguracji węzła do węzła zarządzanego
> * Sprawdź stan zgodności zarządzanego węzła

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka będą potrzebne:

* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna Azure Resource Manager (nie klasycznego) systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Azure PowerShell w wersji modułu 3,6 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Znajomość DSC. Aby uzyskać informacje o konfiguracji DSC, zobacz [żądanego stanu konfiguracji Omówienie środowiska Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Utwórz i przekaż konfiguracji do usługi Automatyzacja Azure

W tym samouczku używamy prostą konfigurację DSC, które powodują, że IIS są zainstalowane na Maszynie wirtualnej.

Aby uzyskać informacje o konfiguracjach DSC, zobacz [Konfiguracje DSC](https://docs.microsoft.com/powershell/dsc/configurations).

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

Wywołanie `Import-AzureRmAutomationDscConfiguration` polecenia cmdlet można przekazać konfiguracji do Twojego konta automatyzacji:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompiluj konfiguracji w konfiguracji węzła

Konfiguracja DSC muszą być skompilowane w konfiguracji węzła, zanim można go przypisać do węzła.

Informacje o konfiguracjach kompilacji, zobacz [konfiguracji DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Wywołanie `Start-AzureRmAutomationDscCompilationJob` polecenia cmdlet, aby skompilować `TestConfig` konfiguracji w konfiguracji węzła:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Spowoduje to utworzenie konfiguracji węzła o nazwie `TestConfig.WebServer` na Twoim koncie automatyzacji.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Zarejestruj Maszynę wirtualną na potrzeby zarządzania przez DSC

Konfiguracja DSC automatyzacji Azure umożliwia zarządzanie maszynach wirtualnych platformy Azure (Classic i Resource Manager), lokalnych maszyn wirtualnych systemu Linux maszyny, usług AWS maszyn wirtualnych i fizycznych komputerach lokalnych. W tym temacie opisano firma Microsoft, jak można zarejestrować tylko Menedżera zasobów maszyn wirtualnych platformy Azure.
Informacje o rejestrowaniu innego typu maszyny, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).

Wywołanie `Register-AzureRmAutomationDscNode` polecenia cmdlet można zarejestrować maszyny Wirtualnej w usłudze Konfiguracja DSC automatyzacji Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

To rejestruje określoną maszynę Wirtualną jako węzła DSC w Twoje konto usługi Automatyzacja Azure.

### <a name="specify-configuration-mode-settings"></a>Określ ustawienia trybu konfiguracji

Podczas rejestrowania Maszynę wirtualną jako węzeł zarządzany, można także określić właściwości konfiguracji.
Na przykład można określić, czy stan maszyny ma być stosowany tylko raz (DSC nie jest podejmowana próba zastosowania konfiguracji po początkowej wyboru), określając `ApplyOnly` jako wartość **ConfigurationMode** właściwości :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Można również określić, jak często sprawdza stan konfiguracji DSC przy użyciu **ConfigurationModeFrequencyMins** właściwości:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Aby uzyskać więcej informacji na temat ustawiania właściwości konfiguracji dla węzła zarządzanego, zobacz [AzureRmAutomationDscNode rejestru](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Aby uzyskać więcej informacji o ustawieniach konfiguracji DSC, zobacz [Konfigurowanie lokalny program Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisać konfiguracji węzła do węzła zarządzanego

Teraz możemy można przypisać konfiguracji węzła skompilowanych chcemy, aby skonfigurować maszynę Wirtualną.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

W ten sposób konfiguracji węzła o nazwie `TestConfig.WebServer` do zarejestrowanych węzła DSC o nazwie `DscVm`.
Domyślnie węzła DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut.
Aby dowiedzieć się, jak zmienić interwał sprawdzania zgodności, zobacz [Konfigurowanie lokalny program Configuration Manager](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdź stan zgodności zarządzanego węzła

Raporty dotyczące stanu zgodności węzła DSC można uzyskać przez wywołanie metody `Get-AzureRmAutomationDscNodeReport` polecenia cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, aby dodać węzły mają być zarządzane w usłudze Konfiguracja DSC automatyzacji Azure, zobacz temat [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md)
* Aby dowiedzieć się, jak używać usługi Konfiguracja DSC automatyzacji za pomocą portalu Azure, zobacz [wprowadzenie do korzystania z usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-getting-started.md)
* Aby uzyskać informacje dotyczące kompilowania konfiguracji DSC, dzięki czemu można je przypisać do węzły docelowe, zobacz [kompilowania konfiguracji DSC automatyzacji Azure](automation-dsc-compile.md)
* Dokumentacja poleceń cmdlet programu PowerShell dla usługi Konfiguracja DSC automatyzacji Azure, aby zapoznać [poleceń cmdlet usługi Konfiguracja DSC automatyzacji Azure](/powershell/module/azurerm.automation/#automation)
* Aby uzyskać informacje o cenach, zobacz [cennik usługi Konfiguracja DSC automatyzacji Azure](https://azure.microsoft.com/pricing/details/automation/)
* Aby zapoznać się z przykładem w potoku ciągłe wdrażanie przy użyciu usługi Konfiguracja DSC automatyzacji Azure, zobacz [ciągłe wdrażanie DSC automatyzacji Azure przy użyciu maszyn wirtualnych IaaS i Chocolatey](automation-dsc-cd-chocolatey.md)
