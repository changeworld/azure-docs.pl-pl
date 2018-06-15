---
title: Zasoby zmiennej usługi Automatyzacja Azure
description: Zmienna zasoby są wartości, które są dostępne dla wszystkich elementów runbook i konfiguracji DSC automatyzacji Azure.  W tym artykule szczegółowo opisano zmienne i sposobu pracy z nimi w tworzeniu zarówno tekstową i graficznego.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea6aae349bfbec0d1b6538010df42e7a0fb22d8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196104"
---
# <a name="variable-assets-in-azure-automation"></a>Zasoby zmiennej usługi Automatyzacja Azure

Zmienna zasoby są wartości, które są dostępne dla wszystkich elementów runbook i konfiguracji DSC na Twoim koncie automatyzacji. Można je utworzyć, zmodyfikować i pobrać z portalu Azure, programu Windows PowerShell, a za pomocą elementu runbook lub konfiguracji DSC. Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnienie wartości dla wielu elementów runbook lub konfiguracji DSC.

- Udostępnienie wartości dla wielu zadań z tego samego elementu runbook lub konfiguracji DSC.

- Zarządzanie wartością z portalu lub wiersz polecenia programu Windows PowerShell, który jest używany przez elementy runbook lub konfiguracji DSC, takiego jak zestaw wspólne elementy konfiguracji, takie jak określonej listy nazw maszyn wirtualnych określonej grupy zasobów, nazwa domeny usługi AD, itp.  

Zmienne automatyzacji są trwałe, dlatego są nadal dostępne nawet wtedy, gdy element runbook lub konfiguracji DSC nie powiedzie się. Umożliwia to także wartości określonych przez jeden element runbook, który następnie jest używany przez inny lub jest używany przez ten sam element runbook lub konfiguracji DSC przy następnym uruchomieniu.

Po utworzeniu zmiennej można określić, że jest on przechowywany zaszyfrowany. Gdy zmienna jest zaszyfrowana, jest bezpiecznie przechowywana w automatyzacji Azure i jego wartość nie można pobrać z [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) polecenia cmdlet, które wchodzi w skład modułu Azure PowerShell. Jest jedynym sposobem zaszyfrowaną wartość mogą być pobierane z **Get-AutomationVariable** działania elementu runbook lub konfiguracji DSC.

>[!NOTE]
>Bezpiecznych zasobów w automatyzacji Azure obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w automatyzacji Azure za pomocą Unikatowy klucz, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem zabezpieczonym zasobem, klucz jest załadowany z magazynu kluczy i następnie używany do szyfrowania elementu zawartości.

## <a name="variable-types"></a>Typy zmiennych

Po utworzeniu zmiennej z portalu Azure, należy określić typ danych z listy rozwijanej, portalu można wyświetlić odpowiednią kontrolkę dla wartości zmiennej. Zmienna nie jest ograniczona do tego typu danych, ale należy ustawić przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu zmienną. Jeśli określisz **nie zdefiniowano**, ma ustawioną wartość zmiennej, a następnie **$null**, i należy ustawić wartość z [AzureRMAutomationVariable zestaw](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) polecenia cmdlet lub **Set-AutomationVariable** działania. Nie można utworzyć lub zmień wartość dla typu zmienną złożone w portalu, ale można podać wartości typu przy użyciu programu Windows PowerShell. Typy złożone są zwracane jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można przechowywać wielu wartości w pojedynczej zmiennej, tworząc tablicę lub tablicę skrótów i zapisać go do zmiennej.

Poniżej przedstawiono listę typów zmiennych, które są dostępne w automatyzacji:

* Ciąg
* Liczba całkowita
* DateTime
* Wartość logiczna
* Null

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu AzureRM PowerShell
Dla AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i zarządzania zasobami poświadczenie automatyzacji przy użyciu programu Windows PowerShell. One dostarczane jako część [modułu AzureRM.Automation](/powershell/azure/overview) która jest dostępna na potrzeby automatyzacji elementów runbook i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Pobiera wartość istniejącej zmiennej.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Usuwa istniejącej zmiennej.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Ustawia wartość istniejącej zmiennej.|

## <a name="activities"></a>Działania
Działania w poniższej tabeli umożliwiają dostęp do poświadczeń w elemencie runbook i konfiguracji DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationVariable|Pobiera wartość istniejącej zmiennej.|
|Set-AutomationVariable|Ustawia wartość istniejącej zmiennej.|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze-Name z **Get-AutomationVariable** runbook lub konfiguracji DSC, ponieważ może to skomplikować wykrywanie zależności między elementów runbook lub konfiguracji DSC automatyzacji zmienne w czasie projektowania.

Funkcje w poniższej tabeli są używane do uzyskania dostępu i pobrać zmiennych w elemencie runbook Python2. 

|Funkcje Python2|Opis|
|:---|:---|
|automationassets.get_automation_variable|Pobiera wartość istniejącej zmiennej. |
|automationassets.set_automation_variable|Ustawia wartość istniejącej zmiennej. |

