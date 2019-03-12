---
title: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
description: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 1eebc879ad56ba4f35e6a8a1b857ae877a6a2f01
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726264"
---
# <a name="mapping-data-flow-union-transformation"></a>Złożenia Przekształcanie przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Unii łączy wiele strumieni danych w jednym z SQL Unii tych strumieni jako nowe dane wyjściowe z transformacji Unii. Wszystkie schematu z każdego strumienia wejściowego zostanie połączona wewnątrz przepływu danych, bez konieczności klucz sprzężenia.

N liczbę strumieni w tabeli ustawień można łączyć, wybierając ikonę "+" obok każdego skonfigurowanego wiersza.

![Przekształcenie złożenia](media/data-flow/union.png "Unii")

W takim przypadku można łączyć różnych metadanych z wielu źródeł (w tym przykładzie trzy innych plików źródłowych) i połączyć je w jednym strumień:

![Przegląd Przekształcanie złożenia](media/data-flow/union111.png "złożenia 1")

Aby to osiągnąć, należy dodać dodatkowe wiersze w ustawieniach Unii, umieszczając wszystkie źródła, które chcesz dodać. Nie ma potrzeby wspólnego klucza wyszukiwania lub łączony albo oczekuje:

![Ustawienia Union przekształcania](media/data-flow/unionsettings.png "ustawienia Unii")

Jeśli ustawisz wybierz przekształcania po swojej Unii, będzie można zmienić nakładających się pola lub pola, które nie zostały nazwane ze źródeł headerless. Kliknij przycisk "Sprawdź", aby wyświetlić metadane łączenia z 132 wszystkie kolumny w tym przykładzie z trzech różnych źródeł:

![Złożenia przekształcania końcowego](media/data-flow/union333.png "złożenia 3")
