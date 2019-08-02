---
title: Zarządzanie elementami Runbook w Azure Automation
description: W tym artykule opisano sposób zarządzania elementami Runbook w programie Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a477811e46d97375d4dce4d83072dda60ca797c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717212"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie elementami Runbook w Azure Automation

Element Runbook można dodać do Azure Automation przez [utworzenie nowego](#create-a-runbook) lub przez zaimportowanie istniejącego elementu Runbook z pliku lub [galerii elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje dotyczące tworzenia i importowania elementów Runbook z pliku.  Możesz uzyskać szczegółowe informacje na temat uzyskiwania dostępu do elementów Runbook i modułów z społeczności w programie [Runbook i galeriach modułów dla Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Utwórz element Runbook

Nowy element Runbook można utworzyć w Azure Automation przy użyciu jednego z portali platformy Azure lub środowiska Windows PowerShell. Po utworzeniu elementu Runbook można go edytować przy użyciu informacji w [przepływie pracy programu PowerShell](automation-powershell-workflow.md) i [tworzeniu grafiki w programie Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Tworzenie elementu Runbook w Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Z poziomu centrum wybierz pozycję **elementy Runbook** , aby otworzyć listę elementów Runbook.
3. Kliknij przycisk **Dodaj element Runbook** , a następnie **Utwórz nowy element Runbook**.
4. Wpisz **nazwę** elementu Runbook i wybierz jego [Typ](automation-runbook-types.md). Nazwa elementu runbook musi zaczynać się literą i mogą mieć litery, cyfry, podkreślenia i łączniki.
5. Kliknij przycisk **Utwórz** , aby utworzyć element Runbook i otworzyć Edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie elementu Runbook za pomocą programu PowerShell

Do utworzenia pustego [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)można użyć polecenia cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) . Użyj parametru **typu** , aby określić jeden z czterech typów elementów Runbook.

Następujące przykładowe polecenia pokazują, jak utworzyć nowy pusty element Runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importowanie elementu Runbook

Nowy element Runbook można utworzyć w Azure Automation przez zaimportowanie skryptu programu PowerShell lub przepływu pracy programu PowerShell (rozszerzenie ps1), wyeksportowanego graficznego elementu Runbook (. graphrunbook) lub skryptu Python 2 (z rozszerzeniem. PR).  Należy określić [Typ elementu Runbook](automation-runbook-types.md) , który jest tworzony podczas importowania, biorąc pod uwagę poniższe zagadnienia.

* Plik może zostać zaimportowany tylko do nowego [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks), a graficzne elementy Runbook można utworzyć tylko na `.graphrunbook` podstawie pliku. `.graphrunbook`
* Plik zawierający przepływ pracy programu PowerShell może zostać zaimportowany tylko do [elementu Runbook przepływu pracy programu PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) `.ps1` Jeśli plik zawiera wiele przepływów pracy programu PowerShell, importowanie zakończy się niepowodzeniem. Każdy przepływ pracy należy zapisać do własnego pliku i zaimportować osobno.
* Plik zawierający przepływ pracy programu PowerShell nie powinien zostać zaimportowany do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), ponieważ nie może zostać rozpoznany przez aparat skryptu programu PowerShell. `.ps1`
* Plik, który nie zawiera przepływu pracy, można zaimportować do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [elementu Runbook przepływu pracy programu PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) `.ps1`  Jeśli zostanie zaimportowany do elementu Runbook przepływu pracy programu PowerShell, zostanie on przekonwertowany na przepływ pracy, a w elemencie Runbook zostaną uwzględnione wprowadzone zmiany.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Aby zaimportować element Runbook z pliku z Azure Portal

Aby zaimportować plik skryptu do Azure Automation, można użyć poniższej procedury.

