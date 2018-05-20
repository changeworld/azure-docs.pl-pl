---
title: Tworzenie lub importowanie elementu runbook automatyzacji Azure
description: W tym artykule opisano, jak utworzyć nowy element runbook automatyzacji Azure lub zaimportować z pliku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ac7b050bf96401d33254dedad5035e43850ecc52
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Tworzenie lub importowanie elementu runbook automatyzacji Azure
Można dodać elementu runbook automatyzacji Azure przez [tworzenia nowej](#creating-a-new-runbook) lub importując istniejący element runbook z pliku lub [galerię elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje na temat tworzenia i importowania elementów runbook z pliku.  Można pobrać szczegółów na uzyskiwanie dostępu do społeczności elementów runbook i modułów w [galerie elementów Runbook i modułów w automatyzacji Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Tworzenie nowego elementu runbook
Możesz utworzyć nowy element runbook w automatyzacji Azure przy użyciu jednej z portali Azure lub programu Windows PowerShell. Po utworzeniu elementu runbook można edytować je przy użyciu informacji w [przepływu pracy programu PowerShell Learning](automation-powershell-workflow.md) i [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Aby utworzyć nowy element runbook usługi Automatyzacja Azure za pomocą portalu Azure
1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Koncentrator, zaznacz **elementów Runbook** aby otworzyć listę elementów runbook.
3. Polecenie **Dodaj element runbook** przycisk, a następnie **Utwórz nowy element runbook**.
4. Wpisz **nazwa** dla elementu runbook i wybierz jego [typu](automation-runbook-types.md). Nazwa elementu runbook musi zaczynać się od litery i może mieć litery, cyfry, podkreślenia i łączniki.
5. Kliknij przycisk **Utwórz** do tworzenia elementu runbook i Otwórz Edytor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Aby utworzyć nowy element runbook usługi Automatyzacja Azure za pomocą środowiska Windows PowerShell
Można użyć [AzureRmAutomationRunbook nowy](https://msdn.microsoft.com/library/mt619376.aspx) , aby utworzyć pustą [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Można określić **nazwa** parametr, aby utworzyć pusty element runbook, który można edytować później, lub możesz określić **ścieżki** parametr, aby zaimportować plik elementu runbook. **Typu** parametru należy również określić jeden z czterech typów elementu runbook.

W następujących przykładowych poleceniach pokazano, jak utworzyć nowy pusty element runbook.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importowanie elementu runbook z pliku do usługi Automatyzacja Azure
Można utworzyć nowy element runbook automatyzacji Azure, importując skrypt programu PowerShell lub przepływu pracy programu PowerShell (z rozszerzeniem .ps1), wyeksportowany graficzny element runbook (graphrunbook) lub skryptu Python 2 (rozszerzenie .py).  Należy określić [typu element runbook](automation-runbook-types.md) utworzonego podczas importowania, biorąc pod uwagę przedstawione poniżej zagadnienia.

* Plik graphrunbook można importować tylko do nowego [graficznym elementem runbook](automation-runbook-types.md#graphical-runbooks), i graficznych elementów runbook można tworzyć tylko z pliku graphrunbook.
* Plik .ps1 zawierającego przepływu pracy programu PowerShell można importować tylko do [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Jeśli plik zawiera wiele przepływów pracy programu PowerShell, następnie import zakończy się niepowodzeniem. Należy zapisać każdego przepływu pracy do własnego pliku i zaimportować każdy osobno.
* Plik .ps1, który nie zawiera przepływu pracy mogą być importowane do albo [runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Po zaimportowaniu do elementu runbook przepływu pracy programu PowerShell, następnie jest konwertowana do przepływu pracy i komentarze są uwzględnione w elemencie runbook, określania wprowadzone zmiany.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Aby zaimportować element runbook z pliku za pomocą portalu Azure
Poniższa procedura służy do importowania plików skryptów do automatyzacji Azure.  

> [!NOTE]
> Należy pamiętać, że można importować tylko plik .ps1 do elementu runbook przepływu pracy programu PowerShell za pomocą portalu.
> 
> 

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Koncentrator, zaznacz **elementów Runbook** aby otworzyć listę elementów runbook.
3. Polecenie **Dodaj element runbook** przycisk, a następnie **importu**.
4. Kliknij przycisk **pliku Runbook** i wybierz plik do zaimportowania
5. Jeśli **nazwa** pole jest włączone, a następnie użytkownik może go zmienić.  Nazwa elementu runbook musi zaczynać się od litery i może mieć litery, cyfry, podkreślenia i łączniki.
6. [Typ elementu runbook](automation-runbook-types.md) jest automatycznie wybierany, ale można zmienić typ po przejęciu zastosowanie ograniczeń pod uwagę. 
7. Nowy element runbook zostanie wyświetlony na liście elementów runbook dla konta automatyzacji.
8. Należy [opublikować elementu runbook](#publishing-a-runbook) przed jej uruchomieniem.

> [!NOTE]
> Po zaimportowaniu graficzny element runbook lub graficznym elementem runbook przepływu pracy programu PowerShell, istnieje możliwość można przekonwertować na inny typ, jeśli chce. Nie można przekonwertować tekstowy.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Aby zaimportować element runbook z pliku skryptu za pomocą środowiska Windows PowerShell
Można użyć [AzureRMAutomationRunbook importu](https://msdn.microsoft.com/library/mt603735.aspx) polecenia cmdlet, aby zaimportować plik skryptu jako wersję roboczą elementu runbook przepływu pracy programu PowerShell. Jeśli istnieje już element runbook, importowanie nie powiedzie się, chyba że są używane *-Force* parametru. 

W następujących przykładowych poleceniach pokazano, jak zaimportować plik skryptu do elementu runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publikowanie elementu runbook
Podczas tworzenia lub zaimportować nowy element runbook należy opublikować przed jej uruchomieniem.  Każdy element runbook w automatyzacji ma wersję roboczą i opublikowaną wersję. Opublikowana wersja jest dostępna do uruchamiania i tylko wersję roboczą można edytowane. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinna zostać udostępniona, należy ją opublikować, co powoduje nadpisanie wersji opublikowanej wersją roboczą.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Aby opublikować element runbook przy użyciu portalu Azure
1. Otwórz element runbook w portalu Azure.
2. Kliknij przycisk **Edytuj** przycisku.
3. Kliknij przycisk **publikowania** przycisk, a następnie **tak** w komunikacie weryfikacyjnym.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Aby opublikować element runbook za pomocą środowiska Windows PowerShell
Można użyć [AzureRmAutomationRunbook publikowania](https://msdn.microsoft.com/library/mt603705.aspx) polecenia cmdlet, aby opublikować element runbook za pomocą środowiska Windows PowerShell. Następujące przykładowe polecenia pokazują, jak opublikować przykładowego elementu runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się, jak można korzystać z elementu Runbook i galerii modułu programu PowerShell, zobacz [galerie elementów Runbook i modułów w automatyzacji Azure](automation-runbook-gallery.md)
* Aby dowiedzieć się więcej na temat edytowania elementów runbook programu PowerShell i przepływ pracy programu PowerShell za pomocą edytora tekstową, zobacz [edycji tekstową elementy runbook automatyzacji Azure](automation-edit-textual-runbook.md)
* Aby dowiedzieć się więcej na temat tworzenia graficznego elementu runbook, zobacz [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md)

