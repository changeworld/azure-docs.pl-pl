---
title: Przekształcenie przestawne przepływu danych mapowania Azure Data Factory
description: Przestawianie danych z wierszy do kolumn przy użyciu funkcji mapowania Azure Data Factory przekształcenie przestawnego przepływu danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 1412f7d822d83a8712d27dd4e86311567d6ac714
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029298"
---
# <a name="azure-data-factory-pivot-transformation"></a>Przekształcanie tabeli przestawnej usługi Azure Data Factory


Użyj tabeli przestawnej w przepływie danych ADF jako agregacji, w której co najmniej jedna kolumna grupowania ma swoje unikatowe wartości wierszy przekształcone w pojedyncze kolumny. Zasadniczo można przestawiać wartości wierszy w nowe kolumny (przekształcić dane w metadane).

![Opcje Pivot]Pivot(media/data-flow/pivot1.png "1")

## <a name="group-by"></a>Grupuj według

![Opcje tabeli]przestawnej(media/data-flow/pivot2.png "2")

Najpierw ustaw kolumny, według których chcesz grupować dla agregacji tabeli przestawnej. W tym miejscu możesz ustawić więcej niż 1 kolumnę przy użyciu znaku + obok listy kolumn.

## <a name="pivot-key"></a>Klucz przestawny

![Opcje tabeli]przestawnej(media/data-flow/pivot3.png "3")

Klucz przestawny jest kolumną przestawianą przez ADF z wierszy do kolumny. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola będzie przestawiana do kolumny. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, który ma być przestawny na wartości kolumn. Jest to kolumna, która określi nowe kolumny, które zostaną utworzone.

## <a name="pivoted-columns"></a>Kolumny przestawne

![Opcje tabeli]przestawnej(media/data-flow/pivot4.png "4")

Na koniec wybierzesz agregację, która ma być używana dla wartości przestawnych, i jak chcesz, aby kolumny były wyświetlane w nowej projekcji wyjściowej na podstawie transformacji.

Obowiązkowe Można ustawić wzorzec nazewnictwa z prefiksem, środkową i sufiksem, który ma zostać dodany do każdej nowej kolumny Nazwa z wartości wierszy.

Na przykład przestawianie "Sales" według "region" spowoduje, że nowe wartości kolumn z każdej wartości sprzedaży, tj. "25", "50", "1000" itd. Jeśli jednak ustawisz wartość prefiksu "Sales-", każda wartość kolumny spowoduje dodanie "Sales-" na początku wartości.

![Opcja Pivot]Pivot(media/data-flow/pivot5.png "5")

Ustawienie kolumny "normalny" spowoduje grupowanie wszystkich kolumn przestawnych z wartościami zagregowanymi. Ustawienie kolumn rozmieszczenia na "boczne" będzie odróżniać się od kolumn i wartości.

### <a name="aggregation"></a>Agregacja

Aby ustawić agregację, która ma być używana dla wartości tabeli przestawnej, kliknij pole u dołu okienka kolumny przestawne. Wprowadzisz do konstruktora wyrażeń przepływu danych ADF, w którym można utworzyć wyrażenie agregacji i podać opisową nazwę aliasu dla nowych wartości agregowanych.

Użyj języka wyrażeń przepływu danych ADF, aby opisać przestawiane przekształcenia kolumn w Konstruktorze wyrażeń: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadane tabeli przestawnej

Transformacja przestawna spowoduje utworzenie nowych nazw kolumn, które są dynamiczne na podstawie danych przychodzących. Klucz przestawny tworzy wartości dla każdej nowej nazwy kolumny. Jeśli nie określisz poszczególnych wartości i chcesz utworzyć dynamiczne nazwy kolumn dla każdej unikatowej wartości w kluczu przestawnym, interfejs użytkownika nie wyświetli metadanych w trakcie inspekcji i nie będzie propagowanie kolumn do transformacji ujścia. W przypadku ustawienia wartości dla klucza przestawnego, usługa ADF może ustalić nowe nazwy kolumn, a nazwy kolumn będą dostępne dla użytkownika w mapowaniu inspekcji i ujścia.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generuj nowy model na podstawie kolumn dynamicznych

Pivot automatycznie generuje nowe nazwy kolumn na podstawie wartości wierszy. Można przekształcić te nowe kolumny w metadane, do których można przywoływać się później w przepływie danych. Aby to zrobić, kliknij kartę Podgląd danych. Wszystkie nowe kolumny wygenerowane przez transformację przestawną są wyświetlane z ikoną "dryfed" w nagłówku tabeli. Kliknij przycisk "Mapuj przelany", aby przekształcić te nowe kolumny w metadane, co sprawia, że są one częścią modelu przepływu danych.

![Kolumny tabeli przestawnej](media/data-flow/newpivot1.png "mapują kolumny tabeli") przestawnej

### <a name="landing-new-columns-in-sink"></a>Wypełnianie nowych kolumn w usłudze sink

Nawet przy użyciu dynamicznych nazw kolumn w tabeli przestawnej można nadal odłożyć nowe nazwy kolumn i wartości do magazynu docelowego. Po prostu ustaw opcję "Zezwalaj na dryf schematu" na wartość włączone w ustawieniach ujścia. Nowe nazwy dynamiczne nie będą widoczne w metadanych kolumny, ale opcja dryfowania schematu umożliwi wygruntowanie danych.

### <a name="view-metadata-in-design-mode"></a>Wyświetl metadane w trybie projektowania

Jeśli chcesz wyświetlić nowe nazwy kolumn jako metadane w inspekcji i chcesz zobaczyć kolumny propagowane jawnie do transformacji ujścia, a następnie ustaw jawne wartości na karcie klucz przestawny.

### <a name="how-to-rejoin-original-fields"></a>Jak ponownie dołączyć oryginalne pola
Transformacja przestawna będzie dotyczyć tylko kolumn używanych w akcji agregacji, grupowania i przestawnej. Jeśli chcesz uwzględnić inne kolumny z poprzedniego kroku w przepływie, użyj nowej gałęzi z poprzedniego kroku i użyj wzorca samosprzężenia, aby połączyć przepływ z oryginalnymi metadanymi.

## <a name="next-steps"></a>Następne kroki

Wypróbuj [transformację UNPIVOT](data-flow-unpivot.md) , aby przekształcić wartości kolumn w wartości wierszy. 
