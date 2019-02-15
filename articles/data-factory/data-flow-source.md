---
title: Mapowanie przekształceń źródła przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształceń źródła przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35f4e794caf84aba860b98e68eadcdcd88e77952
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272119"
---
# <a name="azure-data-factory-mapping-data-flow-source-transformation"></a>Mapowanie przekształceń źródła przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie źródła umożliwia skonfigurowanie źródła danych, które mają być używane do przenoszenia danych do przepływu danych. Może mieć więcej niż 1 źródło przekształcenia w jednym przepływ danych. Zawsze zaczynają się projektowania przepływu danych ze źródłem.

> [!NOTE]
> Każdy przepływ danych wymaga co najmniej jeden Przekształcenie źródła. Dodaj dowolną liczbę dodatkowych źródeł, ile potrzebujesz

![Opcje przekształcania źródła](media/data-flow/source.png "źródła")

Przepływ danych źródła musi być skojarzony z dokładnie jeden zestaw danych usługi ADF, który definiuje kształt i lokalizację danych do zapisu lub odczytu.

## <a name="data-flow-staging-areas"></a>Przepływ danych — obszarów tymczasowych

Przepływ danych ADF ma linii wzroku do 5 głównych obszarów "staging" w obrębie platformy Azure do wykonywania przekształceń danych: Obiektów Blob platformy Azure, Azure Data Lake Store generacji 1, Azure Data Lake Store Gen 2, bazy danych Azure SQL i magazyn danych Azure SQL. ADF ma dostęp do niemal 80 różnych natywne łączniki, tak aby uwzględnić te źródła danych do przepływu danych, etap pierwszy te dane do jednej z tych pięciu głównych przepływ danych przemieszczania obszarów najpierw za pomocą działania kopiowania:

## <a name="options"></a>Opcje

### <a name="allow-schema-drift"></a>Zezwalaj na kilka schematu
Wybierz Zezwalaj dryfu schematu, jeśli kolumny źródłowe zmieni się często. To ustawienie umożliwia wszystkie przychodzące pola ze źródła do przepływu za pośrednictwem przekształceń do ujścia.

### <a name="fail-if-columns-in-the-dataset-are-not-found"></a>Się niepowodzeniem, jeśli nie ma kolumn w zestawie danych
Wybierz tę opcję, aby wymusić Walidacja schematu źródłowego, który zakończy się niepowodzeniem przepływu danych w przypadku kolumn, które oczekują w źródle nie są obecne.

### <a name="sampling"></a>Próbkowanie
Użyj próbkowania, aby ograniczyć liczbę wierszy ze źródła.  Jest to przydatne, gdy będziesz potrzebować tylko próbkę danych źródłowych do testowania i debugowania.

### <a name="define-schema"></a>Definiowanie schematu

![Źródło przekształcenia](media/data-flow/source2.png "źródła 2")

### <a name="you-can-modify-the-name-of-the-source-columns-and-their-associated-data-types"></a>Możesz zmodyfikować nazwę kolumny źródłowe i ich typy skojarzonych danych

Dla typów plików źródłowych, które nie są silnie typizowane (czyli pliki proste w przeciwieństwie do plików Parquet) należy zdefiniować typy danych dla każdego pola, w tym miejscu w Przekształcenie źródła w przeciwieństwie do w zestawie danych.

Jeśli nie widzisz nazw kolumn i typy w przepływu danych, prawdopodobnie nie zdefiniowano je w sekcji zdefiniować schemat ujścia. Tylko należy to zrobić, jeśli nie używasz przepływ danych Obsługa schematu odejściem od tego stanu.

W tym miejscu w "Definiowania schematu" kartę na przekształcenie źródła jest, którym można ustawić formaty i typy danych:

![Źródło przekształcenia](media/data-flow/source003.png "typy danych")

### <a name="optimize"></a>Optymalizacja

![Źródła partycji](media/data-flow/sourcepart.png "partycjonowania")

Na karcie Optymalizuj dla transformacji źródła zobaczysz dodatkowy typ partycjonowania o nazwie "Źródło". To spowoduje tylko światła w górę po wybraniu bazy danych SQL Azure jako źródło. Jest to spowodowane ADF będzie chcesz zrównoleglić połączenia do wykonywania dużych zapytań dotyczących źródła danych bazy danych SQL Azure.

Partycjonowanie danych w źródle danych SQL jest opcjonalne, ale jest przydatne w przypadku dużych kwerend. Dostępne są dwie opcje:

### <a name="column"></a>Kolumna

Wybierz kolumnę do partycji na z tabeli źródłowej. Należy również ustawić maksymalną liczbę połączeń.

### <a name="query-condition"></a>Warunek kwerendy

Opcjonalnie można podzielić połączenia na podstawie zapytania. Dla tej opcji po prostu umieść zawartość predykat WHERE. Czyli rok > 1980
