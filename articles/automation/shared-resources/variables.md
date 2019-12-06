---
title: Zmienne zasoby w Azure Automation
description: Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC w Azure Automation.  W tym artykule wyjaśniono szczegółowe informacje o zmiennych i sposobach pracy z nimi w tworzeniu tekstowych i graficznych.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e56a1c9a158974266b810d31a0e9bb898262761a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849432"
---
# <a name="variable-assets-in-azure-automation"></a>Zmienne zasoby w Azure Automation

Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC na koncie usługi Automation. Można nimi zarządzać za pomocą Azure Portal, programu PowerShell, w ramach elementu Runbook lub konfiguracji DSC. Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości między wieloma elementami Runbook lub konfiguracjami DSC.

- Udostępnij wartość między wieloma zadaniami z tego samego elementu Runbook lub konfiguracji DSC.

- Zarządzaj wartością z portalu lub z wiersza polecenia programu PowerShell, który jest używany przez elementy Runbook lub konfiguracje DSC, takie jak zestaw typowych elementów konfiguracji, takich jak określona lista nazw maszyn wirtualnych, określona grupa zasobów, nazwa domeny usługi AD i wiele więcej.  

Ponieważ zmienne automatyzacji są utrwalane, są dostępne nawet wtedy, gdy konfiguracja elementu Runbook lub konfiguracji DSC zakończy się niepowodzeniem. Takie zachowanie umożliwia ustawienie wartości przez jeden element Runbook, który jest następnie używany przez inny lub jest używany przez ten sam element Runbook lub Konfiguracja DSC przy następnym uruchomieniu.

Po utworzeniu zmiennej można określić, że jest ona zaszyfrowana. Zaszyfrowane zmienne są bezpiecznie przechowywane w Azure Automation i nie można pobrać jego wartości z polecenia cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) , które jest dostarczane jako część modułu Azure PowerShell. Jedynym sposobem pobrania zaszyfrowanej wartości jest działanie **Get-AutomationVariable** w elemencie Runbook lub konfiguracji DSC. Jeśli chcesz zmienić zaszyfrowaną zmienną na niezaszyfrowaną, możesz usunąć i ponownie utworzyć zmienną jako niezaszyfrowaną.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzany przez Azure Automation.

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej przy użyciu Azure Portal należy określić typ danych z listy rozwijanej, aby Portal mógł wyświetlić odpowiednią kontrolkę do wprowadzania wartości zmiennej. Zmienna nie jest ograniczona do tego typu danych. Należy ustawić zmienną przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli określisz **niezdefiniowane**, wartość zmiennej ustawia **$null**i należy ustawić wartość przy użyciu polecenia cmdlet [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) lub działania **Set-AutomationVariable** . Nie można utworzyć ani zmienić wartości typu złożonej zmiennej w portalu, ale można podać wartość dowolnego typu przy użyciu programu Windows PowerShell. Typy złożone są zwracane jako [parametr PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można przechowywać wiele wartości w pojedynczej zmiennej przez utworzenie tablicy lub Hashtable i zapisanie jej do zmiennej.

Poniżej przedstawiono listę typów zmiennych dostępnych w usłudze Automation:

* Ciąg
* Liczba całkowita
* Data i godzina
* Wartość logiczna
* Null

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell AzureRM

W przypadku AzureRM polecenia cmdlet w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AzureRM. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

| Polecenia cmdlet | Opis |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Pobiera wartość istniejącej zmiennej.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Usuwa istniejącą zmienną.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Ustawia wartość istniejącej zmiennej.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elementach Runbook i konfiguracjach DSC. Różnica między poleceniami cmdlet Get-AzureRmAutomationVariable i Get-AutomationVariable została wyjaśniona powyżej na początku tego dokumentu.

| Działania | Opis |
|:---|:---|
|Get-AutomationVariable|Pobiera wartość istniejącej zmiennej.|
|Set-AutomationVariable|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania zmiennych w parametrze-Name polecenia **Get-AutomationVariable** w elemencie Runbook lub konfiguracji DSC, ponieważ może to spowodować komplikację w odnajdywaniu zależności między elementami Runbook i konfiguracją DSC oraz zmiennymi automatyzacji w czasie projektowania.

Funkcje w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elemencie Runbook python2 i pobierania ich.

|Funkcje python2|Opis|
|:---|:---|
|automationassets.get_automation_variable|Pobiera wartość istniejącej zmiennej. |
|automationassets.set_automation_variable|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu Runbook języka Python.

## <a name="creating-a-new-automation-variable"></a>Tworzenie nowej zmiennej automatyzacji

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Aby utworzyć nową zmienną za pomocą Azure Portal

1. Na koncie usługi Automation kliknij kafelek **zasoby** , a następnie w bloku **zasoby** wybierz pozycję **zmienne**.
2. Na kafelku **zmienne** wybierz pozycję **Dodaj zmienną**.
3. Wypełnij opcje w bloku **Nowa zmienna** , a następnie kliknij przycisk **Utwórz** Zapisz nową zmienną.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Aby utworzyć nową zmienną za pomocą programu Windows PowerShell

Polecenie cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) tworzy nową zmienną i ustawia jej wartość początkową. Możesz pobrać wartość przy użyciu polecenia [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Jeśli wartość jest typu prostego, zwracany jest ten sam typ. Jeśli jest to typ złożony, zwracany jest **parametr PSCustomObject** .

Następujące przykładowe polecenia pokazują, jak utworzyć zmienną typu String, a następnie zwrócić jej wartość.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Następujące przykładowe polecenia pokazują, jak utworzyć zmienną typu złożonego, a następnie zwrócić jej właściwości. W tym przypadku jest używany obiekt maszyny wirtualnej z elementu **Get-AzureRmVm** .

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Używanie zmiennej w elemencie Runbook lub konfiguracji DSC

Użyj działania **Set-AutomationVariable** , aby ustawić wartość zmiennej automatyzacji w elemencie Runbook programu PowerShell lub konfiguracji DSC, i polecenie **Get-AutomationVariable** , aby je pobrać. Nie należy używać poleceń cmdlet **Set-AzureRMAutomationVariable** i **Get-AzureRMAutomationVariable** w konfiguracji elementu Runbook lub DSC, ponieważ są one mniej wydajne niż działania przepływu pracy. Nie można również pobrać wartości zabezpieczonych zmiennych przy użyciu elementu **Get-AzureRMAutomationVariable**. Jedynym sposobem utworzenia nowej zmiennej z poziomu elementu Runbook lub konfiguracji DSC jest użycie polecenia cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) .

