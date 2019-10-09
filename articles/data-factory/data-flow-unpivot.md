---
title: Przekształcenie Unpivot przepływu danych mapowania Azure Data Factory
description: Przekształcenie Unpivot przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 28fedf96ec178aae0615129421bdae7721a66105
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029929"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory przekształcenie Unpivot



Użyj wyrażenia UNPIVOT w przepływie danych mapowania ADF jako metody przekształcenia nieznormalizowanego zestawu danych w bardziej znormalizowaną wersję, rozszerzając wartości z wielu kolumn w pojedynczym rekordzie na wiele rekordów z tymi samymi wartościami w jednej kolumnie.

![Operator UNPIVOT]—(media/data-flow/unpivot1.png "Opcje dla opcji 1")

## <a name="ungroup-by"></a>Rozgrupuj według

![Unpivot transformacja](media/data-flow/unpivot5.png "UNPIVOT opcje 2")

Najpierw ustaw kolumny, według których chcesz grupować dla agregacji tabeli przestawnej. Ustaw co najmniej jedną kolumnę do rozgrupowania przy użyciu znaku + obok listy kolumn.

## <a name="unpivot-key"></a>Klucz UNPIVOT

![Unpivot transformacja](media/data-flow/unpivot6.png "UNPIVOT opcje 3")

Klucz przestawny jest kolumną przestawianą przez ADF z wierszy do kolumny. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola będzie przestawiana do kolumny. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, który ma być przestawny na wartości kolumn.

## <a name="unpivoted-columns"></a>Kolumny przestawiane

![Unpivot transformacja](media/data-flow//unpivot7.png "UNPIVOT opcje 4")

Na koniec wybierz agregację, która ma być używana dla wartości przestawnych, i sposób, w jaki kolumny mają być wyświetlane w nowej projekcji wyjściowej z transformacji.

Obowiązkowe Można ustawić wzorzec nazewnictwa z prefiksem, środkową i sufiksem, który ma zostać dodany do każdej nowej kolumny Nazwa z wartości wierszy.

Na przykład przestawianie "Sales" według "region" po prostu daje nowe wartości kolumny na podstawie każdej wartości sprzedaży. Na przykład: "25", "50", "1000",... Jeśli jednak ustawisz wartość prefiksu "Sales", "Sales" zostanie poprzedzona wartościami.

<img src="media/data-flow/unpivot3.png" width="400">

Ustawienie kolumny "normalny" spowoduje grupowanie wszystkich kolumn przestawnych z wartościami zagregowanymi. Ustawienie kolumn rozmieszczenia na "boczne" będzie odróżniać się od kolumn i wartości.

![Operator UNPIVOT]—(media/data-flow//unpivot7.png "Opcje dla opcji 5")

W końcowym zestawie wyników nieprzestawionych danych są wyświetlane sumy kolumn, które są teraz oddzielone do oddzielnych wartości wierszy.

## <a name="next-steps"></a>Następne kroki

Użyj [transformacji przestawnej](data-flow-pivot.md) , aby przestawić wiersze do kolumn.
