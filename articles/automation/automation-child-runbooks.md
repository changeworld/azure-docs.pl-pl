---
title: Podrzędne elementy Runbook w Azure Automation
description: Opisuje różne metody uruchamiania elementu Runbook w Azure Automation z innego elementu Runbook i udostępniania informacji między nimi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a35ee69e6a167f4907294c88710d0484353d4cb2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367013"
---
# <a name="child-runbooks-in-azure-automation"></a>Podrzędne elementy Runbook w Azure Automation

Zaleca się, aby Azure Automation do zapisu z wielokrotnego użytku, modularnych elementów Runbook z dyskretną funkcją, która jest przez inne elementy Runbook. Nadrzędny element Runbook często wywołuje jeden lub więcej podrzędnych elementów Runbook w celu wykonania wymaganych funkcji. Istnieją dwa sposoby wywoływania podrzędnego elementu Runbook, a każdy z nich ma odrębne różnice, które należy zrozumieć, aby można było określić, który jest najlepszy dla różnych scenariuszy.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego elementu Runbook przy użyciu wykonywania wbudowanego

Aby śródwierszowo wywołać element runbook z innego elementu runbook, użyj nazwy elementu runbook i podaj wartości jego parametrów, dokładnie tak, należy użyć działania lub polecenia cmdlet.  Wszystkie elementy Runbook w tym samym koncie usługi Automation są dostępne dla wszystkich innych, aby można było ich używać w ten sposób. Nadrzędny element runbook będzie czekać podrzędnego elementu runbook do wykonania przed przejściem do następnego wiersza, a wszelkie dane wyjściowe zostaną zwrócone bezpośrednio do obiektu nadrzędnego.

Gdy użytkownik śródwierszowo wywołać element runbook, jest ono wykonywane w tym samym zadaniu co nadrzędny element runbook. W historii zadań podrzędnego elementu Runbook, który został uruchomiony, nie ma żadnego wskazania. Wszystkie wyjątki i wszystkie dane wyjściowe strumienia z podrzędnego elementu Runbook są skojarzone z elementem nadrzędnym. Takie zachowanie skutkuje mniejszą liczbą zadań i ułatwia ich śledzenie i rozwiązywanie problemów, ponieważ wszelkie wyjątki zgłoszone przez podrzędny element Runbook i wszystkie dane wyjściowe strumienia są skojarzone z zadaniem nadrzędnym.

Po opublikowaniu elementu Runbook wszystkie podrzędne elementy Runbook, które wywołuje, muszą już być opublikowane. Dzieje się tak, ponieważ Azure Automation kompiluje skojarzenie z dowolnymi podrzędnymi elementami Runbook po skompilowaniu elementu Runbook. Jeśli nie, zostanie wyświetlony stan nadrzędny elementu Runbook, który zostanie prawidłowo opublikowany, ale wygeneruje wyjątek, gdy zostanie on uruchomiony. W takim przypadku można ponownie opublikować nadrzędny element Runbook, aby prawidłowo odwoływać się do podrzędnych elementów Runbook. Nie trzeba ponownie publikować nadrzędnego elementu Runbook, jeśli którykolwiek z podrzędnych elementów Runbook zostanie zmieniony, ponieważ skojarzenie zostało już utworzone.

