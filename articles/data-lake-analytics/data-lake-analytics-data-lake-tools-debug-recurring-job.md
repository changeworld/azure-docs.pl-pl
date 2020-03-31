---
title: Debugowanie zadań cyklicznych w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak za pomocą narzędzia usługi Azure Data Lake Tools for Visual Studio do debugowania nieprawidłowego zadania cyklicznego.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629761"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rozwiązywanie problemów z nietypowym zadaniem cyklicznym

W tym artykule pokazano, jak używać [narzędzi usługi Azure Data Lake Tools dla programu Visual Studio](https://aka.ms/adltoolsvs) do rozwiązywania problemów z zadaniami powtarzającymi się. Dowiedz się więcej o potoku i zadaniach cyklicznych z [usługi Azure Data Lake i bloga Usługi Azure HDInsight.](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)

Zadania cykliczne zwykle współużytkują tę samą logikę zapytania i podobne dane wejściowe. Załóżmy na przykład, że masz cykliczne zadanie uruchomione w każdy poniedziałek rano o godzinie 8:00. liczyć w zeszłym tygodniu tygodniowy aktywny użytkownik. Skrypty dla tych zadań współużytkuje jeden szablon skryptu, który zawiera logikę kwerendy. Dane wejściowe dla tych zadań są dane użycia dla ostatniego tygodnia. Udostępnianie tej samej logiki zapytania i podobnych danych wejściowych zwykle oznacza, że wydajność tych zadań jest podobna i stabilna. Jeśli jedno z powtarzających się zadań nagle wykonuje się nieprawidłowo, kończy się niepowodzeniem lub spowalnia, możesz chcieć:

- Zobacz raporty statystyczne dla poprzednich przebiegów zadania cyklicznego, aby zobaczyć, co się stało.
- Porównaj nieprawidłowe zadanie z normalną, aby dowiedzieć się, co zostało zmienione.

**Pokrewny widok zadania** w narzędziach usługi Azure Data Lake tools dla programu Visual Studio pomaga przyspieszyć postęp rozwiązywania problemów w obu przypadkach.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Znajdź zadania cykliczne i otwórz widok zadania pokrewne

Aby użyć widoku pokrewnego zadania do rozwiązywania problemów z zadaniami cyklicznymi, należy najpierw znaleźć zadanie cykliczne w programie Visual Studio, a następnie otworzyć widok zadania pokrewnego.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Przypadek 1: Masz adres URL zadania cyklicznego

Za pomocą **narzędzia** > **Data Lake** > **Job View**można wkleić adres URL zadania, aby otworzyć widok zadania w programie Visual Studio. Wybierz **pozycję Wyświetl powiązane zadania,** aby otworzyć widok zadania pokrewne.

![Wyświetl łącze Pokrewne oferty pracy w narzędziach analizy usługi Data Lake](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Przypadek 2: Masz potok dla zadania cyklicznego, ale nie adres URL

W programie Visual Studio można otworzyć przeglądarkę potoków za pośrednictwem Eksploratora serwera > konta usługi Azure Data Lake Analytics > **potoki.** (Jeśli nie możesz znaleźć tego węzła w Eksploratorze serwera, [pobierz najnowszą wtyczkę).](https://aka.ms/adltoolsvs) 

![Wybieranie węzła Potoki](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

W przeglądarce potoków wszystkie potoki dla konta Usługi Data Lake Analytics są wyświetlane po lewej stronie. Można rozwinąć potoki, aby znaleźć wszystkie zadania cykliczne, a następnie wybrać ten, który ma problemy. Widok powiązanej pracy zostanie otwarty po prawej stronie.

![Wybieranie potoku i otwieranie widoku zadania pokrewnego](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analizowanie raportu statystycznego

Podsumowanie i raport statystyczny są wyświetlane w górnej części widoku zadania pokrewnego. Tam można znaleźć potencjalną przyczynę problemu. 

1.  W raporcie oś X pokazuje czas składania zadań. Użyj go, aby znaleźć nienormalną pracę.
2.  Użyj procesu na poniższym diagramie, aby sprawdzić statystyki i uzyskać szczegółowe informacje na temat problemu i możliwych rozwiązań.

![Diagram procesu do sprawdzania statystyk](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porównaj nieprawidłowe zadanie z normalną pracą

Wszystkie przesłane zadania cykliczne można znaleźć na liście zadań u dołu widoku zadania pokrewnego. Aby znaleźć więcej szczegółowych informacji i potencjalnych rozwiązań, kliknij prawym przyciskiem myszy nieprawidłowe zadanie. Użyj widoku Różnice zadań, aby porównać nieprawidłowe zadanie z poprzednim normalnym.

![Menu skrótów do porównywania zadań](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Zwróć uwagę na duże różnice między tymi dwoma zadaniami. Różnice te są prawdopodobnie przyczyną problemów z wydajnością. Aby sprawdzić dalej, należy wykonać czynności opisane na poniższym diagramie:

![Diagram procesów do sprawdzania różnic między zadaniami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z pochyleniami danych](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Debugowanie kodu C# zdefiniowanego przez użytkownika dla nieudanych zadań U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
