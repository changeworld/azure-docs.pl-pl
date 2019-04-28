---
title: Podrzędne elementy runbook w usłudze Azure Automation
description: W tym artykule opisano różne metody udostępniania informacji między tymi składnikami i uruchamianie elementu runbook w usłudze Azure Automation z innego elementu runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84f17b76f03c01d0b1441a50b9bcbddc1dfe2ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61081582"
---
# <a name="child-runbooks-in-azure-automation"></a>Podrzędne elementy runbook w usłudze Azure Automation

Jest zalecaną praktyką w usłudze Azure Automation do zapisu wielokrotnego użytku, modularna elementy runbook za pomocą funkcji dyskretnych, która polega na innych elementach runbook. Nadrzędny element runbook często wywołuje jeden lub więcej podrzędnych elementów runbook do wykonania wymaganych funkcji. Istnieją dwa sposoby wywoływania podrzędnego elementu runbook, a każda odznacza się istotnymi różnicami, które należy zrozumieć tak, aby określić, co jest najlepsze dla różnych scenariuszy.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego

Aby śródwierszowo wywołać element runbook z innego elementu runbook, użyj nazwy elementu runbook i podaj wartości jego parametrów, dokładnie tak, należy użyć działania lub polecenia cmdlet.  Wszystkie elementy runbook na tym samym koncie usługi Automation są dostępne dla wszystkich innych ma być używany w ten sposób. Nadrzędny element runbook będzie czekać podrzędnego elementu runbook do wykonania przed przejściem do następnego wiersza, a wszelkie dane wyjściowe zostaną zwrócone bezpośrednio do obiektu nadrzędnego.

Gdy użytkownik śródwierszowo wywołać element runbook, jest ono wykonywane w tym samym zadaniu co nadrzędny element runbook. Nie istnieje żadne oznaki, w historii zadania podrzędnego elementu runbook, który został uruchomiony. Wszelkie wyjątki i strumienie wyjściowe z podrzędnego elementu runbook jest skojarzony z obiektem nadrzędnym. To zachowanie zmniejszyć liczbę zadań oraz łatwiej je śledzić i rozwiązywać, ponieważ wszelkie wyjątki zgłaszane przez podrzędny element runbook i dowolny z jego strumień danych wyjściowych jest skojarzona z zadaniem nadrzędnym.

Po opublikowaniu elementu runbook należy opublikować już wszelkimi podrzędnymi elementami runbook, który ją wywołuje. Jest to spowodowane usługi Azure Automation tworzy skojarzenie z wszelkimi podrzędnymi elementami runbook, gdy element runbook został skompilowany. Jeśli nie, nadrzędny element runbook wydaje się opublikowany prawidłowo, ale wygeneruje wyjątek, gdy jest ona uruchamiana. W takim przypadku można ponownie opublikować nadrzędny element runbook, aby prawidłowo utworzyć odwołanie do podrzędnych elementów runbook. Nie trzeba ponownie opublikować nadrzędny element runbook, jeśli dowolny podrzędne elementy runbook są zmieniane, ponieważ skojarzenie zostało już utworzone.

