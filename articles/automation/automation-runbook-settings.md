---
title: Ustawienia elementu Runbook w usłudze Azure Automation
description: W tym artykule opisano ustawienia konfiguracji dla elementu runbook w usłudze Azure Automation oraz sposobu zmiany ich przy użyciu witryny Azure portal i programu Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 589df59e02a18629d5f405ff1ce8870333f2228e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397717"
---
# <a name="runbook-settings"></a>Ustawienia elementu Runbook
Każdy element runbook w usłudze Azure Automation zawiera wiele ustawień, które ułatwiają jego identyfikację i zmianę jej zachowania w rejestrowania. Każde z tych ustawień poniżej opisano następują procedury wykonywane na ich modyfikowania.

## <a name="settings"></a>Ustawienia
### <a name="name-and-description"></a>Nazwa i opis
Nie można zmienić nazwy elementu runbook, po jego utworzeniu. Opis jest opcjonalny i może zawierać maksymalnie 512 znaków.

### <a name="tags"></a>Tagi
Znaczniki umożliwiają przypisywanie unikatowych słów i wyrażeń w celu ułatwienia identyfikacji elementu runbook. Na przykład podczas przesyłania elementu runbook w celu [galerii programu PowerShell](https://www.powershellgallery.com/), należy określić konkretnego tagów, aby zidentyfikować kategorie, które powinny zostać wyświetlone elementu runbook w. Można określić wiele znaczników elementu runbook, oddzielając je przecinkami.

### <a name="logging"></a>Rejestrowanie
Domyślnie rekordy pełne i postępu nie są zapisywane w historii zadań. Możesz zmienić ustawienia określonego elementu runbook, aby zapisywać te rekordy. Aby uzyskać więcej informacji na temat tych rekordów, zobacz [Runbook Output and Messages](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Zmiana ustawień elementu runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Zmiana ustawień elementu runbook za pomocą witryny Azure portal
Możesz zmienić ustawienia dla elementu runbook w witrynie Azure portal z **ustawienia** blok elementu runbook.

1. W witrynie Azure portal wybierz **automatyzacji** , a następnie kliknij nazwę konta usługi automation.
2. Wybierz **elementów Runbook** kartę.
3. Kliknij nazwę elementu runbook, a następnie nastąpi przekierowanie do bloku ustawienia elementu runbook. W tym miejscu można określić lub zmodyfikować znaczniki, opis elementu runbook, konfigurowanie ustawień śledzenia i rejestrowania i dostęp do narzędzia obsługi, aby pomóc w rozwiązywaniu problemów.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Zmiana ustawień elementu runbook za pomocą programu Windows PowerShell
Możesz użyć [elementu Set-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/set-azurermautomationrunbook) polecenia cmdlet, aby zmienić ustawienia dla elementu runbook. Jeśli chcesz określić wiele znaczników można albo udostępnić tablicy lub ciągu o jednej wartości rozdzielany przecinkami, aby parametr tagów. Można uzyskać bieżącego tagi z [Get-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/get-azurermautomationrunbook).

Następujące przykładowe polecenia pokazują, jak ustawić właściwości dla elementu runbook. Ten przykład dodaje trzy znaczniki do istniejących tagów i określa, że powinny być rejestrowane rekordów pełnych.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się, jak tworzyć i pobierać dane wyjściowe i komunikaty o błędach z elementów runbook, zobacz [Runbook Output and Messages](automation-runbook-output-and-messages.md) 
* Aby dowiedzieć się, jak dodać element runbook, który już został opracowany przez społeczność i inne źródła lub tworzenia własnych elementów runbook zobacz [Tworzenie lub importowanie elementu Runbook](automation-creating-importing-runbook.md) 

