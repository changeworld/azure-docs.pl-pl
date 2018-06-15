---
title: Podrzędne elementy runbook automatyzacji Azure
description: W tym artykule opisano różne metody udostępniania informacji między nimi i uruchamianie elementu runbook automatyzacji Azure z innego elementu runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0511c2bf7eed15f997f8444c945afb18179bbc63
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194006"
---
# <a name="child-runbooks-in-azure-automation"></a>Podrzędne elementy runbook automatyzacji Azure

Jest najlepszym rozwiązaniem w automatyzacji Azure zapisu wielokrotnego użytku, moduły elementów runbook zawierających osobne funkcje, które mogą być używane przez inne elementy runbook. Nadrzędny element runbook często wywołuje jeden lub więcej podrzędnych elementów runbook do wykonania wymaganej funkcjonalności. Istnieją dwa sposoby do wywoływania podrzędnego elementu runbook, a każdy odznacza się istotnymi różnicami, które należy zrozumieć, dzięki czemu można określić, który będzie najlepiej w różnych sytuacjach.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego

Aby wywołać wbudowanego elementu runbook z innego elementu runbook, użyj nazwy elementu runbook i podaj wartości jego parametrów, dokładnie tak, należy użyć działania lub polecenia cmdlet.  Wszystkie elementy runbook na tym samym koncie automatyzacji są dostępne dla wszystkich innych do użycia w ten sposób. Nadrzędny element runbook będzie czekać na zakończenie przed przejściem do następnego wiersza podrzędnego elementu runbook, a wszelkie dane wyjściowe zostaną zwrócone bezpośrednio do elementu nadrzędnego.

Po wywołaniu wbudowanego elementu runbook jest uruchamiany w tym samym zadaniu co nadrzędny element runbook. Nie będzie żadnego wskazania w historii zadania podrzędnego elementu runbook, który działał. Wszelkie wyjątki i strumienie wyjściowe z podrzędnego elementu runbook zostanie skojarzona z obiektu nadrzędnego. To zmniejszyć liczbę zadań oraz łatwiej je śledzić i rozwiązywać problemy, ponieważ wszelkie wyjątki zgłaszane przez podrzędny element runbook oraz wszystkie jego strumieni wyjściowych są skojarzone z zadaniem nadrzędnym.

Po opublikowaniu elementu runbook należy opublikować już wszelkimi podrzędnymi elementami runbook, który wywołuje. Jest to spowodowane usługi Automatyzacja Azure tworzy skojarzenie z wszelkimi podrzędnymi elementami runbook podczas kompilowania elementu runbook. Jeśli nie są, nadrzędny element runbook będzie wydawać się opublikowany prawidłowo, ale wygeneruje wyjątek po uruchomieniu. W takim przypadku można ponownie opublikować nadrzędny element runbook, aby prawidłowo utworzyć odwołanie do podrzędnych elementów runbook. Nie trzeba ponownie opublikować nadrzędny element runbook, jeśli jedną z podrzędnych elementów runbook są zmieniane, ponieważ skojarzenie będzie zostały już utworzone.