> [!NOTE] 
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować modułu "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-automation-variable"></a>Tworzenie nowej zmiennej automatyzacji

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Aby utworzyć nową zmienną za pomocą portalu Azure

1. Twoje konto usługi Automatyzacja, kliknij **zasoby** Kafelek, a następnie na **zasoby** bloku, wybierz opcję **zmienne**.
2. Na **zmienne** kafelka, wybierz opcję **dodać zmienną**.
3. Ustaw opcje na **nową zmienną** bloku i kliknij przycisk **Utwórz** zapisać nową zmienną.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Aby utworzyć nową zmienną za pomocą środowiska Windows PowerShell

[AzureRmAutomationVariable nowy](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) polecenie cmdlet tworzy nową zmienną i ustawia wartość początkową. Można pobrać przy użyciu wartości [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Jeśli wartość jest typu prostego, zwracany jest tego samego typu. Jeśli jest to typ złożony, a następnie **PSCustomObject** jest zwracany.

W następujących przykładowych poleceniach pokazano, jak utworzyć zmienną typu ciąg, a następnie wróć jej wartość.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

W następujących przykładowych poleceniach pokazano, jak utworzyć zmienną typu złożonego, a następnie wróć jego właściwości. W takim przypadku obiekt maszyny wirtualnej z **Get-AzureRmVm** jest używany.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Użycie zmiennej w element runbook lub konfiguracji DSC

Użyj **Set-AutomationVariable** działanie w celu ustawienia wartości zmiennej automatyzacji elementu runbook programu PowerShell lub konfiguracji DSC i **Get-AutomationVariable** można go pobrać. Nie można używać **AzureRMAutomationVariable zestaw** lub **Get-AzureRMAutomationVariable** polecenia cmdlet w element runbook lub konfiguracji DSC, ponieważ są mniej wydajne niż działania przepływu pracy. Również nie można pobrać wartości zmiennych bezpiecznego z **Get-AzureRMAutomationVariable**. Jedynym sposobem, aby utworzyć nową zmienną z elementu runbook lub konfiguracji DSC jest użycie [AzureRMAutomationVariable nowy](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) polecenia cmdlet.


### <a name="textual-runbook-samples"></a>Przykłady tekstowy

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostych wartości zmiennych

Następujące przykładowe polecenia pokazują, jak ustawiania i pobierania zmiennej w tekstowy. W tym przykładzie zakłada się, że zmienne typu całkowitoliczbowego o nazwie *NumberOfIterations* i *NumberOfRunnings* oraz zmienna typu ciąg o nazwie *SampleMessage* zostały już utworzone.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Ustawianie i pobieranie obiekt złożony w zmiennej

Następujący przykładowy kod przedstawia sposób aktualizacji w tekstowy złożona wartość zmiennej. W tym przykładzie maszyny wirtualnej platformy Azure są pobierane z **Get-AzureVM** i zapisywane w istniejącej zmiennej automatyzacji.  Zgodnie z objaśnieniem w [typy zmiennych](#variable-types), to jest przechowywana jako PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

W poniższym kodzie wartość jest pobierane z zmiennej i używane do uruchamiania maszyny wirtualnej.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Ustawiania i pobierania kolekcję w zmiennej

Następujący przykładowy kod przedstawia sposób użycia zmienną z kolekcją złożonych wartości tekstowy. W tym przykładzie wiele maszyn wirtualnych platformy Azure są pobierane z **Get-AzureVM** i zapisywane w istniejącej zmiennej automatyzacji. Zgodnie z objaśnieniem w [typy zmiennych](#variable-types), to jest przechowywane jako zbiór PSCustomObjects.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

W poniższym kodzie kolekcji jest pobierane z zmiennej i używane do uruchamiania każdej maszyny wirtualnej.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ustawiania i pobierania zmiennej w Python2
Następujący przykładowy kod przedstawia sposób użyć zmiennej, ustaw zmienną i obsługi wyjątku dla zmiennej nie istnieje w elemencie runbook Python2.

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


### <a name="graphical-runbook-samples"></a>Przykłady graficznym elementem runbook

W graficznym elementem runbook, należy dodać **Get-AutomationVariable** lub **Set-AutomationVariable** zmiennej w okienku Biblioteka edytora graficznego prawym przyciskiem myszy i wybierając odpowiednie działanie.

![Dodaj zmienną do kanwy](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ustawianie wartości w zmiennej
Na poniższej ilustracji przedstawiono przykładowe działania można zaktualizować zmiennej o prostą wartością w graficznym elementem runbook. W tym przykładzie jednej maszyny wirtualnej platformy Azure są pobierane z **Get-AzureRmVM** i nazwa komputera jest zapisywane w istniejącej zmiennej automatyzacji z typu String. Nie ma znaczenia, czy [jest link potoku lub sekwencji](automation-graphical-authoring-intro.md#links-and-workflow) ponieważ oczekujesz tylko jeden obiekt w danych wyjściowych.

![Zestaw prostej zmiennej](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o łączenie działań w tworzeniu graficzny, zobacz [łącza w tworzeniu graficznego](automation-graphical-authoring-intro.md#links-and-workflow)
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook). 
