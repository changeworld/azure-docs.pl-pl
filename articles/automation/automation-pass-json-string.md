---
title: Przekazywanie obiektu JSON do elementu runbook usługi Azure Automation
description: Jak przekazać parametry do elementu runbook jako obiekt JSON
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: Program PowerShell, elementów runbook, json, usługa azure automation
ms.openlocfilehash: 1bdeef02621924bbb7af1e676d2b275229761081
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054760"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Przekazywanie obiektu JSON do elementu runbook usługi Azure Automation

Może być przydatne do przechowywania danych, które mają być przekazane do elementu runbook w pliku JSON.
Na przykład może utworzyć pliku JSON, który zawiera wszystkie parametry, które mają być przekazane do elementu runbook.
Aby to zrobić, musisz przekonwertować na ciąg za pomocą pliku JSON, a następnie przekonwertowania ciągu na obiekt programu PowerShell przed przekazaniem jej zawartość do elementu runbook.

W tym przykładzie utworzymy skrypt programu PowerShell, który wywołuje [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) można uruchomić elementu runbook programu PowerShell, przekazanie zawartości za pomocą pliku JSON do elementu runbook.
Element runbook programu PowerShell uruchamia Maszynę wirtualną platformy Azure, wprowadzenie parametrów dla maszyny Wirtualnej z formatu JSON, która została przekazana.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub <a href="/pricing/free-account/" target="_blank">[utworzyć bezpłatne konto](https://azure.microsoft.com/free/).
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Będziemy uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.
* Program Azure Powershell zainstalowane na komputerze lokalnym. Zobacz [Instalowanie i konfigurowanie programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) uzyskać informacji na temat sposobu uzyskania programu Azure PowerShell.

## <a name="create-the-json-file"></a>Utwórz plik JSON

Wpisz następujący test w pliku tekstowym, a następnie zapisz go jako `test.json` pozwalającego na komputerze lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Tworzenie elementu runbook

Utwórz nowy element runbook programu PowerShell o nazwie "Test-Json" w usłudze Azure Automation.
Aby dowiedzieć się, jak utworzyć nowy element runbook programu PowerShell, zobacz [Mój pierwszy element runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

Aby zaakceptować danych JSON, element runbook musi podjąć obiekt jako parametr wejściowy.

Element runbook może następnie używać właściwości zdefiniowane w formacie JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Zapisz i Opublikuj ten element runbook na Twoim koncie usługi Automation.

## <a name="call-the-runbook-from-powershell"></a>Wywołaj element runbook za pomocą programu PowerShell

Teraz możesz wywołać element runbook z komputera lokalnego przy użyciu programu Azure PowerShell.
Uruchom następujące polecenia środowiska PowerShell:

1. Logowanie do platformy Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Monit o podanie poświadczeń platformy Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

1. Pobierz zawartość pliku JSON, a następnie przekonwertować na ciąg:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` jest to ścieżka, w której zapisano plik JSON.
1. Konwertuj zawartość ciągu `$json` na obiekt programu PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Utworzyć obiektu hashtable parametrów dla `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Należy zauważyć, że ustawiono wartość `Parameters` na obiekt programu PowerShell, który zawiera wartości z pliku JSON. 
1. Uruchamianie elementu runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Element runbook używa wartości z pliku JSON, aby uruchomić Maszynę wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o edytowaniu elementów runbook programu PowerShell i przepływie pracy programu PowerShell w programie Edytor tekstów, zobacz [edytowanie tekstowych elementów runbook w usłudze Azure Automation](automation-edit-textual-runbook.md) 
* Aby dowiedzieć się więcej o tworzeniu i importowanie elementów runbook, zobacz [Tworzenie lub importowanie elementu runbook w usłudze Azure Automation](automation-creating-importing-runbook.md)


