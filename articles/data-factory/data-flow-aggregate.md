---
title: Przekształcanie agregacji w przepływie danych mapowania Azure Data Factory
description: Dowiedz się, jak agregować dane w skali w Azure Data Factory przy użyciu transformacji zagregowanej przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f21367c36ae31aa2115c109933a581bef464baf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676910"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregowanie transformacji w strumieniu danych mapowania 

Zagregowana transformacja definiuje agregacje kolumn w strumieniach danych. Korzystając z konstruktora wyrażeń, można definiować różne typy agregacji, takie jak SUM, MIN, MAX i COUNT pogrupowane według istniejących lub obliczanych kolumn.

## <a name="group-by"></a>Grupuj według

Wybierz istniejącą kolumnę lub Utwórz nową kolumnę obliczaną, która ma być używana jako klauzula GROUP by dla agregacji. Aby użyć istniejącej kolumny, wybierz ją z listy rozwijanej. Aby utworzyć nową kolumnę obliczaną, umieść kursor nad klauzulą i kliknij **kolumnę obliczaną**. Spowoduje to otwarcie [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md). Po utworzeniu kolumny obliczanej wprowadź nazwę kolumny wyjściowej w polu **Nazwa jako** . Jeśli chcesz dodać dodatkową klauzulę Group by, umieść kursor nad istniejącą klauzulą i kliknij ikonę znaku plus.

![Agregacja grupy transformacji według ustawień](media/data-flow/agg.png "Agregacja grupy transformacji według ustawień")

Klauzula GROUP by jest opcjonalna w transformacji agregowanej.

## <a name="aggregate-column"></a>Kolumna agregująca 

Przejdź do karty **agregaty** , aby skompilować wyrażenia agregacji. Można zastąpić istniejącą kolumnę agregacją lub utworzyć nowe pole z nową nazwą. Wyrażenie agregacji jest wprowadzane w polu po prawej stronie obok selektora nazw kolumn. Aby edytować wyrażenie, kliknij pole tekstowe, aby otworzyć program Expression Builder. Aby dodać dodatkowe agregacje, umieść kursor na istniejącym wyrażeniu i kliknij ikonę Plus, aby utworzyć nową kolumnę agregacji lub [wzorzec kolumny](concepts-data-flow-column-pattern.md).

Każde wyrażenie agregacji musi zawierać co najmniej jedną funkcję agregującą.

![Zagregowane ustawienia agregacji transformacji](media/data-flow/agg2.png "Zagregowane ustawienia agregacji transformacji")


> [!NOTE]
> W trybie debugowania Konstruktor wyrażeń nie może tworzyć podglądów danych za pomocą funkcji agregujących. Aby wyświetlić podglądy danych dla przekształceń agregowanych, należy zamknąć konstruktora wyrażeń i wyświetlić dane za pomocą karty "Podgląd danych".

## <a name="reconnect-rows-and-columns"></a>Połącz ponownie wiersze i kolumny

Przekształcenia agregowane przypominają zapytania select SQL Aggregate. Kolumny, które nie znajdują się w klauzuli Group by ani funkcji agregujących, nie będą przepływać do danych wyjściowych przekształcenia agregowanego. Jeśli chcesz uwzględnić inne kolumny w zagregowanych danych wyjściowych, wykonaj jedną z następujących metod:

* Użyj funkcji agregującej, takiej jak `last()` lub `first()`, aby uwzględnić tę dodatkową kolumnę.
* Ponownie Dołącz kolumny do strumienia wyjściowego przy użyciu [wzorca samosprzężenia](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład pobiera `MoviesYear` strumienia przychodzącego i grupuje wiersze według kolumny `year`. Transformacja tworzy kolumnę agregującą `avgrating`, która oblicza średnią `Rating`kolumn. Ta transformacja agregacji ma nazwę `AvgComedyRatingsByYear`.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Grupuj według przykładu](media/data-flow/agg-script1.png "Grupuj według przykładu")

![Przykład agregacji](media/data-flow/agg-script2.png "Przykład agregacji")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Następne kroki

* Definiowanie agregacji opartej na oknach przy użyciu [transformacji okna](data-flow-window.md)
