---
title: Zmiennych elementów zawartości w usłudze Azure Automation
description: Zmiennych elementów zawartości są wartościami, które są dostępne dla wszystkich elementów runbook i konfiguracji DSC w usłudze Azure Automation.  W tym artykule opisano szczegóły zmiennych i sposób pracy z nimi w tworzeniu tekstową i graficznego.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 622b4ab41162a7858097f717a103878f05917cd3
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342158"
---
# <a name="variable-assets-in-azure-automation"></a>Zmiennych elementów zawartości w usłudze Azure Automation

Zmiennych elementów zawartości są wartościami, które są dostępne dla wszystkich elementów runbook i konfiguracji DSC na koncie usługi automation. Mogą być zarządzane w witrynie Azure portal, programu PowerShell, w ramach elementu runbook lub konfiguracji DSC. Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnienie wartości dla wielu elementów runbook lub konfiguracji DSC.

- Udostępnienie wartości dla wielu zadań z tego samego elementu runbook lub konfiguracji DSC.

- Zarządzanie wartością z portalu lub z wiersza polecenia programu PowerShell, który jest używany przez elementy runbook lub konfiguracji DSC, takich jak zestaw typowe elementy konfiguracji, takie jak określonej listy nazw maszyn wirtualnych, konkretnej grupy zasobów, nazwę domeny usługi AD i innych.  

Ponieważ zmienne automatyzacji są zachowywane, są one dostępne nawet wtedy, jeśli element runbook lub konfiguracji DSC nie powiedzie się. Takie zachowanie umożliwia wartości określonych przez jeden element runbook, który jest następnie używany przez inny lub jest używany przez ten sam element runbook lub konfiguracji DSC przy następnym uruchomieniu.

Po utworzeniu zmiennej możesz określić, czy jest on przechowywany zaszyfrowany. Szyfrowane zmienne są bezpiecznie przechowywane w usłudze Azure Automation, a jego wartość nie można pobrać z [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) polecenia cmdlet, który jest dostarczany jako część modułu Azure PowerShell. Jedynym sposobem czy zaszyfrowana wartość mogą być pobierane pochodzi z **Get-AutomationVariable** działania w elemencie runbook lub konfiguracji DSC. Jeśli chcesz zmienić niezaszyfrowanego szyfrowana zmienna może możesz należy usunąć i ponownie Utwórz zmienną jako niezaszyfrowanego.

>[!NOTE]
>Bezpiecznych zasobów w usłudze Azure Automation obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są zaszyfrowane i przechowywane w usłudze Azure Automation za pomocą Unikatowy klucz, który jest generowany dla każdego konta usługi automation. Ten klucz jest przechowywany w systemie zarządzane usługi Key Vault. Przed zapisaniem zabezpieczonym zasobem, klucz jest ładowane z usługi Key Vault i następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzane przez usługę Azure Automation.

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej za pomocą witryny Azure portal, należy określić typu danych z listy rozwijanej, więc portalu może wyświetlić odpowiednią kontrolkę wprowadzania wartości zmiennej. Zmienna nie jest ograniczona do tego typu danych. Należy ustawić zmiennej za pomocą programu Windows PowerShell, jeśli chcesz określić wartości innego typu. Jeśli określisz **Niezdefiniowany**, a następnie ustawia wartość zmiennej **$null**, i należy ustawić wartość z [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) polecenia cmdlet lub **Set-AutomationVariable** działania. Nie można utworzyć ani zmienić wartość dla typu zmiennej złożone w portalu, ale można podać wartość dowolnego typu za pomocą programu Windows PowerShell. Typy złożone są zwracane jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Możesz przechowywać wiele wartości w pojedynczej zmiennej, tworząc tablicę lub tablicę skrótów i zapisując go do zmiennej.

Poniżej przedstawiono listę typów zmiennych, które są dostępne w usłudze Automation:

* String
* Integer
* DateTime
* Boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet usługi AzureRM PowerShell

