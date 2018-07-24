---
title: Podrzędne elementy runbook w usłudze Azure Automation
description: W tym artykule opisano różne metody udostępniania informacji między tymi składnikami i uruchamianie elementu runbook w usłudze Azure Automation z innego elementu runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582513e7e556859e70c1af9c4f6179e1d60e0139
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216526"
---
# <a name="child-runbooks-in-azure-automation"></a>Podrzędne elementy runbook w usłudze Azure Automation

Jest najlepszym rozwiązaniem w usłudze Azure Automation do zapisu wielokrotnego użytku, modularna elementów runbook zawierających osobne funkcje, które mogą być używane przez inne elementy runbook. Nadrzędny element runbook często wywołuje jeden lub więcej podrzędnych elementów runbook do wykonania wymaganych funkcji. Istnieją dwa sposoby wywoływania podrzędnego elementu runbook, a każda odznacza się istotnymi różnicami, które należy zrozumieć tak, aby określić najlepszą dla różnych scenariuszy.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego

Aby śródwierszowo wywołać element runbook z innego elementu runbook, użyj nazwy elementu runbook i podaj wartości jego parametrów, dokładnie tak, należy użyć działania lub polecenia cmdlet.  Wszystkie elementy runbook na tym samym koncie usługi Automation są dostępne dla wszystkich innych ma być używany w ten sposób. Nadrzędny element runbook będzie czekać podrzędnego elementu runbook do wykonania przed przejściem do następnego wiersza, a wszelkie dane wyjściowe zostaną zwrócone bezpośrednio do obiektu nadrzędnego.

Gdy użytkownik śródwierszowo wywołać element runbook, jest ono wykonywane w tym samym zadaniu co nadrzędny element runbook. Nie będzie żadnego wskazania w historii zadania podrzędnego elementu runbook, który został uruchomiony. Wszelkie wyjątki i strumienie wyjściowe z podrzędnego elementu runbook zostaną skojarzone z elementem nadrzędnym. To zmniejszyć liczbę zadań oraz łatwiej je śledzić i rozwiązywać problemy, ponieważ wszelkie wyjątki zgłaszane przez podrzędny element runbook i dowolny z jego strumieni wyjściowych są skojarzone z zadaniem nadrzędnym.

Po opublikowaniu elementu runbook należy opublikować już wszelkimi podrzędnymi elementami runbook, który ją wywołuje. Jest to spowodowane usługi Azure Automation tworzy skojarzenie z wszelkimi podrzędnymi elementami runbook, gdy element runbook został skompilowany. Jeśli nie, nadrzędny element runbook będzie wydawać się opublikowany prawidłowo, ale wygeneruje wyjątek, gdy jest ona uruchamiana. W takim przypadku można ponownie opublikować nadrzędny element runbook, aby prawidłowo utworzyć odwołanie do podrzędnych elementów runbook. Nie trzeba ponownie opublikować nadrzędny element runbook, jeśli dowolny podrzędne elementy runbook są zmieniane, ponieważ skojarzenie będzie zostały już utworzone.

