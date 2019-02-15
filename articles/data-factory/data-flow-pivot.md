---
title: Mapowanie przekształceń obrotu przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształceń obrotu przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 86404f4eb2eb2de4243c6bb725f4292e7b560d18
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272092"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Mapowanie przekształceń obrotu przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użycia obrotu w przepływ danych ADF agregacji gdzie ma jego wartości odrębnych wiersz przekształcone w poszczególnych kolumnach w grupowaniu kolumn. Zasadniczo można przestawiać wartości wierszy w nowych kolumn (przekształca dane w metadanych).

![Przestawianie opcje](media/data-flow/pivot1.png "przestawnego 1")

## <a name="group-by"></a>Grupuj według

![Przestawianie opcje](media/data-flow/pivot2.png "przestawnego 2")

Najpierw należy ustawić kolumny, które chcesz grupować według usługi agregacji obrotu. Możesz ustawić więcej niż 1 kolumny, w tym miejscu przy użyciu + znak obok listy kolumn.

## <a name="pivot-key"></a>Klucz obrotu

![Przestawianie opcje](media/data-flow/pivot3.png "przestawnego 3")

Klucz Pivot jest kolumna, która ADF będzie przestawnego z wiersza do kolumny. Domyślnie każdej unikatowej wartości w zestawie danych dla tego pola będzie Przełącz się do kolumny. Opcjonalnie można jednak wprowadzić wartości z zestawu danych, do którego ma zostać Przełącz się do wartości w kolumnie.

## <a name="pivoted-columns"></a>Przestawna kolumn

![Przestawianie opcje](media/data-flow/pivot4.png "przestawnego 4")

Na koniec należy wybrać agregacji, które mają być używane dla wartości przestawna i jak kolumny, które mają być wyświetlane w nowych projekcji danych wyjściowych z transformacji.

(Opcjonalnie) Możesz ustawić wzorca nazewnictwa z prefiksu, Środkowej i sufiksem ma zostać dodany do każdego Nazwa nowej kolumny z wartości wierszy.

Na przykład przestawianie "Sprzedaż", "Region" w wyniku wartości w nowej kolumnie od każdej wartości sprzedaży, czyli "25", "50", "1000", etc. Jednak jeśli ustawisz wartość prefiksu "Sprzedaż" 

![Przestawianie opcje](media/data-flow/pivot5.png "przestawnego 5")

Ustawienie rozmieszczenia kolumn na wartość "Normal" opcji pozwala grupować ze sobą wszystkie kolumny przestawioną wartości zagregowanych. Ustawienie rozmieszczenia kolumn do "Poprzecznych" będzie zamiennie kolumny i wartości.

### <a name="aggregation"></a>Agregacja

Aby ustawić agregacji, które mają być używane dla wartości obrotu, kliknij pole w dolnej części okienka przestawiać kolumn. Zawrzesz Konstruktor wyrażeń przepływ danych ADF, której można utworzyć wyrażenie agregacji i podaj nazwę opisową alias nowej wartości zagregowanych.

Użyj danych ADF przepływu język wyrażeń do opisywania przekształceń kolumna przestawna w Konstruktorze wyrażeń: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Jak ponownie dołączyć oryginalne pola
> [!NOTE]
> Przekształcenie Pivot będzie projektu tylko kolumn używanych w agregacji, grupowanie i akcja obrotu. Jeśli chcesz uwzględnić inne kolumny z poprzedniego kroku w przepływie, użyj nowej gałęzi z poprzedniego kroku i użyć wzorca samosprzężenie do połączenia przepływu z odpowiednich oryginalnych metadanych
