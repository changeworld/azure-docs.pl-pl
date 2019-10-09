---
title: Skonfiguruj transformację ujścia w funkcji przepływu danych mapowania Azure Data Factory
description: Dowiedz się, jak skonfigurować transformację ujścia w przepływie danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: be2ab5605f7fa60ebb78493f714648d458e82a6c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029243"
---
# <a name="sink-transformation-for-a-data-flow"></a>Przekształcanie ujścia dla przepływu danych



Po przekształceniu przepływu danych możesz odujścia dane do docelowego zestawu danych. W transformację ujścia Wybierz definicję zestawu danych dla docelowego danych wyjściowych. Może istnieć tyle transformacji ujścia, ile wymaga przepływ danych.

Aby obsłużyć dryfowanie schematu i zmiany w danych przychodzących, należy ujścia danych wyjściowych do folderu bez zdefiniowanego schematu w wyjściowym zestawie danych. Możesz również uwzględnić zmiany kolumn w źródłach, wybierając opcję **Zezwalaj na dryfowanie schematu** w źródle. Następnie Automap wszystkie pola w zlewie.

![Opcje na karcie ujścia, w tym ujścia opcji Automap](media/data-flow/sink1.png "1")

Aby wyprowadzić wszystkie pola przychodzące, Włącz funkcję **Automap**. Aby wybrać pola, które mają być ujścia do miejsca docelowego, lub zmienić nazwy pól w miejscu docelowym, wyłącz **automapowanie**. Następnie otwórz kartę **Mapowanie** , aby zamapować pola wyjściowe.

![Opcje na karcie Mapowanie](media/data-flow/sink2.png "ujścia 2")

## <a name="output"></a>Dane wyjściowe 
W przypadku usługi Azure Blob Storage lub Data Lake Storage typów ujścia dane są wyprowadzane w folderze. Platforma Spark generuje partycjonowane pliki danych wyjściowych na podstawie schematu partycjonowania używanego przez transformację ujścia. 

Schemat partycjonowania można ustawić na karcie **Optymalizacja** . Jeśli chcesz, aby Data Factory scalał dane wyjściowe w jeden plik, wybierz **jedną partycję**.

![Opcje na karcie Optymalizacja](media/data-flow/opt001.png "opcji ujścia")

## <a name="field-mapping"></a>Mapowanie pól
Na karcie **Mapowanie** przekształcenia ujścia można mapować kolumny przychodzące po lewej stronie do miejsc docelowych po prawej stronie. Gdy ujścia danych są przesyłane do plików, Data Factory będą zawsze zapisywać nowe pliki do folderu. Po zmapowaniu do zestawu danych bazy wiedzy wybierz opcję operacje tabeli bazy danych, która będzie wstawiana, aktualizowana, upsert lub usuwana.

(media/data-flow/sink2.png "Pochłaniacze") ![kart mapowania]

W tabeli mapowanie można wybrać wiele kolumn, odłączyć wiele kolumn lub zmapować wiele wierszy na tę samą nazwę kolumny.

Aby zawsze mapować przychodzący zestaw pól na obiekt docelowy, ponieważ są one i w pełni akceptują elastyczne definicje schematu, wybierz opcję **Zezwalaj na dryfowanie schematu**.

![Karta mapowanie pokazująca pola mapowane na kolumny w](media/data-flow/multi1.png "wielu opcjach") zestawu danych

Aby zresetować mapowania kolumn, wybierz pozycję **ponownie Mapuj**.

(media/data-flow/sink1.png "Ujścia") ![karty ujścia]

Wybierz pozycję **Weryfikuj schemat** , aby zakończyć niepowodzeniem ujścia w przypadku zmiany schematu.

Wybierz pozycję **Wyczyść folder,** aby obciąć zawartość folderu ujścia przed zapisaniem plików docelowych w tym folderze docelowym.

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach
Gdy automapowanie jest wyłączone, będzie można dodać mapowanie oparte na kolumnach (stałe mapowanie) lub mapowanie oparte na regułach. Mapowanie oparte na regułach umożliwi pisanie wyrażeń z dopasowywaniem do wzorca. 

Mapowanie oparte na(media/data-flow/rules4.png "regułach") ![mapowania oparte na regułach]

Po wybraniu mapowania opartego na regułach, nastąpi naliczanie PODAJNIKa, aby oszacować pasujące wyrażenie zgodne z regułami przychodzącego wzorca i zdefiniować nazwy pól wychodzących. Możesz dodać dowolną kombinację mapowań pól i reguł. Nazwy pól są następnie generowane w czasie wykonywania przez moduł ADF na podstawie przychodzących metadanych ze źródła. Podczas debugowania można wyświetlać nazwy wygenerowanych pól i korzystać z okienka Podgląd danych.

Szczegóły dopasowania wzorca znajdują się w [dokumentacji wzorca kolumny](concepts-data-flow-column-pattern.md).

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

![Karta Ustawienia pokazująca opcje SQL dotyczące opcji UJŚCIA SQL](media/data-flow/alter-row2.png "")

* **Metoda aktualizacji**: domyślnie zezwala na operacje wstawiania. Usuń zaznaczenie pola wyboru **Zezwalaj** , jeśli chcesz przerwać wstawianie nowych wierszy ze źródła. Aby zaktualizować, upsert lub usunąć wiersze, najpierw Dodaj transformację ALTER-Row do wierszy tagów dla tych działań. 
* **Utwórz ponownie tabelę**: upuść lub Utwórz tabelę docelową przed zakończeniem przepływu danych.
* **Obetnij tabelę**: Usuń wszystkie wiersze z tabeli docelowej przed ukończeniem przepływu danych.
* **Rozmiar wsadu**: wprowadź liczbę, aby przetworzyć przedziały do fragmentów. Użyj tej opcji w przypadku dużych obciążeń danych. 
* **Włącz przemieszczanie**: Użyj podstawy podczas ładowania usługi Azure Data Warehouse jako zestawu danych ujścia.
* **Wstępne i gotowe skrypty SQL**: wprowadź wielowierszowe skrypty SQL, które zostaną wykonane przed (przetwarzanie wstępne) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia

![wstępne i końcowe]skrypty przetwarzania SQL skrypty(media/data-flow/prepost1.png "przetwarzania SQL")

> [!NOTE]
> W przepływie danych można skierować Data Factory, aby utworzyć nową definicję tabeli w docelowej bazie danych. Aby utworzyć definicję tabeli, należy ustawić zestaw danych w transformację ujścia, która ma nową nazwę tabeli. W zestawie danych SQL, pod nazwą tabeli, wybierz opcję **Edytuj** i wprowadź nową nazwę tabeli. Następnie w transformacji ujścia Włącz opcję **Zezwalaj na dryfowanie schematu**. Ustaw dla opcji **Importuj schemat** **wartość Brak**.

![Ustawienia zestawu danych SQL, gdzie można edytować nazwę tabeli](media/data-flow/dataset2.png "schemat SQL")

> [!NOTE]
> W przypadku aktualizowania lub usuwania wierszy w ujścia bazy danych należy ustawić kolumnę klucza. To ustawienie umożliwia przekształcenie zmiany wiersza w celu określenia unikatowego wiersza w bibliotece przenoszenia danych (DML).

## <a name="next-steps"></a>Następne kroki
Po utworzeniu przepływu danych Dodaj [działanie przepływu danych do potoku](concepts-data-flow-overview.md).
