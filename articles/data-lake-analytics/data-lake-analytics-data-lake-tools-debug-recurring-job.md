---
title: Debugowanie zadań cyklicznych w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak używać usługi Azure Data Lake Tools for Visual Studio do debugowania z nietypowym zadaniem cyklicznym.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629761"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rozwiązywanie problemów z nietypowym zadaniem cyklicznym

W tym artykule pokazano, jak [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) rozwiązywania problemów z zadań cyklicznych. Dowiedz się więcej o potokach i zadania cykliczne z [Azure Data Lake i Azure HDInsight blog](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Zadania cykliczne zwykle współużytkować tę samą logikę zapytania i podobne dane wejściowe. Załóżmy, że to zadanie cykliczne uruchamianie każdego ranka poniedziałek o godzinie 8 Aby zliczyć ostatni tydzień, co tydzień aktywnego użytkownika. Skrypty dla tych zadań współużytkować jeden szablon skryptu, który zawiera logikę zapytania. Dane wejściowe dla tych zadań są dane użycia dla ostatniego tygodnia. Udostępnianie tej samej logiki zapytania i podobne dane wejściowe zazwyczaj oznacza, że wykonywania tych zadań podobne i stabilne. Jeśli jedno z zadań cyklicznych nagle wykonuje nieprawidłowo, kończy się niepowodzeniem lub spowalnia dużo, możesz chcieć:

- Zobacz raporty statystyki dotyczące poprzednich uruchomień cyklicznych zadań, aby zobaczyć, co się stało.
- Porównaj nietypowe zadania o normalnym, aby zorientować się, co zostało zmienione.

**Widok zadania powiązane** w usłudze Azure Data Lake Tools for Visual Studio pomaga przyspieszyć rozwiązywania problemów z postępem w obu przypadkach.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Znajdź zadań cyklicznych i otwórz widok zadania pokrewne

Na potrzeby rozwiązywania problemu cykliczne zadania pokrewne widoku zadania, musisz najpierw Znajdź zadanie cykliczne w programie Visual Studio, a następnie otwórz widok powiązanych zadań.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Przypadek 1: Masz adres URL zadania cykliczne

Za pomocą **narzędzia** > **usługi Data Lake** > **widok zadania**, możesz wkleić adres URL zadania, aby otworzyć widok zadań w programie Visual Studio. Wybierz **Wyświetl powiązane zadania** aby otworzyć widok powiązane zadania.

![Link do wyświetlenia powiązanych zadań w narzędziach Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Przypadek 2: Masz potok zadanie cykliczne, ale nie jej adres URL

W programie Visual Studio, możesz otworzyć przeglądarkę potoków za pomocą Eksploratora serwera > Twoje konto usługi Azure Data Lake Analytics > **potoki**. (Jeśli nie możesz znaleźć tego węzła w Eksploratorze serwera [Pobierz najnowszą wtyczkę](https://aka.ms/adltoolsvs).) 

![Wybranie węzła potoków](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

W przeglądarce potoku wszystkich potoków dla konta usługi Data Lake Analytics są wyświetlane po lewej stronie. Można rozwinąć potoków, aby znaleźć wszystkie zadania cykliczne, a następnie wybierz ten, który ma problemy. Widok zadania pokrewne otwiera się po prawej.

![Wybierając potoku i otwarcie widoku Zadania pokrewne](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analizowanie raportu statystyk

Podsumowanie i Raport statystyki są wyświetlane u góry widoku powiązanych zadań. Można tam znaleźć potencjalną główną przyczynę problemu. 

1.  W raporcie osi x zawiera czasu przesłania zadania. Użyj go, aby znaleźć nieprawidłowe zadanie.
2.  Aby sprawdzić statystyk i uzyskuj szczegółowe informacje o problemie i możliwe rozwiązania, użyj procesu na poniższym diagramie.

![Diagram procesu sprawdzania statystyk](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porównaj nieprawidłowe zadanie do normalnego zadania

Można znaleźć wszystkie przesłane zadania cykliczne za pośrednictwem listy zadań w dolnej części widoku Zadania pokrewne. Aby uzyskać więcej szczegółowych informacji i potencjalne rozwiązania, kliknij prawym przyciskiem myszy nietypowe zadania. Porównaj nietypowe zadania o normalnym poprzedniemu za pomocą widoku Porównywarce zadań.

![Menu skrótów dla porównanie zadań](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Należy zwrócić uwagę na duże różnice między te dwa zadania. Te różnice są prawdopodobnie przyczyną problemów z wydajnością. Aby sprawdzić, dodatkowo, wykonaj kroki na poniższym diagramie:

![Diagram procesu sprawdzania różnice między zadaniami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z niesymetryczność danych](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Debugowanie zdefiniowanych przez użytkownika kodu C#, w przypadku nieudanych zadań U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
