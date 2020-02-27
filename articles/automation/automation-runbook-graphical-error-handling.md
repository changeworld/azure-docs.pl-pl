---
title: Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation
description: W tym artykule został opisany sposób implementacji logiki obsługi błędów w graficznych elementach Runbook w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: dec715ec6741f4429d8b1d4f620ef3cb82d4c1d3
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649980"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation

Kluczową zasadą projektowania do uwzględnienia w Azure Automation graficzny element Runbook jest identyfikacja problemów, które element Runbook może napotkać podczas wykonywania. Przykładowe problemy to powodzenie, oczekiwane stany błędu i nieoczekiwane warunki błędu.

Często Jeśli wystąpi błąd niepowodujący zakończenia, który występuje w przypadku działania elementu Runbook, program Windows PowerShell obsługuje działanie przez przetwarzanie dowolnego działania, które następuje, niezależnie od błędu. Błąd prawdopodobnie wygeneruje wyjątek, ale kolejne działanie może zostać uruchomione mimo tego.

Graficzny element Runbook powinien zawierać kod obsługi błędu, aby rozwiązać problemy z wykonywaniem. Aby sprawdzić poprawność danych wyjściowych działania lub obsłużyć błąd, można użyć działania Code programu PowerShell, zdefiniować logikę warunkową dla linku wyjściowego działania lub zastosować inną metodę.

Azure Automation graficzne elementy Runbook zostały ulepszone z możliwością dołączenia obsługi błędów. Teraz możesz przekształcać wyjątki w błędy niepowodujące zakończenia oraz tworzyć linki błędów między działaniami. Ulepszony proces umożliwia elementowi Runbook przechwytywanie błędów i zarządzanie zrealizowanymi lub nieoczekiwanymi warunkami. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Typy błędów programu PowerShell

Typy błędów programu PowerShell, które mogą wystąpić podczas wykonywania elementu Runbook, to: kończenie błędów i błędy niepowodujące zakończenia.
 
### <a name="terminating-error"></a>Błąd powodujący zakończenie

Błąd powodujący przerwanie to poważny błąd podczas wykonywania, który całkowicie zatrzymuje polecenie lub wykonywanie skryptu. Przykłady zawierają nieistniejące polecenia cmdlet, błędy składniowe, które uniemożliwiają uruchomienie polecenia cmdlet, oraz inne błędy krytyczne.

### <a name="non-terminating-error"></a>Błąd niepowodujący zakończenia

Błąd niepowodujący zakończenia to nieważny błąd, który umożliwia kontynuowanie wykonywania pomimo błędu. Przykładami mogą być błędy operacyjne, takie jak nie znaleziono plików i problemy z uprawnieniami.

## <a name="when-to-use-error-handling"></a>Kiedy używać obsługi błędów

Użyj obsługi błędów w elemencie Runbook, gdy działanie krytyczne zgłasza błąd lub wyjątek. Ważne jest, aby zapobiec przetwarzaniu następnego działania w elemencie Runbook i odpowiednio obsłużyć błąd. Obsługa tego błędu jest szczególnie istotna, gdy elementy Runbook obsługują proces operacji biznesowej lub usług.

Dla każdego działania, które może powodować błąd, można dodać łącze błędu wskazujące na inne działanie. Działanie docelowe może być dowolnego typu, w tym działania związane z kodem, wywołania polecenia cmdlet, wywołania innego elementu Runbook i tak dalej. Działanie docelowe może również mieć linki wychodzące, zwykłe lub błędy. Linki umożliwiają elementowi Runbook zaimplementowanie złożonej logiki obsługi błędów bez konieczności działania kodu.

Zalecaną metodą jest utworzenie dedykowanego elementu Runbook obsługi błędów z typowymi funkcjami, ale nie jest to wymagane. Rozważmy na przykład element Runbook, który próbuje uruchomić maszynę wirtualną i zainstalować na niej aplikację. Jeśli maszyna wirtualna nie zostanie uruchomiona poprawnie,:

1. Wysyła powiadomienie o tym problemie.
2. Uruchamia inny element Runbook, który zamiast tego automatycznie inicjuje nową maszynę wirtualną.

Jednym z rozwiązań jest posiadanie linku błędu w elemencie Runbook wskazującego na działanie, które obsługuje krok jeden. Na przykład element Runbook może podłączyć polecenie cmdlet **Write-Warning** do działania dla kroku 2, takiego jak polecenie cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) .

Można również uogólnić to zachowanie w wielu elementach Runbook, umieszczając te dwa działania w oddzielnym elemencie Runbook obsługującym błędy, postępując zgodnie z przedstawionymi wcześniej wskazówkami. Zanim oryginalny element Runbook wywoła ten błąd obsługi elementu Runbook, może utworzyć niestandardowy komunikat z jego danych, a następnie przekazać go jako parametr do elementu Runbook obsługującego błędy.

## <a name="how-to-use-error-handling"></a>Jak korzystać z obsługi błędów

Każde działanie w elemencie Runbook ma ustawienie konfiguracji, które powoduje zamianę wyjątków na błędy niepowodujące zakończenia. To ustawienie jest domyślnie wyłączone. Zalecamy włączenie tego ustawienia dla wszystkich działań, w których element Runbook obsługuje błędy. Włączenie tej konfiguracji zapewnia, że element Runbook obsługuje zarówno błędy kończące, jak i niepowodujące zakończenia w działaniu jako błędy niepowodujące zakończenia, przy użyciu linku błędu.  

Po włączeniu ustawienia konfiguracji należy utworzyć element Runbook, który obsługuje ten błąd. Jeśli działanie powoduje jakikolwiek błąd, następuje wychodzące łącza błędów. Regularne linki nie są wykonywane nawet wtedy, gdy działanie generuje regularne dane wyjściowe.<br><br> ![Przykład linka błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

W poniższym przykładzie element Runbook pobiera zmienną, która zawiera nazwę komputera maszyny wirtualnej. Następnie próbuje uruchomić maszynę wirtualną przy użyciu następnego działania.<br><br> ![Przykład obsługi błędu elementu Runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Działanie **Get-AutomationVariable** i polecenie cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) są skonfigurowane do konwertowania wyjątków na błędy. Jeśli wystąpią problemy z pobraniem zmiennej lub uruchomieniem maszyny wirtualnej, kod generuje błędy.<br><br> ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)ustawień działania obsługi błędów elementu Runbook usługi Automation ![.

Linki błędów przepływają z tych działań do pojedynczego działania kodu **zarządzania błędami** . To działanie jest skonfigurowane za pomocą prostego wyrażenia programu PowerShell, które używa słowa kluczowego *throw* , aby zatrzymać przetwarzanie, oraz `$Error.Exception.Message`, aby uzyskać komunikat, który opisuje bieżący wyjątek.<br><br> przykład kodu obsługi błędu elementu Runbook usługi ![Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o linkach i zrozumieć typy linków w graficznych elementach runbook, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) (Tworzenie graficzne w usłudze Azure Automation).

* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, monitorowaniu zadań elementów Runbook i innych szczegółach technicznych, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).