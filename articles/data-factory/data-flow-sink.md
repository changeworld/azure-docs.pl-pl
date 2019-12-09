---
title: Skonfiguruj transformację ujścia w mapowaniu przepływu danych
description: Dowiedz się, jak skonfigurować transformację ujścia w przepływie danych mapowania.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/03/2019
ms.openlocfilehash: 828487aba651d10e5c906050dab544c097b49762
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930273"
---
# <a name="sink-transformation-for-a-data-flow"></a>Przekształcanie ujścia dla przepływu danych

Po przekształceniu przepływu danych możesz odujścia dane do docelowego zestawu danych. W transformację ujścia Wybierz definicję zestawu danych dla docelowego danych wyjściowych. Może istnieć tyle transformacji ujścia, ile wymaga przepływ danych.

Aby obsłużyć dryfowanie schematu i zmiany w danych przychodzących, należy ujścia danych wyjściowych do folderu bez zdefiniowanego schematu w wyjściowym zestawie danych. Możesz również uwzględnić zmiany kolumn w źródłach, wybierając opcję **Zezwalaj na dryfowanie schematu** w źródle. Następnie Automap wszystkie pola w zlewie.

![Opcje na karcie zlewka z uwzględnieniem opcji Automap](media/data-flow/sink1.png "ujścia 1")

Aby wyprowadzić wszystkie pola przychodzące, Włącz funkcję **Automap**. Aby wybrać pola, które mają być ujścia do miejsca docelowego, lub zmienić nazwy pól w miejscu docelowym, wyłącz **automapowanie**. Następnie otwórz kartę **Mapowanie** , aby zamapować pola wyjściowe.

![Opcje na karcie Mapowanie](media/data-flow/sink2.png "ujścia 2")

## <a name="output"></a>Dane wyjściowe 
W przypadku usługi Azure Blob Storage lub Data Lake Storage typów ujścia dane są wyprowadzane w folderze. Platforma Spark generuje partycjonowane pliki danych wyjściowych na podstawie schematu partycjonowania używanego przez transformację ujścia. 

Schemat partycjonowania można ustawić na karcie **Optymalizacja** . Jeśli chcesz, aby Data Factory scalał dane wyjściowe w jeden plik, wybierz **jedną partycję**. Jeśli chcesz zachować lub utworzyć partycjonowane foldery, Użyj partycji **klucza** i ustaw klucze, które mają być używane dla partycjonowanych struktur folderów.

![Opcje na karcie Optymalizacja](media/data-flow/opt001.png "Opcje ujścia")

## <a name="field-mapping"></a>Mapowanie pola
Na karcie **Mapowanie** przekształcenia ujścia można mapować kolumny przychodzące po lewej stronie do miejsc docelowych po prawej stronie. Gdy ujścia danych są przesyłane do plików, Data Factory będą zawsze zapisywać nowe pliki do folderu. Po zmapowaniu do zestawu danych bazy wiedzy wybierz opcję operacje tabeli bazy danych, która będzie wstawiana, aktualizowana, upsert lub usuwana.

![Karta mapowanie](media/data-flow/sink2.png "Ujścia")

W tabeli mapowanie można wybrać wiele kolumn, odłączyć wiele kolumn lub zmapować wiele wierszy na tę samą nazwę kolumny.

Aby zawsze mapować przychodzący zestaw pól na obiekt docelowy, ponieważ są one i w pełni akceptują elastyczne definicje schematu, wybierz opcję **Zezwalaj na dryfowanie schematu**.

![Karta mapowanie pokazująca pola mapowane na kolumny w zestawie danych](media/data-flow/multi1.png "wiele opcji")

Aby zresetować mapowania kolumn, wybierz pozycję **ponownie Mapuj**.

![Karta ujścia](media/data-flow/sink1.png "Ujścia — jeden")

Wybierz pozycję **Weryfikuj schemat** , aby zakończyć niepowodzeniem ujścia w przypadku zmiany schematu.

Wybierz pozycję **Wyczyść folder,** aby obciąć zawartość folderu ujścia przed zapisaniem plików docelowych w tym folderze docelowym.

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Stałe mapowanie a mapowanie oparte na regułach
Po wyłączeniu automapowania będzie można dodać mapowanie oparte na kolumnach (stałe mapowanie) lub mapowanie oparte na regułach. Mapowanie oparte na regułach umożliwi pisanie wyrażeń z dopasowaniem do wzorca, podczas gdy stałe mapowanie spowoduje zamapowanie nazw kolumn logicznych i fizycznych.

![Mapowanie oparte na regułach](media/data-flow/rules4.png "Mapowanie oparte na regułach")

Po wybraniu mapowania opartego na regułach, nastąpi naliczanie PODAJNIKa, aby oszacować pasujące wyrażenie zgodne z regułami przychodzącego wzorca i zdefiniować nazwy pól wychodzących. Możesz dodać dowolną kombinację mapowań pól i reguł. Nazwy pól są następnie generowane w czasie wykonywania przez moduł ADF na podstawie przychodzących metadanych ze źródła. Podczas debugowania można wyświetlać nazwy wygenerowanych pól i korzystać z okienka Podgląd danych.

Szczegóły dopasowania wzorca znajdują się w [dokumentacji wzorca kolumny](concepts-data-flow-column-pattern.md).

Można także wprowadzać wzorce wyrażeń regularnych w przypadku używania dopasowania opartego na regułach, rozszerzając wiersz i wprowadzając wyrażenie regularne obok "nazwa pasuje:".

