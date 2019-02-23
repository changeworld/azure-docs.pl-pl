---
title: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dba043721c2d81b7fe2c254f62328e54bb959cdc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729376"
---
# <a name="mapping-data-flow-sink-transformation"></a>Mapowanie przekształcenie obiektu Sink przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opcje ujścia](media/data-flow/windows1.png "ujścia 1")

Po ukończeniu swojej Przekształcanie przepływu danych można ujścia przekształconych danych do docelowego zestawu danych. Podczas przekształcania ujścia możesz wybrać definicji zestawu danych, które mają być używane miejsce docelowe danych wyjściowych. Możesz mieć dowolną liczbę przekształcania ujścia jako przepływ danych wymaga.

Powszechną praktyką konta dla zmieniających się danych przychodzących i uwzględnić dryfu schematu jest ujście danych wyjściowych do folderu bez zdefiniowanego schematu w wyjściowy zestaw danych. Można również uwzględnione wszystkie zmiany kolumny w źródłach, wybierając pozycję "Zezwalaj na schemat odejściem od tego stanu" w źródłowej, a następnie automatycznego wszystkie pola w ujściu.

Można zastąpić, dołączania lub się nie powieść przepływ danych wychwytywania do zestawu danych.

Możesz również "Mapuj automatycznie" do ujścia wszystkie pola przychodzących. Jeśli chcesz wybrać pola, które mają być zlew do miejsca docelowego lub chcesz zmienić nazwy pól w miejscu docelowym, to należy wybrać "Off" Mapuj "automatycznie", a następnie kliknij na karcie mapowania, aby zamapować pola danych wyjściowych:

![Opcje ujścia](media/data-flow/sink2.png "ujścia 2")

## <a name="output-to-one-file"></a>Dane wyjściowe do jednego pliku
W przypadku typów ujścia usługi Azure Storage Blob lub Data Lake zwróci przekształconych danych do folderu. Spark spowoduje wygenerowanie plików danych partycjonowanych danych wyjściowych oparte na schemat partycjonowania używany w transformacji ujścia. Możesz ustawić schematu partycjonowania, klikając kartę "Optymalizuj". Jeśli chcesz ADF, aby scalić dane wyjściowe do pojedynczego pliku, kliknij przycisk radiowy "Jednej partycji".

![Opcje ujścia](media/data-flow/opt001.png "ujścia opcje")

### <a name="output-settings"></a>Ustawienia danych wyjściowych

Zastępowanie spowoduje obciąć tabeli, jeśli istnieje, a następnie ponownie go utworzyć i załadować dane. Dołącz będzie wstawianie nowych wierszy. Jeśli tabeli na podstawie nazwy tabeli zestawu danych nie istnieje na wszystkich docelowych ADW, przepływ danych będzie utworzyć tabelę, a następnie załadowanie danych.

Jeśli wyłączysz "Map automatycznie", można mapować pól do tabeli docelowej ręcznie.

![Obiekt sink opcje ADW](media/data-flow/adw2.png "adw ujścia")

#### <a name="field-mapping"></a>Mapowanie pól

Na karcie mapowania swoją transformację ujścia można mapować kolumny przychodzące (lewa strona), do miejsca docelowego (prawa strona). Gdy przepływ danych jest zlew do plików, ADF zawsze zapisać nowe pliki w folderze. Podczas mapowania do zestawu danych bazy danych, można wybrać wygenerować nową tabelę przy użyciu tego schematu (wartość zapisać zasady "Zastąp") lub Wstaw nowe wiersze do istniejącej tabeli i mapowania pól do istniejącego schematu.

Możliwość wielokrotnego wyboru tabeli mapowania umożliwia łączenie wielu kolumn za pomocą jednego kliknięcia, odłącz wiele kolumn lub mapowanie wiele wierszy do tej samej nazwy kolumny.

![Mapowania pól](media/data-flow/multi1.png "wiele opcji")

Jeśli chcesz zresetować mapowania kolumn, naciśnij przycisk "Ponownie zamapować", aby zresetować mapowań.

![Połączenia](media/data-flow/maxcon.png "Połączenia")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Aktualizacje do ujścia przekształcania dla wersji Ogólnodostępnej usługi ADF w wersji 2

![Opcje ujścia](media/data-flow/sink1.png "ujścia jeden")

![Opcje ujścia](media/data-flow/sink2.png "wychwytywanie")

* Zezwalaj na opcje schematu odejściem od tego stanu i sprawdzanie poprawności schematu są teraz dostępne w ujściu. Pozwoli to nakazać usługi ADF, aby w pełni zaakceptować elastycznego schematu definicji (schemat dryfu) lub się nie powieść ujścia, jeśli schemat zmienia się (Sprawdzanie poprawności schematu).

* Usuń Folder. ADF obetnie ujścia zawartość folderu przed napisaniem pliki docelowe, w tym folderze docelowym.

* Opcje nazwy pliku

   * Domyślne: Zezwalaj na platformy Spark do nazwy plików na podstawie ustawień domyślnych część
   * Wzorzec: Wprowadź nazwę dla plików danych wyjściowych
   * Dla każdej partycji: Wprowadź nazwę pliku na partycję
   * Jako dane w kolumnie: Ustaw plik wyjściowy do wartości kolumny

> [!NOTE]
> Operacje na plikach będą wykonywane tylko wtedy, gdy uruchamiasz działania wykonania przepływu danych, a nie w trybie debugowania przepływu danych

W przypadku typów ujścia SQL można ustawić:

* Obcinanie tabeli
* Utwórz tabelę (sprawdza listy/tworzenia)
* Rozmiar wsadu dla dużej ilości danych. Wprowadź liczbę do zasobnika zapisy na fragmenty.

![Mapowanie pól](media/data-flow/sql001.png "opcje SQL")

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu przepływu danych, Dodaj [wykonania przepływu danych działania potoku](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
