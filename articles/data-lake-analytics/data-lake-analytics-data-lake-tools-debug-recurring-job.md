---
title: Debugowanie cyklicznych zadań w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak debugowanie nietypowe cyklicznych zadań przy użyciu narzędzia Azure Data Lake Tools dla programu Visual Studio.
services: data-lake-analytics
author: yanancai
manager: kfile
editor: jasonwhowell
ms.author: yanacai
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 6a181e0cb4014f80673c1bd33e89af69c5677b37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623574"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rozwiązywanie problemów z nietypowe cyklicznych zadań

W tym artykule przedstawiono sposób użycia [Azure Data Lake Tools dla programu Visual Studio](http://aka.ms/adltoolsvs) rozwiązywać problemy z zadaniami cyklicznego. Więcej informacji na temat potoku i cyklicznych zadań z [blogu usługi Azure Data Lake i usłudze Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Cykliczne zadania zazwyczaj korzysta z tej samej logiki kwerendy i podobnych danych wejściowych. Na przykład załóżmy, że masz uruchomiony poniedziałek codziennie o godzinie 8 cyklicznych zadań do obliczenia ostatniego tygodnia co tydzień aktywnego użytkownika. Skrypty dla tych zadań współużytkować jeden szablon skryptu, który zawiera logikę kwerendy. Dane wejściowe dla tych zadań są dane użycia ostatniego tygodnia. Udostępnianie samej logiki kwerendy i podobne dane wejściowe, zwykle oznacza, że wykonywania tych zadań podobne i stabilna. Jeśli jedna cyklicznych zadań nagle nieprawidłowo, wykonuje kończy się niepowodzeniem lub spowalnia partii, można:

- Zobacz raporty statystyk dla poprzedniego przebiegów cyklicznego zadania, aby zobaczyć, co się stało.
- Porównaj nietypowe zadania o normalnym jeden, aby dowiedzieć się, co zostało zmienione.

**Wyświetl zadania dotyczące** w narzędzia Azure Data Lake Tools dla Visual Studio pomaga przyspieszyć rozwiązywania problemów z postępem w obu przypadkach.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Znajdowanie cyklicznych zadań i otwieranie widoku Zadania pokrewne

Aby użyć widoku Zadania pokrewne do cyklicznego problemu zadania, należy najpierw odnaleźć cyklicznych zadań w programie Visual Studio, a następnie otwórz widok zadania pokrewne.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Przypadek 1: Ma adres URL cyklicznych zadań

Za pomocą **narzędzia** > **usługi Data Lake** > **widoku zadania**, możesz wkleić adres URL zadania, aby otworzyć widoku zadania w programie Visual Studio. Wybierz **Wyświetl zadania pokrewne** otwarcie widoku Zadania pokrewne.

![Link do wyświetlenia powiązanych zadań w narzędzi Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Przypadek 2: Masz potoku dla cyklicznego zadania, ale nie jej adres URL

W programie Visual Studio, możesz otworzyć potoku przeglądarki za pomocą Eksploratora serwera > Twoje konto usługi Azure Data Lake Analytics > **potoki**. (Jeśli nie możesz znaleźć tego węzła w Eksploratorze serwera [pobrać najnowsze](http://aka.ms/adltoolsvs).) 

![Wybranie węzła potoki](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

W przeglądarce potoku wszystkich potoków dla konta usługi Data Lake Analytics są wyświetlane po lewej stronie. Można rozwinąć potoki w celu znalezienia wszystkich cyklicznych zadań, a następnie wybierz jedną, która ma problemy. Otwiera widok zadania pokrewne po prawej.

![Wybierając potoku i otwieranie widoku Zadania pokrewne](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analizowanie raportu statystyk

Podsumowanie i Raport statystyki są wyświetlane u góry widoku Zadania pokrewne. Istnieje można znaleźć potencjalnej głównej przyczyny problemu. 

1.  W raporcie osi x zawiera czas przesyłania zadania. Umożliwia ona znaleźć nietypowe zadania.
2.  Aby sprawdzić statystyk i uzyskiwanie szczegółowych informacji o problemie i możliwe rozwiązania, użyj procesu na poniższym diagramie.

![Diagram procesu sprawdzania statystyk](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porównaj nietypowe zadania do normalnej zadania

Przesłane wszystkich cyklicznych zadań za pomocą listy zadań w dolnej części widoku Zadania pokrewne można znaleźć. Aby uzyskać więcej szczegółowych informacji i potencjalne rozwiązania, kliknij prawym przyciskiem myszy nietypowe zadania. Użyj widoku zadania Diff do porównania nietypowe zadania o normalnym poprzedni.

![Menu skrótów do porównywania zadania](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Należy zwrócić uwagę na duże różnice między te dwa zadania. Te różnice są prawdopodobnie powoduje problemy z wydajnością. Na poniższym diagramie, aby sprawdzać dalej, wykonaj kroki:

![Diagram procesu sprawdzania różnice między zadaniami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z danych zegara](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Debugowanie zdefiniowane przez użytkownika kodu C#, dla zadań U-SQL nie powiodło się](data-lake-analytics-debug-u-sql-jobs.md)