![Mapowanie wyrażenia regularnego](media/data-flow/scdt1g4.png "Mapowanie wyrażenia regularnego")

Bardzo prostym typowym przykładem dla mapowania opartego na regułach i stałego mapowania jest przypadek, w którym chcesz zmapować wszystkie pola przychodzące na taką samą nazwę w miejscu docelowym. W przypadku stałych mapowań należy wyświetlić poszczególne kolumny w tabeli. W przypadku mapowania opartego na regułach istnieje jedna reguła, która mapuje wszystkie pola przy użyciu ```true()``` do tej samej nazwy pola przychodzącego reprezentowanej przez ```$$```.

### <a name="sink-association-with-dataset"></a>Skojarzenie ujścia z zestawem danych

Zestaw danych, który został wybrany dla obiektu sink, może lub nie ma zdefiniowanego schematu w definicji zestawu danych. Jeśli nie ma zdefiniowanego schematu, należy zezwolić na dryfowanie schematu. Po zdefiniowaniu stałego mapowania Mapowanie nazw logicznie do fizycznych będzie trwało w transformacji ujścia. W przypadku zmiany definicji schematu zestawu danych można przerwać mapowanie ujścia. Aby tego uniknąć, należy użyć mapowania opartego na regułach. Mapowania oparte na regułach są uogólnione, co oznacza, że zmiany schematu w zestawie danych nie spowodują przerwania mapowania.

## <a name="file-name-options"></a>Opcje nazwy pliku

Skonfiguruj nazewnictwo plików: 

   * **Domyślnie**: Zezwalaj platformie Spark na nazwy plików na podstawie wartości domyślnych części.
   * **Wzorzec**: wprowadź wzorzec dla plików wyjściowych. Na przykład **pożyczki [n]** spowodują utworzenie loans1. csv, loans2. csv i tak dalej.
   * **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny.
   * **Wyprowadzanie danych wyjściowych do pojedynczego pliku**: w przypadku wybrania tej opcji na stronie ADF zostaną połączone pliki wynikowe partycjonowane w jeden nazwany plik. Aby można było użyć tej opcji, zestaw danych powinien zostać rozpoznany jako nazwa folderu. Należy również pamiętać, że ta operacja scalania może się nie powieść w zależności od rozmiaru węzła.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy uruchamiasz działanie wykonywania przepływu danych. Nie zaczynają się one w trybie debugowania przepływu danych.

## <a name="database-options"></a>Opcje bazy danych

Wybierz pozycję Ustawienia bazy danych:

![Karta Ustawienia z opcjami ujścia SQL](media/data-flow/alter-row2.png "Opcje SQL")

* **Metoda aktualizacji**: domyślnie zezwala na operacje wstawiania. Usuń zaznaczenie pola wyboru **Zezwalaj** , jeśli chcesz przerwać wstawianie nowych wierszy ze źródła. Aby zaktualizować, upsert lub usunąć wiersze, najpierw Dodaj transformację ALTER-Row do wierszy tagów dla tych działań. 
* **Utwórz ponownie tabelę**: upuść lub Utwórz tabelę docelową przed zakończeniem przepływu danych.
* **Obetnij tabelę**: Usuń wszystkie wiersze z tabeli docelowej przed ukończeniem przepływu danych.
* **Rozmiar wsadu**: wprowadź liczbę, aby przetworzyć przedziały do fragmentów. Użyj tej opcji w przypadku dużych obciążeń danych. 
* **Włącz przemieszczanie**: Użyj podstawy podczas ładowania usługi Azure Data Warehouse jako zestawu danych ujścia.
* **Wstępne i gotowe skrypty SQL**: wprowadź wielowierszowe skrypty SQL, które zostaną wykonane przed (przetwarzanie wstępne) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia

![wstępne i końcowe skrypty przetwarzania SQL](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

> [!NOTE]
> W przepływie danych można skierować Data Factory, aby utworzyć nową definicję tabeli w docelowej bazie danych. Aby utworzyć definicję tabeli, należy ustawić zestaw danych w transformację ujścia, która ma nową nazwę tabeli. W zestawie danych SQL, pod nazwą tabeli, wybierz opcję **Edytuj** i wprowadź nową nazwę tabeli. Następnie w transformacji ujścia Włącz opcję **Zezwalaj na dryfowanie schematu**. Ustaw dla opcji **Importuj schemat** **wartość Brak**.

![Ustawienia zestawu danych SQL, gdzie można edytować nazwę tabeli](media/data-flow/dataset2.png "Schemat SQL")

> [!NOTE]
> W przypadku aktualizowania lub usuwania wierszy w ujścia bazy danych należy ustawić kolumnę klucza. To ustawienie umożliwia przekształcenie zmiany wiersza w celu określenia unikatowego wiersza w bibliotece przenoszenia danych (DML).

### <a name="cosmosdb-specific-settings"></a>Ustawienia CosmosDB

Podczas wypełniania danych w CosmosDB należy wziąć pod uwagę następujące dodatkowe opcje:

* Klucz partycji: to pole jest wymagane. Wprowadź ciąg, który reprezentuje klucz partycji dla kolekcji. Przykład: ```/movies/title```
* Przepływność: Ustaw opcjonalną wartość dla liczby jednostek ru, która ma zostać zastosowana do kolekcji CosmosDB dla każdego wykonywania tego przepływu danych. Wartość minimalna to 400.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu przepływu danych Dodaj [działanie przepływu danych do potoku](concepts-data-flow-overview.md).
