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
ms.openlocfilehash: 7495c6b114e232a9aad0075e173abebcb3c92cd0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273598"
---
# <a name="variable-assets-in-azure-automation"></a>Zmienne zasoby w Azure Automation

Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC na koncie usługi Automation. Można nimi zarządzać z poziomu Azure Portal, z programu PowerShell, w ramach elementu Runbook lub konfiguracji DSC.

Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości wśród wielu elementów Runbook lub konfiguracji DSC.

- Udostępnianie wartości między wieloma zadaniami z tego samego elementu Runbook lub konfiguracji DSC.

- Zarządzanie wartością używaną przez elementy Runbook lub konfiguracje DSC z poziomu portalu lub wiersza polecenia programu PowerShell. Przykładem jest zestaw typowych elementów konfiguracji, takich jak określona lista nazw maszyn wirtualnych, określona grupa zasobów, nazwa domeny usługi AD i wiele innych.  

Ponieważ zmienne automatyzacji są utrwalane, są dostępne nawet wtedy, gdy konfiguracja elementu Runbook lub konfiguracji DSC zakończy się niepowodzeniem. Takie zachowanie umożliwia jednemu elementowi Runbook lub konfiguracji DSC ustawienie wartości, która jest używana przez inny element Runbook, lub przez ten sam plik Runbook lub konfiguracji DSC przy następnym uruchomieniu.

Podczas tworzenia zmiennej można określić jej szyfrowanie i magazyn, Azure Automation jako bezpieczny zasób. Inne zabezpieczane zasoby obejmują poświadczenia, certyfikaty i połączenia. Azure Automation szyfruje te zasoby i zapisuje je przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości Azure Automation ładuje klucz z Key Vault a następnie używa go do zaszyfrowania elementu zawartości. 

Azure Automation przechowuje każdą zaszyfrowaną zmienną bezpiecznie. Nie można pobrać jej wartości za pomocą polecenia cmdlet [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) , które jest dostarczane jako część modułu Azure PowerShell. Jedynym sposobem na pobranie zaszyfrowanej wartości jest użycie działania **Get-AutomationVariable** w elemencie Runbook lub konfiguracji DSC.

>[!NOTE]
>Jeśli chcesz usunąć szyfrowanie dla zmiennej, należy usunąć zmienną i utworzyć ją ponownie jako niezaszyfrowaną.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej przy użyciu Azure Portal należy określić typ danych na liście rozwijanej, aby Portal mógł wyświetlić odpowiednią kontrolkę do wprowadzania wartości zmiennej. Następujące typy zmiennych są dostępne w Azure Automation:

* Ciąg
* Liczba całkowita
* DateTime
* Wartość logiczna
* Null

Zmienna nie jest ograniczona do wyznaczono typu danych. Należy ustawić zmienną przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli nie zostanie **określona**, wartość zmiennej jest równa **null**i należy ustawić wartość przy użyciu polecenia cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) lub działania **Set-AutomationVariable** .

