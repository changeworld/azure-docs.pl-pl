---
title: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a39fa0949276b7e86c7fdd0d0861492a9a0b723e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438636"
---
# <a name="mapping-data-flow-sink-transformation"></a>Mapowanie przekształcenie obiektu Sink przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opcje ujścia](media/data-flow/sink1.png "ujścia 1")

Po ukończeniu swojej Przekształcanie przepływu danych można ujścia przekształconych danych do docelowego zestawu danych. Podczas przekształcania ujścia możesz wybrać definicji zestawu danych, które mają być używane miejsce docelowe danych wyjściowych. Możesz mieć dowolną liczbę przekształcania ujścia jako przepływ danych wymaga.

Powszechną praktyką konta dla zmieniających się danych przychodzących i uwzględnić dryfu schematu jest ujście danych wyjściowych do folderu bez zdefiniowanego schematu w wyjściowy zestaw danych. Można również uwzględnione wszystkie zmiany kolumny w źródłach, wybierając pozycję "Zezwalaj na schemat odejściem od tego stanu" w źródłowej, a następnie automatycznego wszystkie pola w ujściu.

Można zastąpić, dołączania lub się nie powieść przepływ danych wychwytywania do zestawu danych.

Możesz również "Mapuj automatycznie" do ujścia wszystkie pola przychodzących. Jeśli chcesz wybrać pola, które mają być zlew do miejsca docelowego lub chcesz zmienić nazwy pól w miejscu docelowym, to należy wybrać "Off" Mapuj "automatycznie", a następnie kliknij na karcie mapowania, aby zamapować pola danych wyjściowych:

![Opcje ujścia](media/data-flow/sink2.png "ujścia 2")

## <a name="output-to-one-file"></a>Dane wyjściowe do jednego pliku
W przypadku typów ujścia usługi Azure Storage Blob lub Data Lake zwróci przekształconych danych do folderu. Spark spowoduje wygenerowanie plików danych partycjonowanych danych wyjściowych oparte na schemat partycjonowania używany w transformacji ujścia. Możesz ustawić schematu partycjonowania, klikając kartę "Optymalizuj". Jeśli chcesz ADF, aby scalić dane wyjściowe do pojedynczego pliku, kliknij przycisk radiowy "Jednej partycji".

![Opcje ujścia](media/data-flow/opt001.png "ujścia opcje")

## <a name="field-mapping"></a>Mapowanie pól

Na karcie mapowania swoją transformację ujścia można mapować kolumny przychodzące (lewa strona), do miejsca docelowego (prawa strona). Gdy przepływ danych jest zlew do plików, ADF zawsze zapisać nowe pliki w folderze. Podczas mapowania do zestawu danych bazy danych, można wybrać wygenerować nową tabelę przy użyciu tego schematu (wartość zapisać zasady "Zastąp") lub Wstaw nowe wiersze do istniejącej tabeli i mapowania pól do istniejącego schematu.

Możliwość wielokrotnego wyboru tabeli mapowania umożliwia łączenie wielu kolumn za pomocą jednego kliknięcia, odłącz wiele kolumn lub mapowanie wiele wierszy do tej samej nazwy kolumny.

Jeśli chcesz zawsze przyjmują przychodzący zestaw pól i zamapować je na obiekt docelowy jako — ustawienie "Zezwalaj na schemat odejściem od tego stanu".

![Mapowania pól](media/data-flow/multi1.png "wiele opcji")

Jeśli chcesz zresetować mapowania kolumn, naciśnij przycisk "Ponownie zamapować", aby zresetować mapowań.

![Opcje ujścia](media/data-flow/sink1.png "ujścia jeden")

![Opcje ujścia](media/data-flow/sink2.png "wychwytywanie")

* Zezwalaj na opcje schematu odejściem od tego stanu i sprawdzanie poprawności schematu są teraz dostępne w ujściu. Pozwoli to nakazać usługi ADF, aby w pełni zaakceptować elastycznego schematu definicji (schemat dryfu) lub się nie powieść ujścia, jeśli schemat zmienia się (Sprawdzanie poprawności schematu).

* Usuń Folder. ADF obetnie ujścia zawartość folderu przed napisaniem pliki docelowe, w tym folderze docelowym.

## <a name="file-name-options"></a>Opcje nazwy pliku

   * Domyślne: Zezwalaj na platformy Spark do nazwy plików na podstawie ustawień domyślnych część
   * Wzorzec: Wprowadź wzorzec dla plików danych wyjściowych. Na przykład "pożyczek [n]" spowoduje utworzenie loans1.csv, loans2.csv,...
   * Dla każdej partycji: Wprowadź nazwę pliku na partycję
   * Jako dane w kolumnie: Ustaw plik wyjściowy do wartości kolumny

> [!NOTE]
> Operacje na plikach będą wykonywane tylko wtedy, gdy uruchamiasz działania wykonania przepływu danych, a nie w trybie debugowania przepływu danych

## <a name="database-options"></a>Opcje bazy danych

* Zezwalaj na insert, update, delete, wykonuje operację UPSERT. Wartość domyślna to umożliwia operacje wstawiania. Jeśli użytkownik chce aktualizacji, upsert lub usunąć wiersze, należy najpierw dodać Przekształcenie wiersza polecenia alter do tagu wierszy dla tych określonych akcji. Wyłączenie opcji "Zezwalaj insert" przestanie ADF z wstawianie nowych wierszy ze źródła.
* Obcinanie tabeli (spowoduje to usunięcie wszystkich wierszy z tabeli docelowej przed ukończeniem przepływu danych)
* Utwórz tabelę (sprawdza pozycję listy/Tworzenie tabeli docelowej przed ukończeniem przepływu danych)
* Rozmiar wsadu dla dużej ilości danych. Wprowadź liczbę do zasobnika zapisy na fragmenty
* Włącz tryb przejściowy: Spowoduje to poinstruować usługi ADF, aby podczas ładowania usługi Azure Data Warehouse jako zestaw danych ujścia przy użyciu technologii Polybase

> [!NOTE]
> W przepływu danych możesz poprosić usługi ADF, aby utworzyć nową definicję tabeli w docelowej bazie danych przez ustawienie zestawu danych podczas przekształcania ujścia, który ma nową nazwę tabeli. W zestawie danych SQL kliknij przycisk "Edit" poniżej nazwę tabeli i wprowadź nową nazwę tabeli. Następnie podczas przekształcania ujścia, włącz "Zezwalaj na schemat odejściem od tego stanu". Seth ustawienie "Importuj schemat" na wartość None.

![Źródło przekształcenia schematu](media/data-flow/dataset2.png "schematu SQL")

![SQL ujścia opcje](media/data-flow/alter-row2.png "opcje SQL")

> [!NOTE]
> Podczas aktualizowania lub usuwania wierszy w ujściem Twojej bazy danych, należy ustawić kolumny klucza. W ten sposób jest w stanie określić unikatowy wiersz DML wiersza polecenia Alter.

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu przepływu danych, Dodaj [wykonania przepływu danych działania potoku](concepts-data-flow-overview.md).
