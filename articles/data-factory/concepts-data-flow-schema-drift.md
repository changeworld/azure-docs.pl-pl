---
title: Azure Data Factory mapowanie schematu przepływu danych
description: Twórz odporne przepływy danych w Azure Data Factory z dryfem schematu
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: b5777300f5033569caf3868218e747df3ff83a76
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640231"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapowanie dryfu schematu przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Koncepcji dryfowania schematu jest przypadek, w którym źródła często zmieniają metadane. Pola, kolumny, typy itd. można dodawać, usuwać lub zmieniać na bieżąco. Bez obsługi dryfowania schematu przepływ danych jest narażony na zmiany w nadrzędnych zmianach źródła danych. Gdy zmienia się przychodzące kolumny i pola, typowe wzorce ETL zakończą się niepowodzeniem, ponieważ mają one być powiązane z tymi nazwami źródłowymi.

Aby chronić przed dryfem ze schematami, ważne jest, aby w narzędziu przepływu danych były dostępne obiekty, dzięki którym inżynier danych może:

* Zdefiniuj źródła, które mają modyfikowalne nazwy pól, typy danych, wartości i rozmiary
* Zdefiniuj parametry przekształcenia, które mogą współpracowały ze wzorcami danych, a nie zakodowane pola i wartości
* Zdefiniuj wyrażenia, które opisują wzorce, aby dopasować pola przychodzące zamiast używać nazwanych pól

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Jak zaimplementować dryf schematu w odwzorze przepływów danych ADF
Moduł ADF natywnie obsługuje elastyczne schematy, które zmieniają się po wykonaniu do wykonania, aby można było utworzyć logikę transformacji danych ogólnych bez potrzeby ponownego kompilowania przepływów danych.

* Wybierz pozycję "Zezwalaj na dryfowanie schematu" w transformacji źródłowej

<img src="media/data-flow/schemadrift001.png" width="400">

* Po wybraniu tej opcji wszystkie pola przychodzące będą odczytywane ze źródła przy każdym wykonywaniu przepływu danych i zostaną przekazane przez cały przepływ do ujścia.

* Wszystkie nowo wykryte kolumny (kolumny przedzielone) będą domyślnie umieszczane jako dane typu String. W przypadku transformacji źródłowej wybierz opcję "Zapoznaj się z kolumnami z przekształceniem", jeśli chcesz, aby ADF automatycznie wywnioskować typy danych ze źródła.

* Upewnij się, że w celu zamapowania wszystkich nowych pól w transformację ujścia należy użyć opcji "Automap", aby wszystkie nowe pola zostały pobrane i wystawione w miejscu docelowym, a także ustawić "Zezwalaj na dryfowanie schematu".

<img src="media/data-flow/automap.png" width="400">

* Wszystko będzie działało, gdy w tym scenariuszu zostaną wprowadzone nowe pola z prostym mapowaniem ujścia > Source (Copy).

* Aby dodać przekształcenia w tym przepływie pracy, które obsługują dryfowanie schematu, możesz użyć dopasowania wzorca, aby dopasować kolumny według nazwy, typu i wartości.

* Kliknij pozycję "Dodaj wzorzec kolumny" w kolumnie pochodnej lub przekształcenie agregacji, jeśli chcesz utworzyć transformację, która zrozumie "dryf schematu".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Należy podjąć decyzję dotyczącą architektury w przepływie danych, aby akceptować dryf schematu w ramach przepływu. Gdy to zrobisz, możesz chronić przed zmianami schematu ze źródeł. Jednak w przepływie danych zostaną utracone wczesne powiązania kolumn i typów. Azure Data Factory traktuje przepływy napływu schematu jako przepływy późnego wiązania, dlatego podczas kompilowania przekształceń nazwy kolumn nie będą dostępne w widokach schematu w całym przepływie.

<img src="media/data-flow/taxidrift1.png" width="400">

W przykładowym przepływie danych pokazu dla taksówki istnieje przykładowe dryfowanie schematu w dolnym przepływie danych ze źródłem TripFare. W transformacji agregacji należy zauważyć, że do pól agregacji jest używany projekt "wzorzec kolumny". Zamiast nazw konkretnych kolumn lub wyszukiwania kolumn według pozycji Załóżmy, że dane mogą ulec zmianie i mogą nie być wyświetlane w tej samej kolejności między przebiegami.

W tym przykładzie obsługi odnoszącej się do Azure Data Factory schematu przepływu danych zostały skompilowane i agregacja, które skanują w poszukiwaniu kolumn typu "Double", wiedząc, że domena danych zawiera ceny dla każdej podróży. Następnie możemy wykonać zagregowane obliczenia matematyczne we wszystkich podwójnych polach w źródle, niezależnie od miejsca, w którym znajduje się kolumna, i bez względu na nazwę kolumny.

Składnia przepływu danych Azure Data Factory używa $ $ do reprezentowania każdej dopasowanej kolumny ze wzorca dopasowywania. Można również dopasować nazwy kolumn przy użyciu złożonych funkcji wyszukiwania ciągów i wyrażeń regularnych. W tym przypadku chcemy utworzyć nową agregowaną nazwę pola na podstawie każdego dopasowania typu "Double" kolumny i dołączyć tekst ```_total``` do każdej z tych pasujących nazw: 

```concat($$, '_total')```

Następnie zaokrąglimy i ustawimy wartości dla każdej z tych pasujących kolumn:

```round(sum ($$))```

Tę funkcję dryfowania schematu można zobaczyć w pracy z przykładem "Demonstracja Azure Data Factory przepływu danych". Przejdź do sesji debugowania przy użyciu przełącznika debugowania w górnej części powierzchni projektowej przepływu danych, aby zobaczyć wyniki interaktywnie:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Dostęp do nowych kolumn podrzędnych
W przypadku generowania nowych kolumn za pomocą wzorców kolumn można później uzyskać dostęp do tych nowych kolumn w transformacjach przepływu danych przy użyciu następujących metod:

* Aby identyfikować nowe kolumny według numeru pozycji, użyj "byPosition".
* Użyj "byName", aby identyfikować nowe kolumny według ich nazwy.
* W obszarze wzorce kolumn Użyj wartości "name", "Stream", "position" lub "Type" albo dowolnej kombinacji tych wartości, aby dopasować nowe kolumny.

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach
Mechanizm wybierania i przekształcania ujścia obsługują dopasowanie do wzorca za pośrednictwem mapowania opartego na regułach. Dzięki temu można tworzyć reguły, które mogą mapować kolumny z przeznaczeniem na aliasy kolumn i ujścia tych kolumn do miejsca docelowego.

## <a name="next-steps"></a>Kolejne kroki
W [języku wyrażenia przepływu danych](data-flow-expression-functions.md) można znaleźć dodatkowe funkcje dla wzorców kolumn i dryfowania schematu, w tym "byName" i "byPosition".