Parametry podrzędnego elementu runbook wywoływanego śródwierszowo mogą być dowolnego typu danych, włącznie z obiektami złożonymi i ma nie [serializacji JSON](automation-starting-a-runbook.md#runbook-parameters) się po uruchomieniu elementu runbook za pomocą witryny Azure portal lub za pomocą Polecenie cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy elementów Runbook

Typy, które można wywołać siebie:

* A [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) może wywołać każdego innych wbudowanych (oba są na podstawie programu PowerShell).
* A [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i graficzny przepływ pracy programu PowerShell, elementów runbook można wywołać każdego innych wbudowanych (oba są na podstawie przepływu pracy programu PowerShell)
* Typy programu PowerShell i typy przepływów pracy programu PowerShell nie można wywołać wbudowane siebie nawzajem i musi być Start-AzureRmAutomationRunbook.

Podczas publikowania sprawy kolejności:

* Kolejność publikowania elementów runbook ma znaczenie tylko dla elementów runbook przepływu pracy programu PowerShell i graficzny przepływ pracy programu PowerShell.

Jeśli chcesz wywołać graficzny lub przepływu pracy programu PowerShell podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego, wystarczy użyć nazwę elementu runbook.  Podczas wywoływania podrzędnego elementu runbook programu PowerShell musi poprzedzony takiej samej nazwy *.\\*  do określenia, że skrypt znajduje się w katalogu lokalnym. 

### <a name="example"></a>Przykład

Poniższy przykład przedstawia wywoływanie testowego podrzędnego elementu runbook, który przyjmuje trzy parametry, obiekt złożony, liczbę całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu runbook jest przypisany do zmiennej.  W tym przypadku podrzędnego elementu runbook jest przepływ pracy programu PowerShell.

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

Możesz użyć [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) polecenia cmdlet, aby uruchomić element runbook, zgodnie z opisem w [do uruchamiania elementu runbook za pomocą programu Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Istnieją dwa tryby użytkowania dla tego polecenia cmdlet.  W trybie jednego polecenia cmdlet zwraca identyfikator zadania, jak zadanie podrzędne jest tworzone dla podrzędnego elementu runbook.  W inny tryb, które umożliwiają określenie **— oczekiwania** parametru polecenia cmdlet będzie czekać do momentu podrzędne zadania zostanie zakończone i zwróci dane wyjściowe z podrzędnego elementu runbook.

Zadania podrzędnego elementu runbook, pracę z poleceniem cmdlet uruchomi jako osobne zadanie z nadrzędnego elementu runbook. To powoduje większą liczbę zadań niż wywoływanie wbudowanych elementów runbook i utrudnia ich w celu śledzenia. Element nadrzędny można uruchomić wiele podrzędnych elementów runbook asynchronicznie bez oczekiwania na każdym do ukończenia. Dla tego samego rodzaju równoległego wywoływania podrzędnych elementów runbook wbudowanej nadrzędny element runbook musiałby użyć [słowa kluczowego parallel](automation-powershell-workflow.md#parallel-processing).

Dane wyjściowe do podrzędnych elementów runbook nie są zwracane do nadrzędnego elementu runbook w niezawodny sposób będą ze względu na czas. Również pewnych zmiennych, takich jak $VerbosePreference, $WarningPreference, i inne osoby nie można propagować podrzędnych elementów runbook. Aby uniknąć tych problemów, można wywołać podrzędnych elementów runbook jako osobne zadania automatyzacji przy użyciu `Start-AzureRmAutomationRunbook` polecenia cmdlet z `-Wait` przełącznika. Blokuje nadrzędny element runbook, aż do podrzędnego elementu runbook.

Jeśli nie chcesz, nadrzędny element runbook do zablokowania w okresie oczekiwania, można wywołać za pomocą elementów runbook podrzędnych `Start-AzureRmAutomationRunbook` polecenia cmdlet bez `-Wait` przełącznika. Następnie konieczne będzie używać `Get-AzureRmAutomationJob` czekać na zakończenie zadania i `Get-AzureRmAutomationJobOutput` i `Get-AzureRmAutomationJobOutputRecord` do pobierania wyników.

Parametry dla podrzędnego elementu runbook, pracę za pośrednictwem polecenia cmdlet są przekazywane jako tablica skrótów, zgodnie z opisem w [parametry elementu Runbook](automation-starting-a-runbook.md#runbook-parameters). Mogą być używane tylko proste typy danych. Jeśli element runbook ma parametr o złożonym typie danych, następnie go musi być wywoływany śródwierszowo.

### <a name="example"></a>Przykład

W poniższym przykładzie uruchamianie podrzędnego elementu runbook z parametrami, a następnie czeka na jego zakończenie, za pomocą Start-AzureRmAutomationRunbook — oczekiwania parametru. Po ukończeniu jego dane wyjściowe są pobierane z podrzędnego elementu runbook. Aby użyć `Start-AzureRmAutomationRunbook` musi uwierzytelniać do subskrypcji platformy Azure.

```azurepowershell-interactive
# Connect to Azure with RunAs account
$conn = Get-AutomationConnection -Name "AzureRunAsConnection"

$null = Add-AzureRmAccount `
  -ServicePrincipal `
  -TenantId $conn.TenantId `
  -ApplicationId $conn.ApplicationId `
  -CertificateThumbprint $conn.CertificateThumbprint

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}
$joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego elementu runbook
Poniższa tabela zawiera podsumowanie różnic między obiema metodami wywoływania elementu runbook z innego elementu runbook.

|  | wbudowane | Polecenie cmdlet |
|:--- |:--- |:--- |
| Zadanie |Podrzędne elementy runbook uruchomione w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie podrzędnego elementu runbook. |
| Wykonanie |Nadrzędny element runbook czeka, aż podrzędnego elementu runbook, które należy wykonać przed kontynuowaniem. |Nadrzędny element runbook kontynuuje działanie natychmiast, po uruchomieniu podrzędnego elementu runbook *lub* nadrzędny element runbook czeka na zakończenie zadania podrzędnego. |
| Dane wyjściowe |Nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |Nadrzędny element runbook musi pobrać dane wyjściowe z zadania podrzędnego elementu runbook *lub* nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |
| Parametry |Wartości parametrów podrzędnego elementu runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego elementu runbook muszą być połączone w jednym hashtable i może zawierać tylko prosty, tablicy i typy danych obiektu, że korzystają z serializacji JSON. |
| Konto usługi Automation |Nadrzędny element runbook można używać tylko podrzędnego elementu runbook na tym samym koncie usługi automation. |Nadrzędny element runbook można użyć podrzędnego elementu runbook z dowolnego konta usługi automation z tej samej subskrypcji platformy Azure i inną subskrypcję, jeśli masz z nią połączenie. |
| Publikowanie |Podrzędny element runbook należy opublikować przed opublikowaniem nadrzędnego elementu runbook. |Podrzędnego elementu runbook należy opublikować w dowolnym momencie przed uruchomieniem nadrzędnego elementu runbook. |

## <a name="next-steps"></a>Kolejne kroki

* [Uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md)
* [Dane wyjściowe elementu Runbook i komunikatów w usłudze Azure Automation](automation-runbook-output-and-messages.md)
