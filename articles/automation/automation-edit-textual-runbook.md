---
title: Edytowanie tekstową elementy runbook automatyzacji Azure
description: Ten artykuł zawiera różne procedury dotyczące pracy z elementami runbook programu PowerShell i przepływ pracy programu PowerShell w automatyzacji Azure za pomocą edytora tekstową.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 517c339122e493dfc4140acb12a2e181babea019
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edytowanie tekstową elementy runbook automatyzacji Azure

Edytor tekstowy w automatyzacji Azure może służyć do edycji [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementach runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). To jest typowe funkcje innych edytory kodu, takie jak intellisense i kodowanie kolorami o dodatkowe funkcje specjalne ułatwi podczas uzyskiwania dostępu do zasobów typowych dla elementów runbook. Ten artykuł zawiera szczegółowy opis kroków do wykonywania różnych funkcji z tego edytora.

Edytor tekstowy zawiera funkcję wstawiania kodu dla polecenia cmdlet, zasoby i podrzędnych elementów runbook do elementu runbook. Zamiast wpisywać kod samodzielnie, możesz wybrać z listy dostępnych zasobów i odpowiedni kod do elementu runbook.

Każdy element runbook automatyzacji Azure ma dwie wersje: roboczą i opublikowaną. Edytować wersję roboczą elementu runbook, a następnie ją opublikować, tak aby można było wykonać. Nie można edytować wersji opublikowanej. Zobacz [publikowanie elementu runbook](automation-creating-importing-runbook.md#publishing-a-runbook) Aby uzyskać więcej informacji.

Aby pracować z [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks), zobacz [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Aby edytować element runbook za pomocą portalu Azure

Aby otworzyć elementu runbook do edycji w edytorze tekstową, należy użyć poniższej procedury.

1. W portalu Azure wybierz konto automatyzacji.
2. W obszarze **AUTOMATYZACJI procesu**, wybierz pozycję **elementów Runbook** aby otworzyć listę elementów runbook.
3. Wybierz element runbook chcesz edytować, a następnie kliknij przycisk **Edytuj** przycisku.
4. Wprowadź wymagane zmiany.
5. Kliknij przycisk **zapisać** po zakończeniu edycji.
6. Kliknij przycisk **publikowania** Jeśli chcesz najnowszą wersję roboczą elementu runbook do opublikowania.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Aby wstawić polecenia cmdlet do elementu runbook

1. W obszarze roboczym edytor tekstowy umieść kursor w miejscu umieścić polecenia cmdlet.
2. Rozwiń węzeł **poleceń cmdlet** węzła w formancie biblioteki.
3. Rozwiń moduł zawierający polecenia cmdlet, którego chcesz użyć.
4. Kliknij prawym przyciskiem myszy polecenie cmdlet, aby wstawić i wybierz **Dodaj do kanwy**. Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, domyślny zestaw zostanie dodany. Można również rozwinąć polecenia cmdlet, aby wybrać zestaw innym parametrem.
5. Kod dla polecenia cmdlet dodaje się z jego całą listę parametrów.
6. Wpisz odpowiednią wartość w miejscu typu danych ujęte w nawiasy klamrowe <> dla wymaganych parametrów. Usuń wszystkie parametry, które nie są potrzebne.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Aby wstawić kod dla podrzędnego elementu runbook do elementu runbook

1. W obszarze roboczym edytor tekstowy, umieść kursor w miejscu umieść kod [podrzędnego elementu runbook](automation-child-runbooks.md).
2. Rozwiń węzeł **elementów Runbook** węzła w formancie biblioteki.
3. Kliknij prawym przyciskiem myszy element runbook do wstawienia, a następnie wybierz **Dodaj do kanwy**.
4. Kod dla podrzędnego elementu runbook dodaje się z dowolnym symbole zastępcze dla wszystkich parametrów elementu runbook.
5. Zastąp symbole zastępcze odpowiednie wartości dla każdego parametru.

### <a name="to-insert-an-asset-into-a-runbook"></a>Aby wstawić zasób do elementu runbook

1. W obszarze roboczym edytor tekstowy umieść kursor w miejscu umieść kod dla podrzędnego elementu runbook.
2. Rozwiń węzeł **zasoby** węzła w formancie biblioteki.
3. Rozwiń węzeł dla typu zawartości, który ma.
4. Kliknij prawym przyciskiem myszy element zawartości do wstawienia, a następnie wybierz **Dodaj do kanwy**. Dla [zasoby zmiennej](automation-variables.md), wybierz opcję **Dodaj "Pobierz zmienną" do kanwy** lub **Dodaj "Ustaw zmienną" do kanwy** w zależności od tego, czy chcesz pobrać lub ustawić zmiennej.
5. Do elementu runbook zostanie wstawiony kod dla elementu zawartości.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Aby edytować element runbook usługi Automatyzacja Azure przy użyciu programu Windows PowerShell

Aby edytować element runbook za pomocą środowiska Windows PowerShell, możesz użyć dowolnego edytora i zapisać go w pliku .ps1. Można użyć [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) polecenia cmdlet, aby pobrać zawartość elementu runbook, a następnie [AzureAutomationRunbookDefinition zestaw](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) polecenia cmdlet, aby zastąpić istniejącą wersji roboczej elementu runbook wersją zmodyfikowaną.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Aby pobrać zawartość elementu Runbook za pomocą środowiska Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak pobrać skrypt dla elementu runbook, a następnie zapisz plik skryptu. W tym przykładzie jest pobierana wersja robocza. Użytkownik może również pobrać opublikowaną wersję elementu runbook, chociaż nie można zmienić tej wersji.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Aby zmienić zawartość elementu Runbook za pomocą środowiska Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak zastąpić istniejącą zawartość elementu runbook zawartością pliku skryptu. Należy pamiętać, że to tej samej przykładowej procedury jako [Aby zaimportować element runbook z pliku skryptu za pomocą programu Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Tworzenie lub importowanie elementu runbook automatyzacji Azure](automation-creating-importing-runbook.md)
* [Learning przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* [Graficzny tworzenia w programie usługi Automatyzacja Azure](automation-graphical-authoring-intro.md)
* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Harmonogramy](automation-schedules.md)
* [Zmienne](automation-variables.md)
