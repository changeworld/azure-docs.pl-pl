---
title: Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation
description: W tym artykule został opisany sposób implementacji logiki obsługi błędów w graficznych elementach Runbook w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d7fe38334b71334d4dae9235643117efdf5fbd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61233124"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Obsługa błędów w graficznych elementach Runbook w usłudze Azure Automation

Kluczową zasadą projektu elementu runbook, którą należy wziąć pod uwagę, jest ustalenie różnych problemów, które mogą wystąpić w elemencie runbook. Przykładowe problemy to powodzenie, oczekiwane stany błędu i nieoczekiwane warunki błędu.

Elementy runbook powinny zawierać obsługę błędów. Aby walidować dane wyjściowe działania lub obsługiwać błędy graficznego elementu runbook, możesz używać działań kodu programu Windows PowerShell, definiować logikę warunkową linku danych wyjściowych działania lub zastosować inną metodę.          

Często w przypadku wystąpienia błędu działania runbook, który nie powoduje zakończenia, wszystkie kolejne działania są przetwarzane niezależnie od tego błędu. Błąd prawdopodobnie wygeneruje wyjątek, ale kolejne działanie może zostać uruchomione mimo tego. Obsługa błędów w programie PowerShell została w ten sposób zaprojektowana.    

Typami błędów programu PowerShell, które mogą wystąpić podczas wykonywania, są błędy powodujące i niepowodujące zakończenia. Różnice między błędem powodującym zakończenie i niepowodującym zakończenia są następujące:

* **Błąd powodujący zakończenie**: To poważny błąd podczas wykonywania, który całkowicie zatrzymuje polecenie (lub wykonywanie skryptu). Do przykładów należą nieistniejące polecenia cmdlet, błędy składniowe, które uniemożliwiają wykonanie polecenia cmdlet, lub inne błędy krytyczne.

* **Błąd niepowodujący**: Niezbyt poważny błąd, który umożliwia kontynuowanie wykonywania pomimo awarii. Do przykładów należą błędy operacyjne, takie jak niemożność odnalezienia pliku i problemy z uprawnieniami.

Usługa Azure graficznych elementów runbook usługi Automation zostały ulepszone o możliwość uwzględnienia obsługi błędów. Teraz możesz przekształcać wyjątki w błędy niepowodujące zakończenia oraz tworzyć linki błędów między działaniami. Ten proces umożliwia autorowi elementu runbook wychwytywanie błędów i zarządzanie zrealizowanymi lub nieoczekiwanymi warunkami.  

## <a name="when-to-use-error-handling"></a>Kiedy używać obsługi błędów

Zawsze, gdy krytyczne działanie zgłasza błąd lub wyjątek, istotne jest uniemożliwienie przetworzenia następnego działania w elemencie runbook i odpowiednie obsłużenie błędu. Jest to bardzo ważne zwłaszcza wtedy, gdy Twoje elementy runbook obsługują proces operacji usługi lub biznesowy.

Do każdego działania, które może powodować błąd, autor elementu runbook może dodać link błędu wskazujący dowolne inne działanie. Działanie docelowe może mieć dowolny typ: działanie kodu, wywołanie polecenia cmdlet, wywołanie innego elementu runbook itd.

Ponadto działanie docelowe również może mieć linki wychodzące. Mogą to być zwykłe linki lub linki błędów. Oznacza to, że autor elementu runbook może zaimplementować złożoną logikę obsługi błędów bez konieczności używania działania kodu. Zaleca się utworzenie elementu runbook dedykowanego do obsługi błędów mającego typową funkcjonalność, ale nie jest to obowiązkowe. Logika obsługi błędów w działaniu kodu PowerShell nie jest jedyną możliwością.  

Rozważ na przykład element runbook, który próbuje uruchomić maszynę wirtualną i zainstalować na niej aplikację. Jeśli maszyna wirtualna nie uruchomi się poprawnie, wykonuje on dwie akcje:

1. Wysyła powiadomienie o tym problemie.
2. Uruchamia inny element runbook, który automatycznie aprowizuje nową maszynę wirtualną.

Jedno z rozwiązań to utworzenie linku błędu wskazującego działanie obsługujące pierwszy krok. Na przykład możesz połączyć polecenie cmdlet **Write-Warning** z działaniem dla drugiego kroku, takim jak polecenie cmdlet **Start-AzureRmAutomationRunbook**.

To zachowanie możesz również uogólnić w celu zastosowania go w wielu elementach runbook, umieszczając te dwa działania w oddzielnym elemencie runbook obsługującym błędy i stosując wskazówki podane wcześniej w tym temacie. Przed wywołaniem tego elementu runbook obsługującego błędy możesz skonstruować niestandardowy komunikat na podstawie danych w oryginalnym elemencie runbook, a następnie przekazać go jako parametr do elementu runbook obsługującego błędy.

## <a name="how-to-use-error-handling"></a>Jak korzystać z obsługi błędów

Każde działanie ma ustawienie konfiguracji umożliwiające przekształcenie wyjątków w błędy niepowodujące zakończenia. To ustawienie jest domyślnie wyłączone. Zalecamy włączenie tego ustawienia dla każdego działania, które ma obsługiwać błędy.  

Przez włączenie tej konfiguracji zapewniasz, że zarówno błędy powodujące zakończenie, jak i niepowodujące zakończenia w działaniu są obsługiwane jako błędy niepowodujące zakończenia, a następnie mogą być obsługiwane za pomocą linka błędu.  

Po skonfigurowaniu tego ustawienia możesz utworzyć działanie obsługujące błąd. Jeśli działanie powoduje jakikolwiek błąd, to następne działanie jest zgodne z wychodzącymi linkami błędów, a nie z normalnymi linkami nawet wtedy, gdy działanie utworzy również normalne dane wyjściowe.<br><br> ![Przykład linka błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

W poniższym przykładzie element runbook pobiera zmienną, która zawiera nazwę komputera maszyny wirtualnej. Następnie w kolejnym działaniu próbuje on uruchomić maszynę wirtualną.<br><br> ![Przykład obsługi błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Działanie **Get-AutomationVariable** i **Start AzureRmVm** są skonfigurowane do przekształcania wyjątków w błędy. Jeśli wystąpią problemy z pobraniem zmiennej lub uruchomieniem maszyny wirtualnej, zostaną wygenerowane błędy.<br><br> ![Ustawienia działania obsługi błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Linki błędów z tych działań prowadzą do pojedynczego działania **zarządzania błędami** (działanie kodu). To działanie jest skonfigurowane za pomocą prostego wyrażenia programu PowerShell przy użyciu słowa kluczowego *Throw*, aby zatrzymać przetwarzanie, oraz *$Error.Exception.Message*, aby uzyskać komunikat, który opisuje bieżący wyjątek.<br><br> ![Przykład kodu obsługi błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o linkach i zrozumieć typy linków w graficznych elementach runbook, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) (Tworzenie graficzne w usłudze Azure Automation).

* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).

