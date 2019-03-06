---
title: Jak uruchomić zadania usługi Azure Stream Analytics
description: W tym artykule opisano sposób uruchamiania zadania usługi Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: aa089ed53554ec697bd9430cc95a7cce78aabed2
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411529"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak uruchomić zadania usługi Azure Stream Analytics

Można uruchomić zadania usługi Azure Stream Analytics przy użyciu witryny Azure portal, programu Visual Studio i PowerShell. Po uruchomieniu zadania, możesz wybrać czas wykonywania zadania rozpocząć tworzenie danych wyjściowych. Witryna Azure portal, programu Visual Studio i PowerShell każdego mają różne metody do ustawiania godziny rozpoczęcia. Te metody są opisane poniżej.

## <a name="azure-portal"></a>Azure Portal

Przejdź do zadania w witrynie Azure portal i wybierz pozycję **Start** na stronie Przegląd. Wybierz **czas rozpoczęcia dane wyjściowe zadania** , a następnie wybierz **Start**.

Dostępne są trzy opcje **czas rozpoczęcia dane wyjściowe zadania**: *Teraz*, *niestandardowe*, i *ostatnio zatrzymane*. Wybieranie *teraz* uruchamia zadanie w danej chwili. Wybieranie *niestandardowe* pozwala ustawić niestandardowy czas w przeszłości lub w przyszłości dla zadania rozpocząć. Aby wznowić zatrzymane zadanie bez utraty danych, należy wybrać. Ostatnio zatrzymane *.

## <a name="visual-studio"></a>Visual Studio

W widoku zadania wybierz przycisk zieloną strzałkę, aby uruchomić zadanie. Ustaw **zadanie danych wyjściowych Uruchom tryb** i wybierz **Start**. Stan zadania zmieni się na **systemem**.

Dostępne są trzy opcje **zadanie danych wyjściowych Uruchom tryb**: *JobStartTime*, *CustomTime*, i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecne, wartością domyślną jest *JobStartTime*.

*JobStartTime* sprawia, że punktem początkowym zdarzenie wyjściowe strumienia taka sama jak uruchomienia zadania.

*CustomTime* dane wyjściowe są prezentowane od niestandardowy czas, który jest określony w *OutputStartTime* parametru.

*LastOutputEventTime* sprawia, że punktem początkowym dane wyjściowe strumienia zdarzeń, taka sama jak ostatnie zdarzenie godzina generowania danych wyjściowych.

## <a name="powershell"></a>PowerShell

Użyj następującego polecenia cmdlet, aby uruchomić zadanie przy użyciu programu PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Dostępne są trzy opcje **OutputStartMode**: *JobStartTime*, *CustomTime*, i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecne, wartością domyślną jest *JobStartTime*.

*JobStartTime* sprawia, że punktem początkowym zdarzenie wyjściowe strumienia taka sama jak uruchomienia zadania.

*CustomTime* dane wyjściowe są prezentowane od niestandardowy czas, który jest określony w *OutputStartTime* parametru.

*LastOutputEventTime* sprawia, że punktem początkowym dane wyjściowe strumienia zdarzeń, taka sama jak ostatnie zdarzenie godzina generowania danych wyjściowych.

Aby uzyskać więcej informacji na temat `Start-AzStreamAnalyitcsJob` polecenia cmdlet widoku [odwołania Start AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob.md).

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md)