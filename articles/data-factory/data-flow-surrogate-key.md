---
title: Przekształcenie klucza zastępczego przepływu danych mapowania Azure Data Factory
description: Jak używać transformacji klucza zastępczego przepływu danych Azure Data Factory mapowania, aby generować sekwencyjne wartości klucza
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387158"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Przekształcanie przekształcenia klucza zastępczego przepływu danych



Użyj przekształcenia klucza dwuskładnikowego, aby dodać przyrostową wartość klucza niewspółpracującego do zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schematu gwiazdy, gdzie każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest kluczem niebiznesowym, częścią metodologii Kimballa DW.

![Przekształcanie klucza zastępczego](media/data-flow/surrogate.png "Przekształcanie klucza zastępczego")

"Kolumna klucza" to nazwa, która zostanie nadana nowej kolumnie klucza zastępczego.

"Wartość początkowa" jest punktem początkowym wartości przyrostowej.

## <a name="increment-keys-from-existing-sources"></a>Przyrostowe klawisze z istniejących źródeł

Jeśli chcesz uruchomić sekwencję z wartości, która istnieje w źródle, możesz użyć przekształcenia kolumn pochodnych bezpośrednio po przekształceniu klucza zastępczego i dodać dwie wartości jednocześnie:

![SK Add Max](media/data-flow/sk006.png "Maksymalne dodanie przekształcenia klucza zastępczego")

Aby obsłużyć wartość klucza z poprzednią maksymalną, istnieją dwie techniki, których można użyć:

### <a name="database-sources"></a>Źródła bazy danych

Użyj opcji "Query", aby wybrać wartość MAX () ze źródła przy użyciu transformacji źródłowej:

![Zapytanie klucza zastępczego](media/data-flow/sk002.png "Zapytanie przekształcenia klucza zastępczego")

### <a name="file-sources"></a>Źródła plików

Jeśli poprzednia maksymalna wartość znajduje się w pliku, można użyć transformacji źródłowej razem z transformację zagregowaną i użyć funkcji wyrażenia MAX (), aby uzyskać poprzednią wartość maksymalną:

![Plik klucza zastępczego](media/data-flow/sk008.png "Plik klucza zastępczego")

W obu przypadkach należy przyłączyć przychodzące nowe dane razem ze źródłem, które zawiera poprzednią wartość maksymalną:

![Sprzężenie klawisza zastępczego](media/data-flow/sk004.png "Sprzężenie klawisza zastępczego")

## <a name="next-steps"></a>Następne kroki

W poniższych przykładach użyto [sprzężeń](data-flow-join.md) i przekształceń [kolumn pochodnych](data-flow-derived-column.md) .
