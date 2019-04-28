---
title: Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory
description: Jak używać usługi Azure Data Factory mapowanie przepływu zastępczy klucza przekształcania danych do generowania wartości klucza sekwencyjne
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350624"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapowanie transformacji klucza zastępczy przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj przekształcania klucz zastępczy, aby dodać zwiększającej się wartości inne niż firmy dowolnego wartość klucza do Twojego zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schemat gwiazdy, gdzie każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest częścią klucza niebiznesowe metodologii Kimballa DW.

![Zastępczych przekształcenia klucza](media/data-flow/surrogate.png "zastępczych Przekształcenie klucza")

"Kolumna klucza" jest nazwę, która zostanie nadana do nowej kolumny klucza zastępczy.

"Wartość początkowa" to punkt początkowy wartość przyrostową.

## <a name="increment-keys-from-existing-sources"></a>Inkrementacja kluczy z istniejących źródeł

Jeśli chcesz uruchomić danej sekwencji z wartości znajdującą się w źródle, możesz użyć przekształcenie kolumny nie pochodzącej od razu po swoją transformację klucz zastępczy i dodać dwie wartości razem:

![SK Dodaj Max](media/data-flow/sk006.png "zastępczy klucza przekształcania Dodaj maksymalna")

Wartość klucza z poprzedniego maksymalna wartość początkową, istnieją dwie techniki, które są dostępne:

### <a name="database-sources"></a>Źródłami bazy danych

Za pomocą opcji "Query" wybierzesz MAX() ze źródła przy użyciu transformacji źródła:

![Zastępczych klucza zapytania](media/data-flow/sk002.png "zastępczych zapytanie przekształcenia klucza")

### <a name="file-sources"></a>Plikowych źródeł

Jeśli Twoje poprzednie maksymalna wartość znajduje się w pliku, można użyć swoją transformację źródła wraz z przekształcenie agregacji i użyj funkcji wyrażenia MAX() można pobrać poprzednią wartość maksymalna:

![Plik klucza zastępczych](media/data-flow/sk008.png "zastępczych pliku klucza")

W obu przypadkach musisz dołączyć nowe dane przychodzące wraz z źródła zawierający poprzednią wartość maksymalna:

![Zastępczych klucza sprzężenia](media/data-flow/sk004.png "zastępczych klucza sprzężenia")

## <a name="next-steps"></a>Kolejne kroki

Użyj tych przykładach [Dołącz](data-flow-join.md) i [kolumny nie pochodzącej ze](data-flow-derived-column.md) przekształcenia.
