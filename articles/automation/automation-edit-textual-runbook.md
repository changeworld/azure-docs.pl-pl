---
title: Edytowanie tekstowych elementów runbook w usłudze Azure Automation
description: W tym artykule opisano różne procedury do pracy za pomocą programu PowerShell i przepływie pracy programu PowerShell elementów runbook w usłudze Azure Automation, przy użyciu edytor tekstów.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cbe13c9167ebccdd55d54ddd99ba11c6d58b01e8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429937"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edytowanie tekstowych elementów runbook w usłudze Azure Automation

Edytor tekstów w usłudze Azure Automation może służyć do edycji [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementami runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Ma to typowe cechy innych edytorów kodu, takie jak intellisense i kodowanie kolorami oraz dodatkowe funkcje specjalne ułatwi podczas uzyskiwania dostępu do zasobów wspólne dla elementów runbook. Ten artykuł zawiera szczegółowy opis kroków do wykonywania różnych funkcji za pomocą tego edytora.

Edytor tekstów zawiera funkcję wstawiania kodu dla polecenia cmdlet, zasoby i podrzędnych elementów runbook do elementu runbook. Zamiast wpisywać kod samodzielnie, możesz wybrać z listy dostępnych zasobów i odpowiedni kod wstawiony element runbook.

Każdy element runbook w usłudze Azure Automation ma dwie wersje: roboczą i opublikowaną. Edytuj wersję roboczą elementu runbook, a następnie ją opublikować, dzięki czemu mogą być wykonywane. Nie można edytować wersji opublikowanej. Zobacz [publikowanie elementu runbook](automation-creating-importing-runbook.md#publishing-a-runbook) Aby uzyskać więcej informacji.

Aby pracować z [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks), zobacz [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Aby edytować element runbook za pomocą witryny Azure portal

Użyj poniższej procedury można otworzyć elementu runbook do edycji w Edytor tekstów.

1. W witrynie Azure portal wybierz swoje konto usługi automation.
2. W obszarze **AUTOMATYZACJI procesów**, wybierz opcję **elementów Runbook** aby otworzyć listę elementów runbook.
3. Wybierz element runbook chcesz edytować, a następnie kliknij przycisk **Edytuj** przycisku.
4. Wprowadź wymagane zmiany.
5. Kliknij przycisk **Zapisz** po zakończeniu edycji.
6. Kliknij przycisk **Publikuj** chcącym najnowszą wersję roboczą elementu runbook, które mają zostać opublikowane.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Aby wstawić polecenia cmdlet do elementu runbook

1. W kanwie Edytor tekstów umieść kursor w miejscu, w którym chcesz umieścić polecenia cmdlet.
2. Rozwiń **poleceń cmdlet** węzeł w kontrolce Biblioteka.
3. Rozwiń moduł zawierający polecenia cmdlet, którego chcesz użyć.
4. Kliknij prawym przyciskiem myszy polecenie cmdlet, aby wstawić, a następnie wybierz pozycję **Dodaj do kanwy**. Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, domyślny zestaw zostanie dodany. Można również rozwinąć polecenia cmdlet, aby wybrać zestaw różnych parametrów.
5. Kod dla polecenia cmdlet jest wstawiany za pomocą jego całą listę parametrów.
6. Wprowadź odpowiednie wartości zamiast typów danych, ujęte w nawiasy klamrowe <> dla wymaganych parametrów. Usuń wszelkie parametry, które nie są potrzebne.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Aby wstawić kod dla podrzędnego elementu runbook do elementu runbook

1. W obszarze roboczym Edytor tekstów, umieść kursor w miejscu umieść kod [podrzędnego elementu runbook](automation-child-runbooks.md).
2. Rozwiń **elementów Runbook** węzeł w kontrolce Biblioteka.
3. Kliknij prawym przyciskiem myszy element runbook do wstawienia, a następnie wybierz pozycję **Dodaj do kanwy**.
4. Kod dla podrzędnego elementu runbook jest wstawiany za pomocą dowolnego symbole zastępcze dla wszystkich parametrów elementu runbook.
5. Zastąp symbole zastępcze odpowiednimi wartościami dla każdego parametru.

### <a name="to-insert-an-asset-into-a-runbook"></a>Aby wstawić zawartości do elementu runbook

1. W obszarze roboczym Edytor tekstów umieść kursor w miejscu umieść kod dla podrzędnego elementu runbook.
2. Rozwiń **zasoby** węzeł w kontrolce Biblioteka.
3. Rozwiń węzeł dla typu zasobów, które chcesz.
4. Kliknij prawym przyciskiem myszy zasób, aby wstawić, a następnie wybierz pozycję **Dodaj do kanwy**. Aby uzyskać [zmiennych elementów zawartości](automation-variables.md), wybierz opcję **Dodaj "Pobierz zmienną" do kanwy** lub **Dodaj "Ustaw zmienną" do kanwy** w zależności od tego, czy chcesz pobrać lub ustawić zmienną.
5. Kod dla trwałego są wstawiane do elementu runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Aby edytować element runbook usługi Automation przy użyciu programu Windows PowerShell

Aby edytować element runbook za pomocą programu Windows PowerShell, możesz użyć dowolnego edytora i zapisz go w pliku .ps1. Możesz użyć [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) polecenia cmdlet, aby pobrać zawartość elementu runbook i następnie [polecenia Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) polecenia cmdlet, aby zastąpić istniejącą wersję roboczą elementu runbook za pomocą jeden zmodyfikowane.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Aby pobrać zawartość elementu Runbook za pomocą programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak pobrać skrypt dla elementu runbook i zapisz go jako plik skryptu. W tym przykładzie jest pobierana wersja robocza. Użytkownik może również pobrać opublikowaną wersję elementu runbook, mimo że nie można zmienić tej wersji.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Aby zmienić zawartość elementu Runbook za pomocą programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak zastąpić istniejącą zawartość elementu runbook zawartością pliku skryptu. Należy pamiętać, że to tę samą procedurę próbki, podobnie jak w [Aby zaimportować element runbook z pliku skryptu za pomocą programu Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Tworzenie lub importowanie elementu runbook w usłudze Azure Automation](automation-creating-importing-runbook.md)
* [Uczenie się przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* [Graficzny, tworzenia w usłudze Azure Automation](automation-graphical-authoring-intro.md)
* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Harmonogramy](automation-schedules.md)
* [Zmienne](automation-variables.md)