Nie można użyć portalu do utworzenia lub zmiany wartości złożonego typu zmiennej. Można jednak podać wartość dowolnego typu przy użyciu programu Windows PowerShell. Typy złożone są pobierane jako [parametr PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można przechowywać wiele wartości w pojedynczej zmiennej przez utworzenie tablicy lub Hashtable i zapisanie jej do zmiennej.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Polecenia cmdlet programu PowerShell, które tworzą i zarządzają zmiennymi zasobów

Polecenia AZ module w poniższej tabeli służą do tworzenia zasobów zmiennych automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AZ. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

| Polecenie cmdlet | Opis |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Pobiera wartość istniejącej zmiennej.|
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Usuwa istniejącą zmienną.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ustawia wartość istniejącej zmiennej.|

## <a name="activities-to-access-variables"></a>Działania dotyczące dostępu do zmiennych

Działania w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elementach Runbook i konfiguracjach DSC. Różnica między elementami **Get-AzAutomationVariable** i **Get-AutomationVariable** została omówiona w przypadku zmiennych szyfrowanych na początku tego artykułu.

| Działanie | Opis |
|:---|:---|
|**Get-AutomationVariable**|Pobiera wartość istniejącej zmiennej.|
|**Set-AutomationVariable**|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania zmiennych w parametrze *name* polecenia **Get-AutomationVariable** w elemencie Runbook lub konfiguracji DSC. Użycie tego parametru może komplikuje odnajdywanie zależności między elementami Runbook lub konfiguracjami DSC a zmiennymi automatyzacji w czasie projektowania.

Funkcje w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elemencie Runbook python2 i pobierania ich.

|Funkcje python2|Opis|
|:---|:---|
|automationassets.get_automation_variable|Pobiera wartość istniejącej zmiennej. |
|automationassets.set_automation_variable|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł **automationassets** w górnej części elementu Runbook języka Python.

## <a name="creating-a-new-automation-variable"></a>Tworzenie nowej zmiennej automatyzacji

### <a name="create-a-new-variable-using-the-azure-portal"></a>Tworzenie nowej zmiennej przy użyciu Azure Portal

1. Na koncie usługi Automation kliknij kafelek **zasoby** , a następnie w bloku **zasoby** wybierz pozycję **zmienne**.
2. Na kafelku **zmienne** wybierz pozycję **Dodaj zmienną**.
3. Uzupełnij opcje w bloku **Nowa zmienna** , a następnie kliknij przycisk **Utwórz** , aby zapisać nową zmienną.

### <a name="create-a-new-variable-with-windows-powershell"></a>Tworzenie nowej zmiennej przy użyciu programu Windows PowerShell

Skrypt używa polecenia cmdlet **New-AzAutomationVariable** w celu utworzenia nowej zmiennej i ustawienia jej początkowej wartości. Następnie może pobrać wartość przy użyciu polecenia **Get-AzAutomationVariable**. Jeśli wartość jest typu prostego, wówczas pobierany jest ten sam typ. Jeśli jest to typ złożony, pobierany jest typ **parametr PSCustomObject** .

Poniższy przykład pokazuje, jak utworzyć zmienną typu String, a następnie zwrócić jej wartość.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Poniższy przykład pokazuje, jak utworzyć zmienną typu złożonego, a następnie pobrać jej właściwości. W tym przypadku jest używany obiekt maszyny wirtualnej z elementu [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Używanie zmiennej w elemencie Runbook lub konfiguracji DSC

Użyj działania **Set-AutomationVariable** , aby ustawić wartość zmiennej automatyzacji w elemencie Runbook programu PowerShell lub konfiguracji DSC, i polecenie **Get-AutomationVariable** , aby je pobrać. Nie należy używać poleceń cmdlet **Set-AzAutomationVariable** i **Get-AzAutomationVariable** ani ich odpowiedników modułów AzureRM w elemencie Runbook lub konfiguracji DSC, ponieważ są one mniej wydajne niż działania przepływu pracy. 

Należy zauważyć, że nie można pobrać wartości bezpiecznej zmiennej z parametrem **Get-AzAutomationVariable** lub jego odpowiednikiem modułu AzureRM. 

Jedynym sposobem utworzenia nowej zmiennej z poziomu elementu Runbook lub konfiguracji DSC jest użycie polecenia cmdlet **New-AzAutomationVariable** .

### <a name="textual-runbook-samples"></a>Przykładowe elementy Runbook

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostej wartości ze zmiennej

Następujące przykładowe polecenia pokazują, jak ustawić i pobrać zmienną w tekstowym elemencie Runbook. W tym przykładzie przyjęto założenie, że tworzone są zmienne całkowite o nazwach *NumberOfIterations* i *NumberOfRunnings* oraz zmienna ciągu o nazwie *SampleMessage*.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Ustawianie i pobieranie zmiennej w elemencie Runbook python2

Poniższy przykład pokazuje, jak używać zmiennej, ustawiać zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elemencie Runbook python2.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Graficzne przykłady elementów Runbook

W graficznym elemencie Runbook można dodać działanie **Get-AutomationVariable** lub **Set-AutomationVariable** . Po prostu kliknij prawym przyciskiem myszy zmienną w okienku Biblioteka w edytorze graficznym i wybierz odpowiednie działanie.

![Dodaj zmienną do kanwy](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ustawianie wartości w zmiennej

Na poniższej ilustracji przedstawiono przykładowe działania służące do zaktualizowania zmiennej o prostej wartości w graficznym elemencie Runbook. W tym przykładzie polecenie **Get-AzVM** pobiera pojedynczą maszynę wirtualną platformy Azure i zapisuje nazwę komputera w istniejącej zmiennej automatyzacji z typem ciągu. Nie ma znaczenia, czy [łącze jest potokiem, czy sekwencją](../automation-graphical-authoring-intro.md#links-and-workflow) , ponieważ kod oczekuje tylko pojedynczego obiektu w danych wyjściowych.

![Ustaw prostą zmienną](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat łączenia działań w tworzeniu grafiki, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow).
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [mój pierwszy graficzny element Runbook](../automation-first-runbook-graphical.md).