> [!NOTE]
> Należy pamiętać, że plik. ps1 można zaimportować tylko do elementu Runbook przepływu pracy programu PowerShell przy użyciu portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Z poziomu centrum wybierz pozycję **elementy Runbook** , aby otworzyć listę elementów Runbook.
3. Kliknij przycisk **Dodaj element Runbook** , a następnie zaimportuj.
4. Kliknij pozycję **plik Runbook** , aby wybrać plik do zaimportowania
5. Jeśli pole **Nazwa** jest włączone, można je zmienić.  Nazwa elementu runbook musi zaczynać się literą i mogą mieć litery, cyfry, podkreślenia i łączniki.
6. [Typ elementu Runbook](automation-runbook-types.md) jest wybierany automatycznie, ale można zmienić typ po wprowadzeniu odpowiednich ograniczeń do konta.
7. Nowy element Runbook zostanie wyświetlony na liście elementów Runbook dla konta usługi Automation.
8. Aby można było uruchomić [element Runbook,](#publish-a-runbook) należy go opublikować.

> [!NOTE]
> Po zaimportowaniu graficznego elementu Runbook lub graficznego elementu Runbook przepływu pracy programu PowerShell istnieje możliwość konwersji na inny typ, jeśli jest to potrzebne. Nie można skonwertować do tekstowego elementu Runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Aby zaimportować element Runbook z pliku skryptu za pomocą programu Windows PowerShell

Można użyć polecenia cmdlet [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) , aby zaimportować plik skryptu jako szkic elementu Runbook przepływu pracy programu PowerShell. Jeśli element Runbook już istnieje, importowanie nie powiedzie się, o ile nie zostanie użyty parametr *-Force* .

Następujące przykładowe polecenia pokazują, jak zaimportować plik skryptu do elementu Runbook.

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

Podczas testowania elementu Runbook jest wykonywana [wersja robocza](#publish-a-runbook) i wszystkie wykonywane działania. Nie utworzono historii zadań, ale strumienie [wyjściowe](automation-runbook-output-and-messages.md#output-stream) i [ostrzegawcze i błędów](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych testu. Komunikaty do [pełnego strumienia](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych tylko wtedy, gdy [zmienna $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) jest ustawiona na Kontynuuj.

Mimo że wersja robocza jest uruchamiana, element Runbook nadal wykonuje się normalnie i wykonuje wszelkie akcje dotyczące zasobów w środowisku. Z tego powodu należy przetestować elementy Runbook wyłącznie dla zasobów nieprodukcyjnych.

Procedura testowania każdego [typu elementu Runbook](automation-runbook-types.md) jest taka sama i nie ma różnicy w testowaniu między edytorem tekstu i edytorem graficznym w Azure Portal.

1. Otwórz wersję roboczą elementu Runbook w [Edytorze](automation-edit-textual-runbook.md) tekstu lub [edytorze graficznym](automation-graphical-authoring-intro.md).
1. Kliknij przycisk **Testuj** , aby otworzyć stronę test.
1. Jeśli element Runbook ma parametry, zostaną one wyświetlone w okienku po lewej stronie, w którym można podać wartości, które mają być używane na potrzeby testu.
1. Jeśli chcesz uruchomić test w hybrydowym procesie [roboczym elementu Runbook](automation-hybrid-runbook-worker.md), Zmień **Parametry uruchomieniowe** na **hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie Zachowaj domyślną **platformę Azure** , aby uruchomić test w chmurze.
1. Kliknij przycisk **Uruchom** , aby rozpocząć test.
1. Jeśli element Runbook jest [przepływem pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficznym](automation-runbook-types.md#graphical-runbooks), można go zatrzymać lub zawiesić podczas testowania za pomocą przycisków pod okienkiem danych wyjściowych. Po wstrzymaniu elementu runbook, kończy działanie bieżącego przed jego wstrzymaniem. Po wstrzymaniu elementu runbook można go zatrzymać lub uruchomić ją ponownie.
1. Sprawdź dane wyjściowe z elementu Runbook w okienku danych wyjściowych.

## <a name="publish-a-runbook"></a>Publikowanie elementu Runbook

Podczas tworzenia lub importowania nowego elementu Runbook należy go opublikować przed jego uruchomieniem.  Każdy element Runbook w usłudze Automation ma wersję roboczą i opublikowaną. Opublikowana wersja jest dostępna do uruchomienia, a tylko wersję roboczą można edytować. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinna być dostępna, opublikuj ją, która zastępuje opublikowaną wersję wersją roboczą.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz element Runbook w Azure Portal.
2. Kliknij przycisk **Edytuj** .
3. Kliknij przycisk **Publikuj** , a następnie pozycję **tak** w komunikacie weryfikacyjnym.

### <a name="powershell"></a>PowerShell

Aby opublikować element Runbook za pomocą programu Windows PowerShell, można użyć polecenia cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) . Następujące przykładowe polecenia pokazują, jak opublikować przykładowy element Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak możesz skorzystać z galerii modułów Runbook i modułu programu PowerShell, zobacz [elementy Runbook i galerie modułów dla Azure Automation](automation-runbook-gallery.md)
* Aby dowiedzieć się więcej o edytowaniu elementów Runbook programu PowerShell i przepływu pracy programu PowerShell przy użyciu edytora tekstu, zobacz [Edytowanie tekstowych elementów Runbook w Azure Automation](automation-edit-textual-runbook.md)
* Aby dowiedzieć się więcej na temat graficznego tworzenia elementów Runbook, zobacz [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md)
