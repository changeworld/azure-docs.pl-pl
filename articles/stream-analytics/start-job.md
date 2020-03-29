---
title: Jak uruchomić zadanie usługi Azure Stream Analytics
description: W tym artykule opisano sposób uruchamiania zadania usługi Stream Analytics z witryny Azure Portal, PowerShell i Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426464"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak uruchomić zadanie usługi Azure Stream Analytics

Zadanie usługi Azure Stream Analytics można uruchomić przy użyciu portalu Azure, programu Visual Studio i programu PowerShell. Po uruchomieniu zadania należy wybrać godzinę rozpoczęcia tworzenia danych wyjściowych zadania. Witryna Azure portal, visual studio i program PowerShell mają różne metody ustawiania czasu rozpoczęcia. Metody te są opisane poniżej.

## <a name="start-options"></a>Opcje startu
Trzy następujące opcje są dostępne, aby rozpocząć pracę. Należy pamiętać, że wszystkie czasy wymienione poniżej są te określone w [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Jeśli timestamp by nie jest określony, czas przybycia zostanie użyty.
* **Teraz:** Sprawia, że punkt początkowy strumienia zdarzenia wyjściowego jest taki sam, jak po uruchomieniu zadania. Jeśli używany jest operator czasowy (np. przedział czasu, LGD lub JOIN), usługa Azure Stream Analytics automatycznie spojrzy wstecz na dane w źródle wejściowym. Na przykład jeśli uruchomisz zadanie "Teraz" i jeśli zapytanie używa 5-minutowego okna roboczego, usługa Azure Stream Analytics będzie wyszukiwać dane sprzed 5 minut we danych wejściowych.
Pierwsze możliwe zdarzenie wyjściowe miałoby sygnaturę czasową równą lub większą niż bieżący czas, a ASA gwarantuje, że wszystkie zdarzenia wejściowe, które mogą logicznie przyczynić się do danych wyjściowych, zostały uwzględnione. Na przykład nie są generowane częściowe agregaty okienne. Zawsze jest to pełna wartość zagregowana.

* **Niestandardowe:** Można wybrać punkt początkowy danych wyjściowych. Podobnie jak w przypadku opcji **Teraz,** usługa Azure Stream Analytics automatycznie odczyta dane przed tym czasem, jeśli używany jest operator czasowy 

* **Po ostatnim zatrzymaniu**. Ta opcja jest dostępna, gdy zadanie zostało wcześniej uruchomione, ale została zatrzymana ręcznie lub nie powiodła się. Wybierając tę opcję Usługa Azure Stream Analytics użyje ostatniego czasu wydruku, aby ponownie uruchomić zadanie, aby nie utracić żadnych danych. Podobnie jak w przypadku poprzednich opcji usługa Azure Stream Analytics automatycznie odczytuje dane przed tą godziną, jeśli używany jest operator czasowy. Ponieważ kilka partycji wejściowych może mieć inny czas, pierwszy czas zatrzymania wszystkich partycji jest używany, w wyniku czego niektóre duplikaty mogą być widoczne w danych wyjściowych. Więcej informacji na temat przetwarzania dokładnie raz są dostępne na stronie [Gwarancje dostarczania zdarzeń.](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)


## <a name="azure-portal"></a>Portal Azure

Przejdź do zadania w witrynie Azure portal i wybierz **przycisk Start** na stronie przeglądu. Wybierz **godzinę rozpoczęcia pracy wyjściowej,** a następnie wybierz przycisk **Start**.

Wybierz jedną z opcji dla **czasu rozpoczęcia pracy wyjściowej**. Opcje to *Teraz*, *Niestandardowe*i, jeśli zadanie zostało wcześniej uruchomione, *Po ostatnim zatrzymaniu*. Zobacz powyżej, aby uzyskać więcej informacji na temat tych opcji.

## <a name="visual-studio"></a>Visual Studio

W widoku zadania wybierz zielony przycisk strzałki, aby rozpocząć zadanie. Ustaw **tryb uruchamiania wyjścia zadania** i **wybierz**start . Stan zadania zmieni się na **Uruchomiony**.

Istnieją trzy opcje **dla trybu uruchamiania wyjścia zadania:** *JobStartTime*, *CustomTime*i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecna, wartością domyślną jest *JobStartTime*. Zobacz powyżej, aby uzyskać więcej informacji na temat tych opcji.


## <a name="powershell"></a>PowerShell

Aby rozpocząć pracę za pomocą programu PowerShell, użyj następującego polecenia cmdlet:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Istnieją trzy opcje **dla OutputStartMode:** *JobStartTime*, *CustomTime*i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecna, wartością domyślną jest *JobStartTime*. Zobacz powyżej, aby uzyskać więcej informacji na temat tych opcji.

Aby uzyskać więcej `Start-AzStreamAnalyitcsJob` informacji na temat polecenia cmdlet, zobacz [odwołanie start-AzStreamAnalyticsJob .](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu narzędzi usługi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
