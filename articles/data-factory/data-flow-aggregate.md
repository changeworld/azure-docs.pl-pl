---
title: Agregowanie transformacji w przepływie danych mapowania — Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak agregować dane w skali w Azure Data Factory przy użyciu transformacji zagregowanej przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 1dcc28313d1d8e59024fbc70738567cb59585d20
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326490"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregowanie transformacji w strumieniu danych mapowania 



Przekształcenie agregowane polega na tym, że można zdefiniować agregacje kolumn w strumieniach danych. Korzystając z konstruktora wyrażeń, można definiować różne typy agregacji, takie jak SUM, MIN, MAX i COUNT, które mogą być pogrupowane według istniejących lub obliczanych kolumn.

## <a name="group-by"></a>Grupuj według
Wybierz istniejącą kolumnę lub Utwórz nową kolumnę obliczaną, która ma być używana jako klauzula GROUP by dla agregacji. Aby użyć istniejącej kolumny, wybierz żądaną kolumnę z listy rozwijanej. Aby utworzyć nową kolumnę obliczaną, umieść kursor nad klauzulą i kliknij pozycję "kolumna obliczana". Spowoduje to otwarcie [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md). Po utworzeniu kolumny obliczanej wprowadź nazwę kolumny wyjściowej w polu "Nazwa jako". Jeśli chcesz dodać dodatkową klauzulę Group by, umieść kursor nad istniejącą klauzulą i kliknij przycisk "+".

![Agreguj grupę transformacji według ustawień](media/data-flow/agg.png "Agreguj grupę transformacji według ustawień")

> [!NOTE]
> Klauzula GROUP by jest opcjonalna w transformacji agregowanej.

## <a name="aggregate-column"></a>Kolumna agregująca 
Wybierz kartę "agregacje", aby utworzyć wyrażenia agregacji. Możesz wybrać istniejącą kolumnę i zastąpić wartość agregacją lub utworzyć nowe pole z nową nazwą. Wyrażenie agregacji jest wprowadzane w polu po prawej stronie obok selektora nazw kolumn. Aby edytować wyrażenie, kliknij pole tekstowe, aby otworzyć program Expression Builder. Aby dodać dodatkową agregację, umieść kursor na istniejącym wyrażeniu i kliknij znak "+", aby utworzyć nową kolumnę agregacji lub [wzorzec kolumny](concepts-data-flow-column-pattern.md).

Agregacja zagregowanych ![ustawień](media/data-flow/agg2.png "agregacji zagregowanych") ustawień agregacji

> [!NOTE]
> Każde wyrażenie agregacji musi zawierać co najmniej jedną funkcję agregującą.

> [!NOTE]
> W trybie debugowania Konstruktor wyrażeń nie może tworzyć podglądów danych za pomocą funkcji agregujących. Aby wyświetlić podglądy danych dla przekształceń agregowanych, należy zamknąć konstruktora wyrażeń i wyświetlić dane za pomocą karty "Podgląd danych".

## <a name="reconnect-rows-and-columns"></a>Połącz ponownie wiersze i kolumny
Przekształcenia agregowane są ściśle równoważne z kwerendami SELECT SQL Aggregate. Kolumny, które nie znajdują się w klauzuli Group by ani funkcjach agregujących, nie przepływają do danych wyjściowych przekształcenia agregowanego. Jeśli istnieją inne kolumny, które mają zostać uwzględnione w danych wyjściowych zagregowanych wierszy, należy wykonać jedną z:

* Użyj funkcji agregującej, aby dołączyć tę dodatkową kolumnę, taką jak Last () lub First ()
* Dołącz ponownie kolumny przed agregacją przy użyciu [wzorca samosprzężenia](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Skrypt przepływu danych

Skrypt agregacji(media/data-flow/aggdfs1.png "zagregowanego") ![skryptu przepływu danych]

* ```MoviesYear```: kolumna pochodna definiująca kolumny Year i title
* ```AvgComedyRatingByYear```: Przekształcenie agregacji dla średniej klasyfikacji Comedies pogrupowane według roku
* ```avgrating```: Nazwa nowej kolumny, która jest tworzona w celu przechowywania zagregowanej wartości

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```
  
## <a name="next-steps"></a>Następne kroki

* Definiowanie agregacji opartej na oknach przy użyciu [transformacji okna](data-flow-window.md)
