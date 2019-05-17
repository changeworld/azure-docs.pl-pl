---
title: Skonfigurować przekształcenia ujścia w funkcji mapowania przepływu danych usługi Azure Data Factory
description: Dowiedz się, jak skonfigurować przekształcenie obiektu sink w mapowanie przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596269"
---
# <a name="sink-transformation-for-a-data-flow"></a>Obiekt sink przekształcania przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Po Przekształcanie przepływu danych można ujście danych do docelowego zestawu danych. Przekształcenia ujścia wybierz polecenie definicji zestawu danych, miejsce docelowe danych wyjściowych. Może mieć jako ujście wiele przekształceń, jako przepływ danych wymaga.

Do konta w celu dryfu schematu i zmiany w danych przychodzących do folderu bez zdefiniowanego schematu w wyjściowy zestaw danych będący ujściem danych wyjściowych. Można również uwzględnić zmiany kolumn w źródłach, wybierając **Zezwalaj dryfu schematu** w źródle. Następnie automatycznego wszystkich pól w ujściu.

![Opcje na karcie ujścia, w tym opcja automatycznego mapy](media/data-flow/sink1.png "ujścia 1")

Aby ujścia wszystkie przychodzące pola, należy włączyć funkcję **mapy automatycznie**. Aby wybrać pola obiektu sink do miejsca docelowego lub zmienić nazwy pól w miejscu docelowym, należy wyłączyć opcję **mapy automatycznie**. Następnie otwórz **mapowanie** kartę, aby zamapować pola danych wyjściowych.

![Opcje na karcie mapowania](media/data-flow/sink2.png "ujścia 2")

## <a name="output"></a>Dane wyjściowe 
Dla usługi Azure Blob storage lub typach ujścia usługi Data Lake Storage danych wyjściowych przekształconych danych do folderu. Spark generuje pliki danych partycjonowanych danych wyjściowych oparte na wdrożony schemat partycjonowania używa przekształcania ujścia. 

Możesz ustawić schematu partycjonowania z **Optymalizacja** kartę. Jeśli chcesz, aby usługi Data Factory, aby scalić dane wyjściowe do pojedynczego pliku, wybierz opcję **pojedyncza partycja**.

![Opcje na karcie Optymalizacja](media/data-flow/opt001.png "ujścia opcje")

## <a name="field-mapping"></a>Mapowanie pól

Na **mapowanie** kartę z swoją transformację ujścia przychodzących kolumn po lewej stronie można mapować do miejsc docelowych po prawej stronie. Gdy przepływ danych jest zlew do plików, usługi Data Factory zawsze zapisać nowe pliki w folderze. Podczas mapowania do zestawu danych bazy danych, można wygenerować nową tabelę, która używa tego schematu, ustawiając **Zapisz zasady** do **Zastąp**. Lub wstawianie nowych wierszy w istniejącej tabeli, a następnie mapowania pól do istniejącego schematu. 

![Na karcie mapowania](media/data-flow/sink2.png "wychwytywanie")

W tabeli mapowania można multiselect połączyć wiele kolumn, odłącz wiele kolumn lub mapowanie wiele wierszy do tej samej nazwy kolumny.

Zawsze mapowania przychodzących zestaw pól do obiektu docelowego, i aby w pełni zaakceptować elastycznego schematu definicji, wybierz pozycję **Zezwalaj dryfu schematu**.

![Na karcie mapowania, wyświetlane pola zamapowane do kolumn w zestawie danych](media/data-flow/multi1.png "wiele opcji")

Aby zresetować swoje mapowania kolumn, wybierz **ponownie zamapować**.

![Karta ujście](media/data-flow/sink1.png "ujścia jeden")

Wybierz **sprawdzania poprawności schematu** niepowodzenie ujścia w przypadku zmiany schematu.

Wybierz **wyczyść folder** obciąć zawartość folderu ujścia przed napisaniem pliki docelowe, w tym folderze docelowym.

## <a name="file-name-options"></a>Opcje nazwy pliku

Skonfiguruj sposób nazywania plików: 

   * **Domyślne**: Zezwalaj na platformy Spark do nazwy plików na podstawie ustawień domyślnych części.
   * **Wzorzec**: Wprowadź wzorzec dla plików danych wyjściowych. Na przykład **pożyczek [n]** utworzy loans1.csv, loans2.csv i tak dalej.
   * **Dla każdej partycji**: Wprowadź jedną nazwę pliku dla każdej partycji.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy do wartości kolumny.
   * **Dane wyjściowe do pojedynczego pliku**: Po wybraniu tej opcji ADF łączy pliki wyjściowe podzielonym na partycje z jedną o nazwie pliku. Aby użyć tej opcji, zestaw danych powinna być rozwiązywana na nazwę folderu. Ponadto należy pamiętać, że ta operacja scalania może prawdopodobnie zakończy się niepowodzeniem na podstawie rozmiaru węzła.

> [!NOTE]
> Plik rozpoczęcia operacji tylko wtedy, gdy używasz działania wykonywania przepływu danych. Nie uruchamiania w trybie debugowania przepływu danych.

## <a name="database-options"></a>Opcje bazy danych

Wybierz ustawienia bazy danych:

* **Zaktualizuj metodę**: Wartość domyślna to umożliwia operacje wstawiania. Wyczyść **insert Zezwalaj** Jeśli chcesz zatrzymać wstawianie nowych wierszy ze źródła. Można zaktualizować upsert, lub usunąć wiersze, należy najpierw dodać przekształcania wiersza polecenia alter do tagu wierszy dla tych akcji. 
* **Ponowne utworzenie tabeli**: Porzuć lub utworzenia tabeli docelowej, zanim zostanie zakończone przepływu danych.
* **TRUNCATE table**: Usunąć wszystkie wiersze z tabeli docelowej, zanim zostanie zakończone przepływu danych.
* **Wielkość partii**: Wprowadź liczbę do zasobnika zapisy na fragmenty. Użyj tej opcji w przypadku obciążeń dużych ilości danych. 
* **Włącz tryb przejściowy**: Podczas ładowania usługi Azure Data Warehouse jako zestaw danych ujścia przy użyciu technologii PolyBase.

![Na karcie Ustawienia, przedstawiający SQL ujścia opcje](media/data-flow/alter-row2.png "opcje SQL")

> [!NOTE]
> W przepływu danych można kierować Data Factory, aby utworzyć nową definicję tabeli w docelowej bazie danych. Aby utworzyć definicję tabeli, należy ustawić zestawu danych podczas przekształcania ujścia, który ma nową nazwę tabeli. Wybierz zestaw danych SQL, pod nazwą tabeli **Edytuj** i wprowadź nową nazwę tabeli. Następnie podczas przekształcania ujścia, Włącz **Zezwalaj dryfu schematu**. Ustaw **Importuj schemat** do **Brak**.

![Ustawienia zestawu danych SQL, przedstawiający miejsce edytować nazwę tabeli](media/data-flow/dataset2.png "schematu SQL")

> [!NOTE]
> Podczas aktualizacji lub usuwania wierszy w ujściu usługi bazy danych, należy ustawić kolumny klucza. To ustawienie umożliwia przekształcania wiersza polecenia alter określić unikatowy wiersz Biblioteka przenoszenia danych (DML).

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu przepływu danych, Dodaj [działania potoku przepływu danych](concepts-data-flow-overview.md).
