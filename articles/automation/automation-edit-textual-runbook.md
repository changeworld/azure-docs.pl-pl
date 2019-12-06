---
title: Edytowanie tekstowych elementów Runbook w Azure Automation
description: Ten artykuł zawiera różne procedury pracy z elementami Runbook programu PowerShell i programu PowerShell w programie Azure Automation przy użyciu edytora tekstu.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850860"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edytowanie tekstowych elementów Runbook w Azure Automation

Edytor tekstowy w Azure Automation może służyć do edytowania [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Ma ona typowe funkcje innych edytorów kodu, takich jak IntelliSense i kodowanie kolorami, dzięki dodatkowym funkcjom, które ułatwiają uzyskiwanie dostępu do zasobów wspólnych dla elementów Runbook. Ten artykuł zawiera szczegółowe instrukcje dotyczące wykonywania różnych funkcji za pomocą tego edytora.

Edytor tekstowy zawiera funkcję wstawiania kodu dla poleceń cmdlet, zasobów i podrzędnych elementów Runbook do elementu Runbook. Zamiast wpisywać kod samodzielnie, możesz wybrać z listy dostępnych zasobów i umieścić odpowiedni kod w elemencie Runbook.

Każdy element Runbook w Azure Automation ma dwie wersje, wersję roboczą i opublikowaną. Można edytować wersję roboczą elementu Runbook, a następnie opublikować ją, aby można ją było wykonać. Nie można edytować wersji opublikowanej. Aby uzyskać więcej informacji, zobacz [Publikowanie elementu Runbook](manage-runbooks.md#publish-a-runbook).

Aby korzystać z [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks), zobacz [tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Aby edytować element Runbook za pomocą Azure Portal

Aby otworzyć element Runbook do edycji w edytorze tekstu, należy wykonać czynności opisane w poniższej procedurze.

1. W Azure Portal wybierz konto usługi Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **elementy Runbook** , aby otworzyć listę elementów Runbook.
3. Wybierz element Runbook, który chcesz edytować, a następnie kliknij przycisk **Edytuj** .
4. Edytuj element Runbook.
5. Kliknij przycisk **Zapisz** po zakończeniu edycji.
6. Kliknij przycisk **Publikuj** chcącym najnowszą wersję roboczą elementu runbook, które mają zostać opublikowane.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Aby wstawić polecenie cmdlet do elementu Runbook

1. Na kanwie edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić polecenie cmdlet.
2. Rozwiń węzeł **polecenia cmdlet** w kontrolce Biblioteka.
3. Rozwiń moduł zawierający polecenie cmdlet, którego chcesz użyć.
4. Kliknij prawym przyciskiem myszy polecenie cmdlet, aby wstawić i wybrać pozycję **Dodaj do kanwy**. Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, zostanie dodany zestaw domyślny. Możesz również rozwinąć polecenie cmdlet, aby wybrać inny zestaw parametrów.
5. Kod dla polecenia cmdlet zostanie wstawiony wraz z całą listą parametrów.
6. Podaj odpowiednią wartość w miejscu typu danych, ujętą w nawiasy klamrowe < > dla wszystkich wymaganych parametrów. Usuń wszystkie parametry, które nie są potrzebne.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Aby wstawić kod dla podrzędnego elementu Runbook do elementu Runbook

1. Na kanwie edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić kod dla [podrzędnego elementu Runbook](automation-child-runbooks.md).
2. Rozwiń węzeł **elementy Runbook** w kontrolce Biblioteka.
3. Kliknij prawym przyciskiem myszy element Runbook, a następnie wybierz pozycję **Dodaj do kanwy**.
4. Kod podrzędnego elementu Runbook jest wstawiany z dowolnym symbolem zastępczym dla każdego z parametrów elementu Runbook.
5. Zastąp symbole zastępcze odpowiednimi wartościami dla każdego parametru.

### <a name="to-insert-an-asset-into-a-runbook"></a>Aby wstawić zasób do elementu Runbook

1. Na kanwie edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić kod dla podrzędnego elementu Runbook.
2. Rozwiń węzeł **zasobów** w kontrolce Biblioteka.
3. Rozwiń węzeł typu żądanego elementu zawartości.
4. Kliknij prawym przyciskiem myszy element zawartości, a następnie wybierz pozycję **Dodaj do kanwy**. W przypadku [zasobów zmiennych](automation-variables.md), wybierz opcję **"Pobierz zmienną" do kanwy** lub **Dodaj "Ustaw zmienną" do kanwy** w zależności od tego, czy chcesz uzyskać lub ustawić zmienną.
5. Kod zasobu zostanie wstawiony do elementu Runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Aby edytować element Runbook Azure Automation przy użyciu programu Windows PowerShell

Aby edytować element Runbook za pomocą programu Windows PowerShell, należy użyć wybranego edytora i zapisać go w pliku `.ps1`. Możesz użyć polecenia cmdlet [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) , aby pobrać zawartość elementu Runbook, a następnie polecenie cmdlet [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) , aby zastąpić istniejący element Runbook z zmodyfikowaną wersją.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Aby pobrać zawartość elementu Runbook przy użyciu programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak pobrać skrypt dla elementu runbook i zapisz go jako plik skryptu. W tym przykładzie jest pobierana wersja robocza. Istnieje również możliwość pobrania opublikowanej wersji elementu Runbook, chociaż nie można zmienić tej wersji.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Aby zmienić zawartość elementu Runbook przy użyciu programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak zastąpić istniejącą zawartość elementu Runbook zawartością pliku skryptu. Jest to taka sama Przykładowa procedura jak w programie w [celu zaimportowania elementu Runbook z pliku skryptu za pomocą programu Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md)
* [Uczenie przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md)
* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Harmonogramy](automation-schedules.md)
* [Zmienne](automation-variables.md)