Dla usługi AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i obsługi zasobów poświadczeń usługi automation przy użyciu programu Windows PowerShell. Są dostarczane jako część systemu [modułu z wersjami AzureRM.Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Pobiera wartość istniejącej zmiennej.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Usuwa istniejącą zmienną.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Ustawia wartość istniejącej zmiennej.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do dostępu do poświadczeń w elemencie runbook i konfiguracjach DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationVariable|Pobiera wartość istniejącej zmiennej.|
|Set-AutomationVariable|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania zmiennych w parametrze Name **Get-AutomationVariable** w elemencie runbook lub konfiguracji DSC, ponieważ może to skomplikować wykrywanie zależności między elementami runbook lub konfiguracji DSC i automatyzacji zmienne w czasie projektowania.

Funkcje w poniższej tabeli są używane do uzyskania dostępu i pobierania zmiennych w element runbook programu Python2.

|Funkcje Python2|Opis|
|:---|:---|
|automationassets.get_automation_variable|Pobiera wartość istniejącej zmiennej. |
|automationassets.set_automation_variable|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-automation-variable"></a>Tworzenie nowej zmiennej automatyzacji

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Aby utworzyć nową zmienną za pomocą witryny Azure portal

1. Na koncie usługi Automation kliknij **zasoby** Kafelek a następnie na **zasoby** bloku wybierz **zmienne**.
2. Na **zmienne** kafelka, wybierz opcję **Dodaj zmienną**.
3. Wypełnij odpowiednie opcje na **nową zmienną** bloku i kliknij przycisk **Utwórz** Zapisz nową zmienną.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Aby utworzyć nową zmienną za pomocą programu Windows PowerShell

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) polecenie cmdlet tworzy nową zmienną i ustawia jej wartość początkową. Możesz pobrać przy użyciu wartości [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Jeśli wartość jest typu prostego, zwracany jest tego samego typu. Jeśli jest to typ złożony, a następnie **PSCustomObject** jest zwracana.

Następujące przykładowe polecenia pokazują, jak utworzyć zmienną typu ciąg, a następnie zwracają wartość.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Następujące przykładowe polecenia pokazują, jak utworzyć zmienną o typie złożonym, a następnie wróć jego właściwości. W tym przypadku obiekt maszyny wirtualnej z **Get-AzureRmVm** jest używany.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Używanie zmiennej w elemencie runbook lub konfiguracji DSC

Użyj **Set-AutomationVariable** działania, aby ustawić wartość zmiennej automatyzacji elementu runbook programu PowerShell lub konfiguracji DSC i **Get-AutomationVariable** można go pobrać. Nie używaj **Set-AzureRMAutomationVariable** lub **Get-AzureRMAutomationVariable** polecenia cmdlet w elemencie runbook lub konfiguracji DSC, ponieważ są one mniej wydajne niż działań przepływu pracy. Również nie można pobrać wartości zmiennych bezpieczny z **Get-AzureRMAutomationVariable**. Jedynym sposobem, aby utworzyć nową zmienną z w ramach elementu runbook lub konfiguracji DSC jest użycie [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) polecenia cmdlet.

### <a name="textual-runbook-samples"></a>Przykłady tekstowych elementów runbook

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostych wartość ze zmiennej

Następujące przykładowe polecenia pokazują, jak do ustawiania i pobierania zmiennej w elemencie runbook tekstową. W tym przykładzie zakłada się, że zmienne typu całkowitoliczbowego o nazwie *NumberOfIterations* i *NumberOfRunnings* oraz zmienna typu ciąg o nazwie *SampleMessage* mają został utworzony.

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

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ustawiania i pobierania zmiennej w Python2

Poniższy przykład kodu pokazuje, jak użyć zmiennej, ustaw zmienną i obsługi dla zmiennej nie istnieje w elemencie runbook Python2 wyjątku.

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

### <a name="graphical-runbook-samples"></a>Przykłady graficznego elementu runbook

W graficznego elementu runbook, należy dodać **Get-AutomationVariable** lub **Set-AutomationVariable** klikając prawym przyciskiem myszy na zmiennej w okienku Biblioteka edytor graficzny i wybierając polecenie działania możesz chcesz.

![Dodaj zmienną do kanwy](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ustawianie wartości w zmiennej

Na poniższej ilustracji przedstawiono przykład działań, aby zaktualizować zmienną wartością prostego graficznego elementu runbook. W tym przykładzie **Get-AzureRmVM** pobiera pojedynczej maszyny wirtualnej platformy Azure i nazwę komputera zapisuje zmienna usługi Automation z typem ciągu. Nie ma znaczenia, czy [łącze jest potok lub sekwencji](../automation-graphical-authoring-intro.md#links-and-workflow) ponieważ oczekujesz tylko jeden obiekt w danych wyjściowych.

![Ustaw zmienną prosty](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat łączenia działań razem w tworzenia elementów graficznych, zobacz [łącza w tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow)
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](../automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
