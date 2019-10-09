---
title: Przekształcenie klucza zastępczego przepływu danych mapowania Azure Data Factory
description: Jak używać transformacji klucza zastępczego przepływu danych Azure Data Factory mapowania, aby generować sekwencyjne wartości klucza
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 45e2d35a3b0a3f3c89913bbe70d7c43c17cbcee0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029181"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Przekształcanie przekształcenia klucza zastępczego przepływu danych



Użyj przekształcenia klucza dwuskładnikowego, aby dodać przyrostową wartość klucza niewspółpracującego do zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schematu gwiazdy, gdzie każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest kluczem niebiznesowym, częścią metodologii Kimballa DW.

Przekształcanie(media/data-flow/surrogate.png "klucza zastępczego") ![transformacji klucza]zastępczego

"Kolumna klucza" to nazwa, która zostanie nadana nowej kolumnie klucza zastępczego.

"Wartość początkowa" jest punktem początkowym wartości przyrostowej.

## <a name="increment-keys-from-existing-sources"></a>Przyrostowe klawisze z istniejących źródeł

Jeśli chcesz uruchomić sekwencję z wartości, która istnieje w źródle, możesz użyć przekształcenia kolumn pochodnych bezpośrednio po przekształceniu klucza zastępczego i dodać dwie wartości jednocześnie:

![Sk Dodawanie maksymalnego](media/data-flow/sk006.png "przekształcenia klucza zastępczego Dodaj maksimum")

Aby obsłużyć wartość klucza z poprzednią maksymalną, istnieją dwie techniki, których można użyć:

### <a name="database-sources"></a>Źródła bazy danych

Użyj opcji "Query", aby wybrać wartość MAX () ze źródła przy użyciu transformacji źródłowej:

(media/data-flow/sk002.png "Zapytanie o przekształcenie klucza zastępczego") ![zapytania klucza]zastępczego

### <a name="file-sources"></a>Źródła plików

Jeśli poprzednia maksymalna wartość znajduje się w pliku, można użyć transformacji źródłowej razem z transformację zagregowaną i użyć funkcji wyrażenia MAX (), aby uzyskać poprzednią wartość maksymalną:

(media/data-flow/sk008.png "Plik klucza zastępczego") ![pliku klucza zastępczego]

W obu przypadkach należy przyłączyć przychodzące nowe dane razem ze źródłem, które zawiera poprzednią wartość maksymalną:

![](media/data-flow/sk004.png "Sprzężenie zastępcze klawisza") Join

## <a name="next-steps"></a>Następne kroki

W poniższych przykładach użyto [sprzężeń](data-flow-join.md) i przekształceń [kolumn pochodnych](data-flow-derived-column.md) .
