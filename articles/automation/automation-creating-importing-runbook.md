---
title: Tworzenie lub importowanie elementu runbook w usłudze Azure Automation
description: W tym artykule opisano sposób tworzenia nowego elementu runbook w usłudze Azure Automation lub zaimportujemy jeden z pliku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ccf9036d3701c710c6d3258f81390ed355c246c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578035"
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Tworzenie lub importowanie elementu runbook w usłudze Azure Automation

Element runbook można dodać do usługi Azure Automation, albo [tworzenia nowej](#creating-a-new-runbook) lub przez zaimportowanie istniejącego elementu runbook z pliku lub [galerii elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje na temat tworzenia i importowania elementów runbook z pliku.  Możesz uzyskać wszystkie szczegóły znajdujące się na dostęp do społeczności elementów runbook i modułów w [elementów Runbook i modułów galeriach, aby usługa Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Tworzenie nowego elementu runbook

Możesz utworzyć nowy element runbook w usłudze Azure Automation za pomocą jednego z witrynie Azure Portal lub programu Windows PowerShell. Po utworzeniu elementu runbook można edytować za pomocą informacji w [uczenie się przepływu pracy programu PowerShell](automation-powershell-workflow.md) i [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Aby utworzyć nowy element runbook usługi Azure Automation w witrynie Azure portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
1. Z Centrum, wybierz **elementów Runbook** aby otworzyć listę elementów runbook.
1. Kliknij pozycję **Dodaj element runbook** przycisku i następnie **Utwórz nowy element runbook**.
1. Wpisz **nazwa** dla elementu runbook, a następnie wybierz jego [typu](automation-runbook-types.md). Nazwa elementu runbook musi zaczynać się literą i mogą mieć litery, cyfry, podkreślenia i łączniki.
1. Kliknij przycisk **Utwórz** do tworzenia elementu runbook i otworzyć Edytor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Aby utworzyć nowy element runbook usługi Azure Automation za pomocą programu Windows PowerShell
Możesz użyć [New-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationrunbook) polecenia cmdlet, aby utworzyć pustą [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). **Typu** parametru powinny zostać dołączone do określenia zestawu obejmującego cztery typy elementów runbook.

Następujące przykładowe polecenia pokazują, jak utworzyć nowy pusty element runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importowanie elementu runbook z pliku do usługi Azure Automation

Można utworzyć nowy element runbook w usłudze Azure Automation, importując skrypt programu PowerShell lub przepływu pracy programu PowerShell (z rozszerzeniem .ps1), wyeksportowanego graficznego elementu runbook (graphrunbook) lub skrypt języka Python 2 (z rozszerzeniem PY).  Należy określić [typ elementu runbook](automation-runbook-types.md) utworzonego podczas importowania, biorąc pod uwagę następujące kwestie.

* Plik graphrunbook można importować tylko do nowego [graficzny element runbook](automation-runbook-types.md#graphical-runbooks), i graficzne elementy runbook można tworzyć tylko z plikiem graphrunbook.
* Plik .ps1 zawierającego przepływu pracy programu PowerShell można importować tylko do [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Jeśli plik zawiera wiele przepływów pracy programu PowerShell, następnie import zakończy się niepowodzeniem. Należy zapisać każdego przepływu pracy do własnego pliku i zaimportować każdego oddzielnie.
* Plik .ps1, który nie zawiera przepływu pracy mogą być importowane do albo [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Po zaimportowaniu do elementu runbook przepływu pracy programu PowerShell jest następnie konwertowana do przepływu pracy i komentarze są uwzględnione w elementu runbook, określania, które zostały wprowadzone zmiany.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Aby zaimportować element runbook z pliku za pomocą witryny Azure portal

Poniższa procedura służy do importowania pliku skryptu do usługi Azure Automation.  

> [!NOTE]
> Należy pamiętać, że można importować tylko plik .ps1 do elementu runbook przepływu pracy programu PowerShell przy użyciu portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Z Centrum, wybierz **elementów Runbook** aby otworzyć listę elementów runbook.
3. Kliknij pozycję **Dodaj element runbook** przycisku i następnie **importu**.
4. Kliknij przycisk **plikowi elementu Runbook** aby wybrać plik do zaimportowania
5. Jeśli **nazwa** pole jest włączone, a następnie użytkownik może go zmienić.  Nazwa elementu runbook musi zaczynać się literą i mogą mieć litery, cyfry, podkreślenia i łączniki.
6. [Typ elementu runbook](automation-runbook-types.md) jest automatycznie wybierana, ale można zmienić typ, po uwzględnieniu ograniczenia stosowane do konta. 
7. Nowy element runbook zostanie wyświetlony na liście elementów runbook dla konta usługi Automation.
8. Należy najpierw [opublikować element runbook](#publishing-a-runbook) zanim będzie można go uruchomić.

> [!NOTE]
> Po zaimportowaniu graficznego elementu runbook lub graficzny element runbook przepływu pracy programu PowerShell, istnieje możliwość do przekonwertowania do innych typów, jeśli chcieli. Nie można przekonwertować tekstowy element runbook.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Aby zaimportować element runbook z pliku skryptu za pomocą programu Windows PowerShell
Możesz użyć [polecenia Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) polecenia cmdlet, aby zaimportować plik skryptu jako wersję roboczą elementu runbook przepływu pracy programu PowerShell. Jeśli istnieje już element runbook, import zakończy się niepowodzeniem, chyba że używasz *-Force* parametru. 

Następujące przykładowe polecenia pokazują, jak zaimportować plik skryptu do elementu runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="publishing-a-runbook"></a>Publikowanie elementu runbook

Podczas tworzenia lub zaimportować nowy element runbook należy opublikować zanim będzie można go uruchomić.  Każdy element runbook w usłudze Automation ma wersje roboczą i opublikowaną. Opublikowana wersja jest dostępna do uruchomienia, a tylko wersję roboczą można edytować. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinny być udostępniane, należy ją opublikować, co powoduje nadpisanie wersji opublikowanej wersją roboczą.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Aby opublikować element runbook za pomocą witryny Azure portal

1. Otwórz element runbook w witrynie Azure portal.
1. Kliknij przycisk **Edytuj** przycisku.
1. Kliknij przycisk **Publikuj** przycisku i następnie **tak** w komunikacie weryfikacyjnym.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Aby opublikować element runbook za pomocą programu Windows PowerShell
Możesz użyć [Publish-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/publish-azurermautomationrunbook) polecenia cmdlet, aby opublikować element runbook za pomocą programu Windows PowerShell. Następujące przykładowe polecenia pokazują, jak opublikować przykładowego elementu runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak można korzystać z elementów Runbook i galerię modułów programu PowerShell, zobacz [elementów Runbook i modułów galeriach, aby usługa Azure Automation](automation-runbook-gallery.md)
* Aby dowiedzieć się więcej o edytowaniu elementów runbook programu PowerShell i przepływie pracy programu PowerShell w programie Edytor tekstów, zobacz [edytowanie tekstowych elementów runbook w usłudze Azure Automation](automation-edit-textual-runbook.md)
* Aby dowiedzieć się więcej na temat tworzenia graficznego elementu runbook, zobacz [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md)