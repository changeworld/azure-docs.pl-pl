---
title: Mapowanie przepływu danych wydajności i dostosowywania przewodnik dotyczący usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przepływów danych w usłudze Azure Data Factory, korzystając z przepływów danych mapowania.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172343"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapowanie wydajności przepływów danych i dostosowywania przewodnik

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Platformy Azure Data Factory mapowanie przepływu danych udostępniają interfejs niekorzystające z kodu przeglądarki do projektowania, wdrażania i Organizuj Przekształcanie danych w dużej skali.

> [!NOTE]
> Jeśli nie jesteś zaznajomiony z usługi ADF mapowanie przepływu danych ogólnie rzecz biorąc, zobacz [omówienie przepływów danych](concepts-data-flow-overview.md) przed przeczytaniem tego artykułu.
>

> [!NOTE]
> Podczas projektowania i testowania przepływów danych z poziomu interfejsu użytkownika usługi ADF, upewnij się włączyć przełącznik debugowanie dzięki czemu możliwe jest wykonanie przepływów danych w czasie rzeczywistym bez konieczności oczekiwania na klastrze do rozgrzewki.
>

![Debugowanie przycisk](media/data-flow/debugb1.png "debugowania")

## <a name="optimizing-for-azure-sql-database"></a>Optymalizacja dla usługi Azure SQL Database

![Źródło część](media/data-flow/sourcepart2.png "część źródła")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Można dopasować Spark partycjonowanie partycjonowania bazy danych źródła danych na podstawie bazy danych tabeli kolumna klucza w Przekształcenie źródła

* Przejdź do "Optymalizuj" i wybierz pozycję "Źródło". Ustaw kolumnę określonej tabeli lub typu w zapytaniu.
* Jeśli została wybrana opcja "kolumnę", następnie wybierz kolumny partycji.
* Ponadto można ustawić maksymalną liczbę połączeń do bazy danych SQL Azure. Możesz spróbować wyższe ustawienie do uzyskania równoległych połączeń z bazą danych. Jednak czasami może spowodować szybsze działanie z ograniczoną liczbę połączeń.

### <a name="set-batch-size-and-query-on-source"></a>Ustaw rozmiar partii i zapytania w źródle

![Źródło](media/data-flow/source4.png "źródła")

* Ustawianie rozmiaru partii zleca usługi ADF, aby przechowywać dane w zestawach w pamięci, a nie wiersz po wierszu. Jest to ustawienie opcjonalne i może zostać wykorzystać zasoby w węzłach obliczeniowych, jeśli nie są one prawidłowo wielkości.
* Ustawianie zapytania umożliwia filtrowanie wierszy po prawej stronie w miejscu źródłowym przed nawet przychodzących dla przepływu danych do przetwarzania, co ułatwia szybsze pozyskiwania danych początkowych.
* Jeśli używasz zapytania, można dodać wskazówki zapytania opcjonalne dla Twojej bazy danych SQL Azure, czyli READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Ustaw rozmiar partii ujścia

![Obiekt sink](media/data-flow/sink4.png "ujście")

* Aby uniknąć przetwarzania wiersz po wierszu floes swoje dane, należy ustawić "rozmiar partii" w ustawieniach obiektu sink dla bazy danych SQL Azure. Informuje, że usługi ADF, aby proces bazy danych zapisuje w partiach, w zależności od rozmiaru, pod warunkiem.

### <a name="set-partitioning-options-on-your-sink"></a>Ustaw opcje obiektu sink partycjonowanie

* Nawet jeśli nie masz dane podzielone na partycje w tabelach bazy danych SQL Azure docelowego, przejdź na kartę Optymalizuj i ustawić partycjonowania.
* Bardzo często po prostu informuje usługi ADF, aby używa działanie okrężne partycjonowania w klastrach platformy Spark wykonanie powoduje szybsze ładowanie zamiast wymuszania wielokrotnego wszystkich połączeń z jednego węzła/partycji danych.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Zwiększ rozmiar Twojej aparatu obliczeniowego w Azure Integration Runtime

![Nowe środowisko IR](media/data-flow/ir-new.png "nowe środowisko IR")

* Zwiększ liczbę rdzeni, które spowoduje zwiększenie liczby węzłów i udostępnić więcej mocy obliczeniowej w celu wykonywania zapytań i zapisu do bazy danych SQL Azure.
* Wypróbuj "Obliczenia zoptymalizowane pod kątem" i "Zoptymalizowanego pod kątem pamięci" opcje do zastosowania większej ilości zasobów do węzłów obliczeniowych.

### <a name="disable-indexes-on-write"></a>Wyłącz indeksy podczas zapisu
* Użyj ADF potoku przechowywane procedury działania przed działaniem usługi przepływ danych, które wyłączają indeksów w tabelach docelowych, które są zapisywane z magazynem ujścia.
* Po działania przepływu danych Dodaj kolejne działanie przechowywanej, która umożliwiała te indeksy.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Zwiększ rozmiar bazy danych SQL Azure
* Zaplanuj zmiany rozmiaru źródła i ujścia Azure SQL DB zanim przebieg, który ogranicza potoku, aby zwiększyć przepływność i minimalizowanie ograniczania platformy Azure, gdy liczba jednostek DTU.
* Po zakończeniu wykonywanie potoku można zmienić rozmiar bazy danych do ich normalna szybkość wykonywania.

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły, przepływ danych:

- [Przegląd przepływu danych](concepts-data-flow-overview.md)
- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)

