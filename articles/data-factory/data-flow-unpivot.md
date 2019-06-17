---
title: Przepływ danych mapowania fabryki danych platformy Azure, Anuluj przestawienie transformacji
description: Przepływ danych mapowania fabryki danych platformy Azure, Anuluj przestawienie transformacji
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348318"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Usługi Azure Data Factory Anuluj przestawienie transformacji

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj Unpivot w usłudze ADF mapowanie przepływu danych jako sposób przekształcając zestawu unnormalized nieco bardziej znormalizowany przez wartości z wielu kolumn w jednym rekordzie rozszerzasz działalność o wielu rekordów przy użyciu tych samych wartości w jednej kolumnie.

![Anuluj przestawienie przekształcania](media/data-flow/unpivot1.png "Anuluj przestawienie opcji 1")

## <a name="ungroup-by"></a>Rozgrupuj przez

![Anuluj przestawienie przekształcania](media/data-flow/unpivot5.png "Anuluj przestawienie opcji 2")

Najpierw należy ustawić kolumny, które chcesz grupować według usługi agregacji obrotu. Ustaw co najmniej jedną kolumnę dla rozgrupowywanie z + znak obok listy kolumn.

## <a name="unpivot-key"></a>Anuluj przestawienie klucza

![Anuluj przestawienie przekształcania](media/data-flow/unpivot6.png "Anuluj przestawienie opcje 3")

Klucz Pivot jest kolumna, która ADF będzie przestawnego z wiersza do kolumny. Domyślnie każdej unikatowej wartości w zestawie danych dla tego pola będzie Przełącz się do kolumny. Opcjonalnie można jednak wprowadzić wartości z zestawu danych, do którego ma zostać Przełącz się do wartości w kolumnie.

## <a name="unpivoted-columns"></a>Anulowano przestawienie kolumn

![Anuluj przestawienie przekształcania](media/data-flow//unpivot7.png "Anuluj przestawienie opcje 4")

Na koniec wybierz agregacji, które mają być używane dla wartości przestawna i jak kolumny, które mają być wyświetlane w nowych projekcji danych wyjściowych z transformacji.

(Opcjonalnie) Możesz ustawić wzorca nazewnictwa z prefiksu, Środkowej i sufiksem ma zostać dodany do każdego Nazwa nowej kolumny z wartości wierszy.

Na przykład obrotowego "sprzedaż", "Region" będzie po prostu zapewniają wartości w nowej kolumnie od każdej wartości sprzedaży. Na przykład: "25", "50", "1000", ... Jednak jeśli ustawisz wartość prefiksu "Sprzedaż", następnie "Sprzedaż" będzie zaczynała się do wartości.

<img src="media/data-flow/unpivot3.png" width="400">

Ustawienie rozmieszczenia kolumn na wartość "Normal" opcji pozwala grupować ze sobą wszystkie kolumny przestawioną wartości zagregowanych. Ustawienie rozmieszczenia kolumn do "Poprzecznych" będzie zamiennie kolumny i wartości.

![Anuluj przestawienie przekształcania](media/data-flow//unpivot7.png "Anuluj przestawienie opcje 5")

Na zestaw wyników danych końcowych przestawienia przedstawiono Anulowano przestawienie w oddzielnym wierszu wartości sumy kolumn.

## <a name="next-steps"></a>Kolejne kroki

Użyj [przestawnego przekształcania](data-flow-pivot.md) pivot wiersze jako kolumny.