Parametry podrzędnego elementu runbook wywoływanego śródwierszowo mogą być dowolnego typu danych, włącznie z obiektami złożonymi, a nie Brak [serializacji JSON](automation-starting-a-runbook.md#runbook-parameters) się po uruchomieniu elementu runbook przy użyciu portalu Azure lub z Polecenie cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy elementów Runbook

Typy, które można wywołać siebie:

* A [runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) można wywołać każdy, wbudowanego (są na podstawie programu PowerShell).
* A [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i przepływ pracy programu PowerShell graficznego elementów runbook można wywołać każdy, wbudowanego (oba są przepływu pracy programu PowerShell)
* Typy środowiska PowerShell i typy przepływu pracy programu PowerShell nie można wywołać wbudowanego siebie i musi być Start AzureRmAutomationRunbook.

Podczas publikowania sprawy kolejności:

* Kolejność publikowania elementów runbook ma znaczenie tylko dla elementów runbook przepływu pracy programu PowerShell i graficzny przepływ pracy programu PowerShell.

Podczas wywoływania graficzny lub przepływ pracy programu PowerShell podrzędnego elementu runbook przy użyciu wykonywania śródwierszowego tylko należy użyć nazwy elementu runbook.  Podczas wywoływania podrzędnego elementu runbook programu PowerShell musi poprzedzony nazwy z *.\\*  do określenia, że skrypt znajduje się w katalogu lokalnym. 

### <a name="example"></a>Przykład

Poniższy przykład przedstawia wywoływanie testowego podrzędnego elementu runbook, który akceptuje trzy parametry, obiekt złożony, liczbę całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu runbook jest przypisany do zmiennej.  W takim przypadku podrzędnego elementu runbook jest element runbook przepływu pracy programu PowerShell

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Poniżej znajduje się tym samym przykładzie, jako element podrzędny przy użyciu elementu runbook programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Uruchamianie podrzędnego elementu runbook za pomocą polecenia cmdlet

Można użyć [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) polecenia cmdlet można uruchomić elementu runbook zgodnie z opisem w [uruchomienia elementu runbook przy użyciu programu Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Dostępne są dwa tryby użycia tego polecenia cmdlet.  W trybie jednego polecenia cmdlet zwraca identyfikator zadania natychmiast po utworzeniu zadania podrzędne dla podrzędnego elementu runbook.  W innych trybie, który zostanie włączone, określając **— oczekiwania** parametru polecenia cmdlet będzie czekać podrzędne zakończenie zadania i zwróci dane wyjściowe z podrzędnego elementu runbook.

Zadanie podrzędnego elementu runbook uruchomione za pomocą polecenia cmdlet zostanie uruchomione jako osobne zadanie z nadrzędnego elementu runbook. To powoduje większą liczbę zadań niż wywoływania wbudowanego elementu runbook i utrudnia ich w celu śledzenia. Element nadrzędny można uruchomić wiele podrzędnych elementów runbook asynchronicznie bez oczekiwania na ukończenie działania każdego. Aby uzyskać takie same wykonywanie równoległe wywoływania wbudowanej podrzędnych elementów runbook, nadrzędny element runbook musiałby użyć [słowa kluczowego parallel](automation-powershell-workflow.md#parallel-processing).

Dane wyjściowe podrzędnych elementów runbook nie są zwracane do nadrzędnego elementu runbook niezawodnie ze względu na czas. Również pewnych zmiennych, takich jak $VerbosePreference, $WarningPreference, i inne osoby nie można propagować podrzędnych elementów runbook. Aby uniknąć tych problemów, można wywołać podrzędnych elementów runbook jako osobne zadania automatyzacji przy użyciu `Start-AzureRmAutomationRunbook` polecenia cmdlet z `-Wait` przełącznika. Blokuje nadrzędny element runbook, aż do zakończenia działania podrzędnego elementu runbook.

Jeśli nie chcesz nadrzędny element runbook do zablokowania na oczekiwania, można wywołać w podrzędnego elementu runbook przy użyciu `Start-AzureRmAutomationRunbook` polecenia cmdlet bez `-Wait` przełącznika. Następnie należy użyć `Get-AzureRmAutomationJob` czekać na zakończenie zadania i `Get-AzureRmAutomationJobOutput` i `Get-AzureRmAutomationJobOutputRecord` można pobrać wyników.

Parametry dla podrzędnego elementu runbook uruchomione za pomocą polecenia cmdlet są przekazywane jako tablica skrótów zgodnie z opisem w [parametrów elementu Runbook](automation-starting-a-runbook.md#runbook-parameters). Mogą być używane tylko proste typy danych. Jeśli element runbook ma parametr o złożonym typie danych, następnie go musi być wywoływany śródwierszowo.

### <a name="example"></a>Przykład

Poniższy przykład uruchamia podrzędnego elementu runbook z parametrami, a następnie oczekiwanie na jej zakończenie przy użyciu Start AzureRmAutomationRunbook-oczekiwania parametru. Po ukończeniu dane wyjściowe są zbierane z podrzędnego elementu runbook.

```azurepowershell-interactive
$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}
$joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego elementu runbook
Poniższa tabela zawiera podsumowanie różnic między obiema metodami wywoływania elementu runbook z innego elementu runbook.

|  | W tekście | Polecenie cmdlet |
|:--- |:--- |:--- |
| Zadanie |Podrzędne elementy runbook uruchomione w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie dla podrzędnego elementu runbook. |
| Wykonanie |Nadrzędny element runbook czeka na zakończenie przed kontynuowaniem podrzędnego elementu runbook. |Nadrzędny element runbook kontynuuje działanie od razu po uruchomieniu podrzędnego elementu runbook *lub* nadrzędny element runbook czeka na zakończenie zadania podrzędne. |
| Dane wyjściowe |Nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |Nadrzędny element runbook musi pobrać dane wyjściowe z zadania podrzędnego elementu runbook *lub* nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |
| Parametry |Wartości parametrów podrzędnego elementu runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego elementu runbook muszą być połączone w jedną tablicę skrótów i może zawierać tylko proste, tablicy i typy danych obiektu tym korzystają z serializacji JSON. |
| Konto usługi Automation |Nadrzędny element runbook można używać tylko podrzędnego elementu runbook na tym samym koncie automatyzacji. |Nadrzędny element runbook można użyć podrzędnego elementu runbook z dowolnego konta automatyzacji z tej samej subskrypcji platformy Azure i innej subskrypcji, jeśli masz połączenia z nią. |
| Publikowanie |Podrzędnego elementu runbook należy opublikować przed opublikowaniem nadrzędnego elementu runbook. |Podrzędnego elementu runbook należy opublikować w dowolnym momencie przed uruchomieniem nadrzędnego elementu runbook. |

## <a name="next-steps"></a>Kolejne kroki

* [Uruchamianie elementu runbook automatyzacji Azure](automation-starting-a-runbook.md)
* [Element Runbook danych wyjściowych i komunikatów w usłudze Automatyzacja Azure](automation-runbook-output-and-messages.md)
