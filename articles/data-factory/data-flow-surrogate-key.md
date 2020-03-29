---
title: Transformacja klucza zastępczego przepływu danych mapowania
description: Jak używać transformacji klucza zastępczego przepływu danych usługi Azure Data Factory do generowania sekwencyjnych wartości kluczy
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930200"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Transformacja klucza zastępczego przepływu danych mapowania



Przekształcenie klucza zastępczego służy do dodawania przyrostowej wartości dowolnego klucza niebiznesowego do zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schematu gwiazdy, w którym każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest kluczem niebiznesowym, częścią metodologii Kimball DW.

![Przekształcenie klucza zastępczego](media/data-flow/surrogate.png "Transformacja klucza zastępczego")

"Kolumna klucza" to nazwa, którą nadasz nowej kolumnie klucza zastępczego.

"Wartość początkowa" jest punktem początkowym wartości przyrostowej.

## <a name="increment-keys-from-existing-sources"></a>Klucze przyrostowe z istniejących źródeł

Jeśli chcesz rozpocząć sekwencję od wartości, która istnieje w źródle, możesz użyć transformacji kolumny pochodnej bezpośrednio po transformacji klucza zastępczego i dodać dwie wartości razem:

![SK dodaj Maks.](media/data-flow/sk006.png "Maksymalna wartość przekształcenia klucza zastępczego")

Aby wysiewać wartość klucza z poprzednim max, istnieją dwie techniki, których można użyć:

### <a name="database-sources"></a>Źródła bazy danych

Użyj opcji "Zapytanie", aby wybrać MAX() ze źródła przy użyciu transformacji źródła:

![Kwerenda klucza zastępczego](media/data-flow/sk002.png "Kwerenda transformacji klucza zastępczego")

### <a name="file-sources"></a>Źródła plików

Jeśli poprzednia wartość maksymalna znajduje się w pliku, można użyć transformacji źródła wraz z transformacją agregacji i użyć funkcji wyrażenia MAX(), aby uzyskać poprzednią wartość maksymalną:

![Plik klucza zastępczego](media/data-flow/sk008.png "Plik klucza zastępczego")

W obu przypadkach należy dołączyć przychodzące nowe dane wraz ze źródłem, które zawiera poprzednią wartość maksymalną:

![Sprzężenie klucza zastępczego](media/data-flow/sk004.png "Sprzężenie klucza zastępczego")

## <a name="next-steps"></a>Następne kroki

W tych przykładach użyto przekształceń [sprzężenia](data-flow-join.md) i [kolumny pochodnej.](data-flow-derived-column.md)