Parametry podrzędnego elementu runbook wywoływanego śródwierszowo może być dowolnego typu danych, włącznie z obiektami złożonymi. Istnieje nie [serializacji JSON](start-runbooks.md#runbook-parameters) się po uruchomieniu elementu runbook za pomocą witryny Azure portal lub za pomocą polecenia cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy elementów Runbook

Typy, które można wywołać siebie:

* A [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) może wywołać każdego innych wbudowanych (oba są na podstawie programu PowerShell).
* A [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i graficzny przepływ pracy programu PowerShell, elementów runbook można wywołać każdego innych wbudowanych (oba są na podstawie przepływu pracy programu PowerShell)
* Typy programu PowerShell i typy przepływów pracy programu PowerShell nie można wywołać wbudowane siebie nawzajem i musi być Start-AzureRmAutomationRunbook.

Podczas publikowania sprawy kolejności:

* Kolejność publikowania elementów runbook ma znaczenie tylko dla elementów runbook przepływu pracy programu PowerShell i graficzny przepływ pracy programu PowerShell.

Po wywołaniu graficzny lub przepływu pracy programu PowerShell podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego, możesz użyć nazwy elementu runbook.  Podczas wywoływania podrzędnego elementu runbook programu PowerShell, należy uruchomić takiej samej nazwy *.\\*  do określenia, że skrypt znajduje się w katalogu lokalnym.

### <a name="example"></a>Przykład

Poniższy przykład Uruchamianie testu podrzędnego elementu runbook, który przyjmuje trzy parametry, obiekt złożony, liczbę całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu runbook jest przypisany do zmiennej.  W tym przypadku podrzędnego elementu runbook jest przepływ pracy programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Oto przykład tego samego, przy użyciu elementu runbook programu PowerShell jako element podrzędny.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Uruchamianie podrzędnego elementu runbook za pomocą polecenia cmdlet

> [!IMPORTANT]
> Jeśli są wywoływania podrzędnego elementu runbook za pomocą `Start-AzureRmAutomationRunbook` polecenia cmdlet z `-Wait` przełącznika i wyniki podrzędnego elementu runbook jest obiektem, mogą wystąpić błędy. Aby obejść ten błąd, zobacz [podrzędnych elementów runbook z danymi wyjściowymi obiektu](troubleshoot/runbooks.md#child-runbook-object) dowiesz się, jak można zaimplementować logikę do sondowania pod kątem wyników i używanie [Get AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Możesz użyć [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) polecenia cmdlet, aby uruchomić element runbook, zgodnie z opisem w [do uruchamiania elementu runbook za pomocą programu Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Istnieją dwa tryby użytkowania dla tego polecenia cmdlet.  W trybie jednego polecenia cmdlet zwraca identyfikator zadania, gdy zadanie podrzędne jest tworzone dla podrzędnego elementu runbook.  W inny tryb, które umożliwiają określenie **— oczekiwania** parametru polecenia cmdlet czeka, aż zadanie podrzędne zostanie zakończone i zwraca dane wyjściowe z podrzędnego elementu runbook.

Zadania podrzędnego elementu runbook, pracę za pośrednictwem polecenia cmdlet jest uruchamiane jako osobne zadanie z nadrzędnego elementu runbook. To zachowanie większą liczbę zadań niż początkowa w tekście elementu runbook i utrudnia ich w celu śledzenia. Element nadrzędny można uruchomić więcej niż jeden podrzędny element runbook asynchronicznie bez oczekiwania na każdym do ukończenia. Dla tego samego rodzaju równoległego wywoływania podrzędnych elementów runbook wbudowanej nadrzędny element runbook musiałby użyć [słowa kluczowego parallel](automation-powershell-workflow.md#parallel-processing).

Dane wyjściowe podrzędne elementy runbook nie są zwracane do nadrzędnego elementu runbook niezawodnie ze względu na czas. Również pewnych zmiennych, takich jak $VerbosePreference, $WarningPreference, i inne osoby nie można propagować podrzędnych elementów runbook. Aby uniknąć tych problemów, należy uruchomić podrzędnych elementów runbook jako osobne zadania automatyzacji przy użyciu `Start-AzureRmAutomationRunbook` polecenia cmdlet z `-Wait` przełącznika. Blokuje nadrzędny element runbook, aż do podrzędnego elementu runbook.

Jeśli nie chcesz, nadrzędny element runbook do zablokowania w okresie oczekiwania, można uruchomić modułu podrzędnego elementu runbook za pomocą polecenia `Start-AzureRmAutomationRunbook` polecenia cmdlet bez `-Wait` przełącznika. Następnie konieczne będzie używać `Get-AzureRmAutomationJob` czekać na zakończenie zadania i `Get-AzureRmAutomationJobOutput` i `Get-AzureRmAutomationJobOutputRecord` do pobierania wyników.

Parametry dla podrzędnego elementu runbook, pracę za pośrednictwem polecenia cmdlet są przekazywane jako tablica skrótów, zgodnie z opisem w [parametry elementu Runbook](start-runbooks.md#runbook-parameters). Mogą być używane tylko proste typy danych. Jeśli element runbook ma parametr o złożonym typie danych, następnie go musi być wywoływany śródwierszowo.

Kontekstu subskrypcji mogą zostać utracone podczas uruchamiania podrzędnych elementów runbook jako osobne zadania. Aby dla podrzędnego elementu runbook do wykonania cmdlet usługi Azure RM dla określonej subskrypcji platformy Azure podrzędnego elementu runbook musi uwierzytelnić się do tej subskrypcji, niezależnie od nadrzędnego elementu runbook.

Jeśli zadań w ramach tego samego konta usługi Automation działają z więcej niż jedną subskrypcję, wybranie subskrypcji w ramach jednego zadania mogą ulec zmianie kontekstu aktualnie wybranej subskrypcji dla innych zadań. Aby uniknąć tego problemu, należy użyć `Disable-AzureRmContextAutosave –Scope Processsave` na początku każdego elementu runbook. Ta akcja tylko zapisuje kontekstu wykonania tego elementu runbook.

### <a name="example"></a>Przykład

W poniższym przykładzie uruchamianie podrzędnego elementu runbook z parametrami, a następnie czeka na jego zakończenie, za pomocą Start-AzureRmAutomationRunbook — oczekiwania parametru. Po ukończeniu jego dane wyjściowe są pobierane z podrzędnego elementu runbook. Aby użyć `Start-AzureRmAutomationRunbook`, wymagane jest uwierzytelnienie do subskrypcji platformy Azure.

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

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego elementu runbook

Poniższa tabela zawiera podsumowanie różnic między obiema metodami wywoływania elementu runbook z innego elementu runbook.

|  | wbudowane | Polecenia cmdlet |
|:--- |:--- |:--- |
| Zadania |Podrzędne elementy runbook uruchomione w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie podrzędnego elementu runbook. |
| Wykonanie |Nadrzędny element runbook czeka, aż podrzędnego elementu runbook, które należy wykonać przed kontynuowaniem. |Nadrzędny element runbook kontynuuje działanie natychmiast, po uruchomieniu podrzędnego elementu runbook *lub* nadrzędny element runbook czeka na zakończenie zadania podrzędnego. |
| Dane wyjściowe |Nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |Nadrzędny element runbook musi pobrać dane wyjściowe z zadania podrzędnego elementu runbook *lub* nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |
| Parametry |Wartości parametrów podrzędnego elementu runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego elementu runbook musi być połączone w jednym tablicę skrótów. Ta tablica skrótów można tylko zawierają proste, tablicy i obiektu typy danych używające serializacji JSON. |
| Konto usługi Automation |Nadrzędny element runbook można używać tylko podrzędnego elementu runbook na tym samym koncie usługi automation. |Nadrzędne elementy runbook można używać z dowolnego konta usługi automation z tej samej subskrypcji platformy Azure i inną subskrypcję, czy masz połączenie z podrzędnego elementu runbook. |
| Publikowanie |Podrzędny element runbook należy opublikować przed opublikowaniem nadrzędnego elementu runbook. |Podrzędny element runbook należy opublikować w dowolnym momencie przed nadrzędny element runbook jest uruchomiony. |

## <a name="next-steps"></a>Kolejne kroki

* [Uruchamianie elementu runbook w usłudze Azure Automation](start-runbooks.md)
* [Dane wyjściowe elementu Runbook i komunikatów w usłudze Azure Automation](automation-runbook-output-and-messages.md)

