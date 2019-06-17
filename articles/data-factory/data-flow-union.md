---
title: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
description: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348280"
---
# <a name="mapping-data-flow-union-transformation"></a>Złożenia Przekształcanie przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Unii łączy wiele strumieni danych w jednym z SQL Unii tych strumieni jako nowe dane wyjściowe z transformacji Unii. Wszystkie schematu z każdego strumienia wejściowego zostanie połączona wewnątrz przepływu danych, bez konieczności klucz sprzężenia.

N liczbę strumieni w tabeli ustawień można łączyć, wybierając ikonę "+" obok każdego wiersza skonfigurowane, w tym zarówno dane źródłowe, jak i strumieni z istniejących przekształcenia przepływu danych.

![Przekształcenie złożenia](media/data-flow/union.png "Unii")

W takim przypadku można łączyć różnych metadanych z wielu źródeł (w tym przykładzie trzy innych plików źródłowych) i połączyć je w jednym strumień:

![Przegląd Przekształcanie złożenia](media/data-flow/union111.png "złożenia 1")

Aby to osiągnąć, należy dodać dodatkowe wiersze w ustawieniach Unii, umieszczając wszystkie źródła, które chcesz dodać. Nie ma potrzeby wspólnego klucza wyszukiwania lub łączony albo oczekuje:

![Ustawienia Union przekształcania](media/data-flow/unionsettings.png "ustawienia Unii")

Jeśli ustawisz wybierz przekształcania po swojej Unii, będzie można zmienić nakładających się pola lub pola, które nie zostały nazwane ze źródeł headerless. Kliknij przycisk "Sprawdź", aby wyświetlić metadane łączenia z 132 wszystkie kolumny w tym przykładzie z trzech różnych źródeł:

![Złożenia przekształcania końcowego](media/data-flow/union333.png "złożenia 3")

## <a name="name-and-position"></a>Nazwa i położenia

Po wybraniu przycisku "union według nazwy" każdej wartości w kolumnie będzie się zmniejszać do odpowiadającej jej kolumny z każdego źródła, przy użyciu nowego schematu metadanych połączonych.

Jeśli wybierzesz opcję "union według pozycji", każda wartość kolumny będzie się zmniejszać do oryginalnego położenia z każdego odpowiedniego źródła skutkuje nowy strumień połączone danych, gdzie dane z każdego źródła są dodawane do tego samego strumienia:

![Dane wyjściowe złożenia](media/data-flow/unionoutput.png "złożenia danych wyjściowych")

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z podobnych przekształceń, w tym [Dołącz](data-flow-join.md) i [Exists](data-flow-exists.md).