### <a name="textual-runbook-samples"></a>Przykładowe elementy Runbook

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostej wartości ze zmiennej

Następujące przykładowe polecenia pokazują, jak ustawić i pobrać zmienną w tekstowym elemencie Runbook. W tym przykładzie zakłada się, że zostały utworzone zmienne typu Integer o nazwach *NumberOfIterations* i *NumberOfRunnings* oraz zmienna typu String o nazwie *SampleMessage* .

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ustawianie i pobieranie zmiennej w python2

Poniższy przykładowy kod pokazuje, jak używać zmiennej, ustawiać zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elemencie Runbook python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Graficzne przykłady elementów Runbook

W graficznym elemencie Runbook Dodaj polecenie **Get-AutomationVariable** lub **Set-AutomationVariable** , klikając prawym przyciskiem myszy zmienną w okienku Biblioteka w edytorze graficznym i wybierając żądane działanie.

![Dodaj zmienną do kanwy](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ustawianie wartości w zmiennej

Na poniższej ilustracji przedstawiono przykładowe działania służące do zaktualizowania zmiennej o prostej wartości w graficznym elemencie Runbook. W tym przykładzie polecenie **Get-AzureRmVM** pobiera pojedynczą maszynę wirtualną platformy Azure, a nazwa komputera jest zapisywana w istniejącej zmiennej automatyzacji z typem ciągu. Nie ma znaczenia, czy [łącze jest potokiem, czy sekwencją](../automation-graphical-authoring-intro.md#links-and-workflow) , ponieważ oczekiwano tylko pojedynczego obiektu w danych wyjściowych.

![Ustaw prostą zmienną](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat łączenia działań w tworzeniu grafiki, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow)
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](../automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