Parametry podrzędnego elementu Runbook o nazwie inline mogą być dowolnego typu danych, w tym obiektów złożonych. Nie ma [serializacji JSON](start-runbooks.md#runbook-parameters) , ponieważ jest uruchamiany element Runbook przy użyciu Azure Portal lub polecenia cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy elementów Runbook

Które typy mogą się wzajemnie wywoływać:

* [Element Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) mogą wywołać każdą inną wbudowaną (obie są oparte na programie PowerShell).
* [Elementy Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i graficzne elementy Runbook przepływu pracy programu PowerShell mogą wywołać każdą inną wbudowaną (obie są oparte na przepływie pracy programu PowerShell)
* Typy programu PowerShell i typy przepływów pracy programu PowerShell nie mogą wywoływać siebie nawzajem i muszą używać polecenia Start-AzureRmAutomationRunbook.

Gdy publikuje się zamówienie:

* Kolejność publikowania elementów Runbook dotyczy tylko przepływu pracy programu PowerShell i graficznych elementów Runbook przepływu pracy programu PowerShell.

W przypadku wywołania graficznego elementu Runbook programu PowerShell lub przepływu pracy przy użyciu funkcji wykonywania wbudowanego należy użyć nazwy elementu Runbook.  Gdy wywołujesz podrzędny element Runbook programu PowerShell, musisz uruchomić jego nazwę z *.\\* , aby określić, że skrypt znajduje się w katalogu lokalnym.

### <a name="example"></a>Przykład

W poniższym przykładzie jest uruchamiany testowy podrzędny element Runbook, który akceptuje trzy parametry, obiekt złożony, liczbę całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu runbook jest przypisany do zmiennej.  W takim przypadku podrzędny element Runbook jest elementem Runbook przepływu pracy programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Poniżej znajduje się ten sam przykład użycia elementu Runbook programu PowerShell jako elementu podrzędnego.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Uruchamianie podrzędnego elementu Runbook za pomocą polecenia cmdlet

> [!IMPORTANT]
> Jeśli wywołujesz podrzędny element Runbook za pomocą polecenia cmdlet `Start-AzureRmAutomationRunbook` z przełącznikiem `-Wait`, a wyniki podrzędnego elementu Runbook są obiektem, mogą wystąpić błędy. Aby obejść ten błąd, zobacz [podrzędne elementy Runbook z danymi wyjściowymi obiektów](troubleshoot/runbooks.md#child-runbook-object) , aby dowiedzieć się, jak wdrożyć logikę w celu sondowania wyników i użyć polecenia [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Można użyć polecenia cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) , aby uruchomić element Runbook, zgodnie z opisem w artykule [Aby uruchomić element Runbook za pomocą programu Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Istnieją dwa tryby użycia tego polecenia cmdlet.  W jednym trybie polecenie cmdlet zwraca identyfikator zadania, gdy tworzone jest zadanie podrzędne dla podrzędnego elementu Runbook.  W innym trybie włączonym przez określenie parametru **-wait** polecenie cmdlet czeka, aż zakończy się zadanie podrzędne i zwróci dane wyjściowe z podrzędnego elementu Runbook.

Zadanie z podrzędnego elementu Runbook uruchomionego za pomocą polecenia cmdlet jest uruchamiane w osobnym zadaniu z nadrzędnego elementu Runbook. Takie zachowanie skutkuje większymi zadaniami niż Uruchamianie elementu Runbook w tekście i utrudnia ich śledzenie. Element nadrzędny może uruchomić więcej niż jeden podrzędny element Runbook, asynchronicznie bez oczekiwania na ukończenie każdego z nich. Dla tego samego rodzaju równoległego wywoływania podrzędnych elementów runbook wbudowanej nadrzędny element runbook musiałby użyć [słowa kluczowego parallel](automation-powershell-workflow.md#parallel-processing).

Dane wyjściowe podrzędnych elementów Runbook nie są w sposób wiarygodny zwracane do nadrzędnego elementu Runbook z powodu chronometrażu. Niektóre zmienne, takie jak $VerbosePreference, $WarningPreference i inne, mogą nie być propagowane do podrzędnych elementów Runbook. Aby uniknąć tych problemów, można uruchomić podrzędne elementy Runbook jako oddzielne zadania automatyzacji za pomocą polecenia cmdlet `Start-AzureRmAutomationRunbook` z przełącznikiem `-Wait`. Spowoduje to zablokowanie nadrzędnego elementu Runbook do momentu ukończenia podrzędnego elementu Runbook.

Jeśli nie chcesz, aby nadrzędny element Runbook był blokowany podczas oczekiwania, możesz uruchomić podrzędny element Runbook za pomocą polecenia cmdlet `Start-AzureRmAutomationRunbook` bez przełącznika `-Wait`. Następnie należy użyć `Get-AzureRmAutomationJob`, aby czekać na ukończenie zadania, a następnie `Get-AzureRmAutomationJobOutput` i `Get-AzureRmAutomationJobOutputRecord`, aby pobrać wyniki.

Parametry dla podrzędnego elementu runbook, pracę za pośrednictwem polecenia cmdlet są przekazywane jako tablica skrótów, zgodnie z opisem w [parametry elementu Runbook](start-runbooks.md#runbook-parameters). Można używać tylko prostych typów danych. Jeśli element runbook ma parametr o złożonym typie danych, następnie go musi być wywoływany śródwierszowo.

Kontekst subskrypcji może zostać utracony podczas uruchamiania podrzędnych elementów Runbook jako oddzielnych zadań. Aby podrzędny element Runbook wykonywał polecenia cmdlet usługi Azure RM względem określonej subskrypcji platformy Azure, podrzędny element Runbook musi uwierzytelniać się w tej subskrypcji niezależnie od nadrzędnego elementu Runbook.

Jeśli zadania w ramach tego samego konta usługi Automation działają z więcej niż jedną subskrypcją, wybranie subskrypcji w jednym zadaniu może zmienić bieżący kontekst subskrypcji dla innych zadań. Aby uniknąć tego problemu, użyj `Disable-AzureRmContextAutosave –Scope Processsave` na początku każdego elementu Runbook. Ta akcja powoduje zapisanie tylko kontekstu dla tego wykonywania elementu Runbook.

### <a name="example"></a>Przykład

Poniższy przykład uruchamia podrzędny element Runbook z parametrami, a następnie czeka na jego ukończenie przy użyciu parametru Start-AzureRmAutomationRunbook-wait. Po zakończeniu jego dane wyjściowe są zbierane z podrzędnego elementu Runbook. Aby korzystać z `Start-AzureRmAutomationRunbook`, musisz uwierzytelnić się w ramach subskrypcji platformy Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego elementu Runbook

Poniższa tabela zawiera podsumowanie różnic między obiema metodami wywoływania elementu runbook z innego elementu runbook.

|  | wbudowane | Polecenie cmdlet |
|:--- |:--- |:--- |
| Zadanie |Podrzędne elementy runbook uruchomione w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie podrzędnego elementu runbook. |
| Wykonanie |Nadrzędny element runbook czeka, aż podrzędnego elementu runbook, które należy wykonać przed kontynuowaniem. |Nadrzędny element Runbook jest kontynuowany natychmiast po uruchomieniu podrzędnego elementu Runbook *lub* nadrzędny element Runbook czeka na zakończenie zadania podrzędnego. |
| Dane wyjściowe |Nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |Nadrzędny element Runbook musi pobrać dane wyjściowe z podrzędnego zadania elementu Runbook *lub* nadrzędnego elementu Runbook można bezpośrednio pobrać dane wyjściowe z podrzędnego elementu Runbook. |
| Parametry |Wartości parametrów podrzędnego elementu runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego elementu Runbook muszą być połączone w jedną tablicę skrótów. Ta tablica skrótów może zawierać tylko typy danych Simple, Array i Object, które używają serializacji JSON. |
| Konto usługi Automation |Nadrzędny element Runbook może używać tylko podrzędnego elementu Runbook na tym samym koncie usługi Automation. |Nadrzędne elementy Runbook mogą używać podrzędnego elementu Runbook z dowolnego konta usługi Automation w ramach tej samej subskrypcji platformy Azure, a nawet innej subskrypcji, z którą masz połączenie. |
| Publikowanie |Podrzędny element runbook należy opublikować przed opublikowaniem nadrzędnego elementu runbook. |Podrzędny element Runbook należy opublikować w dowolnym momencie przed uruchomieniem nadrzędnego elementu Runbook. |

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md)
* [Dane wyjściowe i komunikaty elementu Runbook w Azure Automation](automation-runbook-output-and-messages.md)

