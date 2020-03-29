---
title: Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation
description: W tym artykule został opisany sposób implementacji logiki obsługi błędów w graficznych elementach Runbook w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367078"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation

Kluczową zasadą projektowania, którą należy wziąć pod uwagę dla graficznego systemu runbook usługi Azure Automation, jest identyfikacja problemów, które może wystąpić podczas wykonywania. Przykładowe problemy to powodzenie, oczekiwane stany błędu i nieoczekiwane warunki błędu.

Często, jeśli występuje błąd nieubiegający, który występuje z działaniem runbook, program Windows PowerShell obsługuje działanie, przetwarzając wszelkie działania, które następuje, niezależnie od błędu. Błąd prawdopodobnie wygeneruje wyjątek, ale kolejne działanie może zostać uruchomione mimo tego.

Twój graficzny element runbook powinien zawierać kod obsługi błędów, aby poradzić sobie z problemami z wykonywaniem. Aby sprawdzić poprawność danych wyjściowych działania lub obsłużyć błąd, można użyć działania kodu programu PowerShell, zdefiniować logikę warunkową na łączu wyjściowym działania lub zastosować inną metodę.

Graficzne elementy runbook usługi Azure Automation ulepszono o możliwość uwzględnienia obsługi błędów. Teraz możesz przekształcać wyjątki w błędy niepowodujące zakończenia oraz tworzyć linki błędów między działaniami. Ulepszony proces umożliwia systemowi runbook wychwytywania błędów i zarządzania zrealizowanych lub nieoczekiwanych warunków. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="powershell-error-types"></a>Typy błędów programu PowerShell

Typy błędów programu PowerShell, które mogą wystąpić podczas wykonywania elementów runbook są kończące błędy i błędy nie kończące.
 
### <a name="terminating-error"></a>Błąd zakończenia

Błąd zakończenia jest poważnym błędem podczas wykonywania, który całkowicie zatrzymuje wykonanie polecenia lub skryptu. Przykłady obejmują nieistniejące polecenia cmdlet, błędy składni, które uniemożliwiają uruchomienie polecenia cmdlet i inne błędy krytyczne.

### <a name="non-terminating-error"></a>Błąd nieukający

Błąd nieukańczy to błąd niepodwążający się do poważnego błędu, który umożliwia kontynuowanie wykonywania pomimo warunku błędu. Przykłady obejmują błędy operacyjne, takie jak nie znaleziono błędów plików i problemów z uprawnieniami.

## <a name="when-to-use-error-handling"></a>Kiedy używać obsługi błędów

Użyj obsługi błędów w biei uruchomieniu, gdy działanie krytyczne zgłasza błąd lub wyjątek. Ważne jest, aby zapobiec przetwarzania następnego działania w trakcie i odpowiednio obsłużyć błąd. Obsługa błędu jest szczególnie krytyczna, gdy programy runbook obsługują proces operacyjny firmy lub usługi.

Dla każdego działania, które może spowodować błąd, można dodać łącze błędu wskazujące inne działanie. Działanie docelowe może być dowolnego typu, w tym działania kodu, wywołanie polecenia cmdlet, wywołanie innego śmiętu runbook i tak dalej. Działanie docelowe może również mieć łącza wychodzące, łącza regularne lub błędy. Łącza umożliwiają runbook do zaimplementowania logiki obsługi błędów złożonych bez uciekania się do działania kodu.

Zalecaną praktyką jest utworzenie dedykowanego śmiękań wiązki błędów z typowymi funkcjami, ale ta praktyka nie jest obowiązkowa. Rozważmy na przykład element runbook, który próbuje uruchomić maszynę wirtualną i zainstalować na nim aplikację. Jeśli maszyna wirtualna nie uruchamia się poprawnie, jest ona:

1. Wysyła powiadomienie o tym problemie.
2. Uruchamia kolejny projekt runbook, który automatycznie przepisuje nową maszynę wirtualną zamiast.

Jednym z rozwiązań jest mieć łącze błędu w trakcie runbook wskazując na działanie, które obsługuje krok pierwszy. Na przykład element runbook `Write-Warning` można połączyć polecenie cmdlet do działania dla kroku drugiego, takich jak polecenie cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

Można również uogólnić to zachowanie do użycia w wielu żyłaczkach, umieszczając te dwa działania w oddzielnym uruchomieniu życie. Zanim oryginalny system runbook wywoła ten projekt runbook obsługi błędów, można utworzyć wiadomość niestandardową z jego danych, a następnie przekazać go jako parametr do uruchomieniu do dziennika runbook.

## <a name="how-to-use-error-handling"></a>Jak korzystać z obsługi błędów

Każde działanie w bieśdmięcieniu ma ustawienie konfiguracji, które zamienia wyjątki w błędy nieubiegające się. To ustawienie jest domyślnie wyłączone. Zalecamy włączenie tego ustawienia w każdej aktywności, w której system runbook obsługuje błędy. To ustawienie gwarantuje, że element runbook obsługuje zarówno błędy kończące, jak i nieubiegające się w działaniu jako błędy nieubiegające się, używając łącza błędu.  

Po włączeniu ustawienia konfiguracji należy utworzyć działanie, które obsługuje błąd. Jeśli działanie generuje błąd, są przestrzegane wychodzące łącza błędów. Regularne linki nie są przestrzegane, nawet jeśli działanie produkuje regularne dane wyjściowe, jak również.<br><br> ![Przykład linka błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

W poniższym przykładzie element runbook pobiera zmienną, która zawiera nazwę komputera maszyny Wirtualnej. Następnie próbuje uruchomić maszynę wirtualną z następnego działania.<br><br> ![Przykład obsługi błędów łańszka systemu runbook automatyzacji](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Działanie `Get-AutomationVariable` i polecenie cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) są skonfigurowane do konwertowania wyjątków na błędy. Jeśli występują problemy z uzyskaniem zmiennej lub uruchomieniem maszyny Wirtualnej, kod generuje błędy.<br><br> ![Ustawienia](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)działań obsługi błędów systemu runbook automatyzacji .

Łącza błędów przepływu z tych `error management` działań do pojedynczego działania kodu. To działanie jest skonfigurowane przy użyciu prostego `throw` wyrażenia programu PowerShell, które używa słowa kluczowego, aby zatrzymać przetwarzanie, wraz z `$Error.Exception.Message` komunikatem opisującym bieżący wyjątek.<br><br> ![Przykład kodu obsługi błędu egonika automatyzacji](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o linkach i zrozumieć typy linków w graficznych elementach runbook, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) (Tworzenie graficzne w usłudze Azure Automation).

* Aby dowiedzieć się więcej na temat wykonywania uruchomieniu, monitorowania zadań umnienia i innych szczegółów technicznych, zobacz [Wykonanie uruchomieniu w usłudze Azure Automation.](automation-runbook-execution.md)