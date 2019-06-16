---
title: Zarządzanie elementami runbook w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania elementami runbook w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61300281"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie elementami runbook w usłudze Azure Automation

Element runbook można dodać do usługi Azure Automation, albo [tworzenia nowej](#create-a-runbook) lub przez zaimportowanie istniejącego elementu runbook z pliku lub [galerii elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje na temat tworzenia i importowania elementów runbook z pliku.  Możesz uzyskać wszystkie szczegóły znajdujące się na dostęp do społeczności elementów runbook i modułów w [elementów Runbook i modułów galeriach, aby usługa Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Możesz utworzyć nowy element runbook w usłudze Azure Automation za pomocą jednego z witrynie Azure Portal lub programu Windows PowerShell. Po utworzeniu elementu runbook można edytować za pomocą informacji w [uczenie się przepływu pracy programu PowerShell](automation-powershell-workflow.md) i [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Utwórz element runbook w witrynie Azure portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Z Centrum, wybierz **elementów Runbook** aby otworzyć listę elementów runbook.
3. Kliknij pozycję **Dodaj element runbook** przycisku i następnie **Utwórz nowy element runbook**.
4. Wpisz **nazwa** dla elementu runbook, a następnie wybierz jego [typu](automation-runbook-types.md). Nazwa elementu runbook musi zaczynać się literą i mogą mieć litery, cyfry, podkreślenia i łączniki.
5. Kliknij przycisk **Utwórz** do tworzenia elementu runbook i otworzyć Edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie elementu runbook za pomocą programu PowerShell

Możesz użyć [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) polecenia cmdlet, aby utworzyć pustą [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Użyj **typu** parametru do określenia zestawu obejmującego cztery typy elementów runbook.

Następujące przykładowe polecenia pokazują, jak utworzyć nowy pusty element runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importowanie elementu runbook

Można utworzyć nowy element runbook w usłudze Azure Automation, importując skrypt programu PowerShell lub przepływu pracy programu PowerShell (z rozszerzeniem .ps1), wyeksportowanego graficznego elementu runbook (graphrunbook) lub skrypt języka Python 2 (z rozszerzeniem PY).  Należy określić [typ elementu runbook](automation-runbook-types.md) utworzonego podczas importowania, biorąc pod uwagę następujące kwestie.

* A `.graphrunbook` tylko można zaimportować pliku do nowego [graficzny element runbook](automation-runbook-types.md#graphical-runbooks), i graficzne elementy runbook można tworzyć tylko z `.graphrunbook` pliku.
* A `.ps1` pliku zawierającego przepływu pracy programu PowerShell można importować tylko do [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Jeśli plik zawiera wiele przepływów pracy programu PowerShell, następnie import zakończy się niepowodzeniem. Należy zapisać każdego przepływu pracy do własnego pliku i zaimportować każdego oddzielnie.
* A `.ps1` pliku, który nie zawiera przepływu pracy mogą być importowane do albo [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Po zaimportowaniu do elementu runbook przepływu pracy programu PowerShell jest następnie konwertowana do przepływu pracy i komentarze są uwzględnione w elementu runbook, określania, które zostały wprowadzone zmiany.

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
8. Należy najpierw [opublikować element runbook](#publish-a-runbook) zanim będzie można go uruchomić.

> [!NOTE]
> Po zaimportowaniu graficznego elementu runbook lub graficzny element runbook przepływu pracy programu PowerShell, istnieje możliwość do przekonwertowania do innych typów, jeśli chcieli. Nie można przekonwertować tekstowy element runbook.

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

## <a name="test-a-runbook"></a>Testowanie elementu Runbook

Podczas testowania elementu runbook [wersję roboczą](#publish-a-runbook) jest wykonywane i wykonywane są wszystkie akcje, które wykonuje. Historia zadań nie zostanie utworzony, ale [dane wyjściowe](automation-runbook-output-and-messages.md#output-stream) i [ostrzeżeń i błędów](automation-runbook-output-and-messages.md#message-streams) strumienie są wyświetlane w teście dane wyjściowe w okienku. Komunikaty dotyczące [pełne Stream](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych tylko wtedy, gdy [zmiennej $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) jest ustawiona, aby kontynuować.

Mimo że jest uruchamiana wersja robocza elementu runbook nadal wykonywany normalnie i wykonuje wszystkie akcje dotyczące zasobów w środowisku. Z tego powodu należy przetestować tylko elementy runbook dotyczące zasobów spoza środowiska produkcyjnego.

Procedury do przetestowania każdego [typ elementu runbook](automation-runbook-types.md) jest takie same i występują żadnej różnicy w testach między Edytor tekstów, a edytor graficzny, w witrynie Azure portal.  

1. Otwórz wersję roboczą elementu runbook albo [Edytor tekstów](automation-edit-textual-runbook.md) lub [edytor graficzny](automation-graphical-authoring-intro.md).
1. Kliknij przycisk **testu** przycisk, aby otworzyć stronę testową.
1. Jeśli element runbook ma parametry, będą one wyświetlane w okienku po lewej stronie, gdzie możesz podać wartości, które ma być używany dla testu.
1. Jeśli chcesz uruchomić test [hybrydowego procesu roboczego Runbook](automation-hybrid-runbook-worker.md), następnie zmienić **parametrów uruchomieniowych** do **hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie Zachowaj domyślną **Azure** do uruchomienia testu w chmurze.
1. Kliknij przycisk **Start** przycisk, aby uruchomić test.
1. Jeśli element runbook ma [przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficzny](automation-runbook-types.md#graphical-runbooks), a następnie można zatrzymać lub je zawiesić, gdy jest testowana za pomocą przycisków pod okienkiem danych wyjściowych. Po wstrzymaniu elementu runbook, kończy działanie bieżącego przed jego wstrzymaniem. Po wstrzymaniu elementu runbook można go zatrzymać lub uruchomić ją ponownie.
1. Sprawdź dane wyjściowe z elementu runbook w okienku danych wyjściowych.

## <a name="publish-a-runbook"></a>Publikowanie elementu Runbook

Podczas tworzenia lub zaimportować nowy element runbook należy opublikować zanim będzie można go uruchomić.  Każdy element runbook w usłudze Automation ma wersje roboczą i opublikowaną. Opublikowana wersja jest dostępna do uruchomienia, a tylko wersję roboczą można edytować. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinny być udostępniane, należy ją opublikować, co powoduje nadpisanie wersji opublikowanej wersją roboczą.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz element runbook w witrynie Azure portal.
2. Kliknij przycisk **Edytuj** przycisku.
3. Kliknij przycisk **Publikuj** przycisku i następnie **tak** w komunikacie weryfikacyjnym.

### <a name="powershell"></a>PowerShell

Możesz użyć [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) polecenia cmdlet, aby opublikować element runbook za pomocą programu Windows PowerShell. Następujące przykładowe polecenia pokazują, jak opublikować przykładowego elementu runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak można korzystać z elementów Runbook i galerię modułów programu PowerShell, zobacz [elementów Runbook i modułów galeriach, aby usługa Azure Automation](automation-runbook-gallery.md)
* Aby dowiedzieć się więcej o edytowaniu elementów runbook programu PowerShell i przepływie pracy programu PowerShell w programie Edytor tekstów, zobacz [edytowanie tekstowych elementów runbook w usłudze Azure Automation](automation-edit-textual-runbook.md)
* Aby dowiedzieć się więcej na temat tworzenia graficznego elementu runbook, zobacz [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md)
