---
title: Jak uruchomić zadania usługi Azure Stream Analytics
description: W tym artykule opisano sposób uruchamiania zadania usługi Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61362269"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak uruchomić zadania usługi Azure Stream Analytics

Można uruchomić zadania usługi Azure Stream Analytics przy użyciu witryny Azure portal, programu Visual Studio i PowerShell. Po uruchomieniu zadania, możesz wybrać czas wykonywania zadania rozpocząć tworzenie danych wyjściowych. Witryna Azure portal, programu Visual Studio i PowerShell każdego mają różne metody do ustawiania godziny rozpoczęcia. Te metody są opisane poniżej.

## <a name="start-options"></a>Opcje uruchamiania
Trzy poniższe opcje są dostępne do rozpoczęcia zadania. Należy pamiętać, że cały czas, które są wymienione poniżej są określone w [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Jeśli nie określono TIMESTAMP BY, użyty zostanie czas nadejścia.
* **Teraz**: Sprawia, że punkt początkowy zdarzenie wyjściowe strumienia taki sam jak podczas uruchamiania zadania. Jeśli używany jest operator danych czasowych (np. przedział czasu, opóźnienie lub ŁĄCZONY albo oczekuje), Azure Stream Analytics automatycznie przyjrzymy się tworzenie kopii danych w źródle danych wejściowych. Na przykład "Teraz" Uruchamianie zadania i zapytanie używa okno wirowania 5 minut, Azure Stream Analytics wyszukiwanie danych od 5 minut temu w danych wejściowych.
Pierwsze zdarzenie w danych wyjściowych zostało równy lub większy niż bieżący czas i ASA gwarantuje wyliczanymi wszystkich zdarzeń wejściowych, które logicznie mogą przyczynić się do danych wyjściowych. Na przykład są generowane nie częściowej agregacji w trybie okna. Zawsze jest pełna wartość zagregowaną.

* **Niestandardowy**: Możesz wybrać początkowy punkt danych wyjściowych. Podobnie jak **teraz** opcji usługi Azure Stream Analytics będzie automatycznie odczytywać dane przed tym razem, gdy używany jest operator danych czasowych 

* **Ostatnio zatrzymane**. Ta opcja jest dostępna, gdy zadanie została wcześniej uruchomiona, ale został ręcznie zatrzymany lub nie powiodło się. Po wybraniu tej opcji usługi Azure Stream Analytics użyje ostatniego wyjścia ponownie uruchomić zadanie, dzięki czemu żadne dane nie zostaną utracone. Podobnie do poprzednich opcji usługi Azure Stream Analytics będzie automatycznie odczytywać dane przed tym razem Jeśli używany jest operator danych czasowych. Ponieważ kilka partycji danych wejściowych może mieć inny czas, Najwcześniejsza godzina zatrzymania wszystkich partycji jest używana, a w rezultacie niektóre duplikaty mogą być widoczne w danych wyjściowych. Więcej informacji na temat dokładnie — po zakończeniu przetwarzania są dostępne na stronie [gwarancją dostarczania zdarzeń](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Przejdź do zadania w witrynie Azure portal i wybierz pozycję **Start** na stronie Przegląd. Wybierz **czas rozpoczęcia dane wyjściowe zadania** , a następnie wybierz **Start**.

Wybierz jedną z opcji dla **czas rozpoczęcia dane wyjściowe zadania**. W tabeli przedstawiono opcje *teraz*, *niestandardowe*, i, jeśli zadanie zostało wcześniej uruchomione, *ostatnio zatrzymane*. Aby uzyskać więcej informacji o tych opcjach, zobacz powyżej.

## <a name="visual-studio"></a>Visual Studio

W widoku zadania wybierz przycisk zieloną strzałkę, aby uruchomić zadanie. Ustaw **zadanie danych wyjściowych Uruchom tryb** i wybierz **Start**. Stan zadania zmieni się na **systemem**.

Dostępne są trzy opcje **zadanie danych wyjściowych Uruchom tryb**: *JobStartTime*, *CustomTime*, i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecne, wartością domyślną jest *JobStartTime*. Aby uzyskać więcej informacji o tych opcjach, zobacz powyżej.


## <a name="powershell"></a>PowerShell

Użyj następującego polecenia cmdlet, aby uruchomić zadanie przy użyciu programu PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Dostępne są trzy opcje **OutputStartMode**: *JobStartTime*, *CustomTime*, i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecne, wartością domyślną jest *JobStartTime*. Aby uzyskać więcej informacji o tych opcjach, zobacz powyżej.

Aby uzyskać więcej informacji na temat `Start-AzStreamAnalyitcsJob` polecenia cmdlet widoku [odwołania Start AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md)
