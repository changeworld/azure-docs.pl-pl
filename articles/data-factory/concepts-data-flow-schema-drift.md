---
title: Mapowanie dryfu schemat przepływu danych w usłudze Azure Data Factory
description: Twórz odporne na błędy przepływu danych w usłudze Azure Data Factory przy użyciu schematu odejściem od tego stanu
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: aadab68185347dc0a12e0802f675efe13ecea545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61262163"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapowanie dryfu schemat przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Koncepcji dryfu schematu jest przypadek, w których źródłach często zmieniać metadanych. Pola, kolumn, typów, itp. można dodać, usunąć lub zmienione na bieżąco. Bez obsługi dla schematu odejściem od tego stanu, przepływ danych staje się podatne na zmiany w zmianach źródła danych nadrzędnego. Po zmianie przychodzących kolumn i pól, typowych wzorcach ETL zakończyć się niepowodzeniem, ponieważ mogą one być powiązane z tych nazw źródeł.

W celu ochrony przeciwko dryfu schematu, ważne jest zapewnienie urządzeń w narzędziu przepływ danych umożliwiający jako inżynier danych, aby:

* Definiowanie źródeł, które mają nazwy tych pól, typy danych, wartości i rozmiary
* Definiowanie parametrów transformacji, które może współpracować z wzorców danych zamiast zakodowanych pola i wartości
* Definiowanie wyrażenia, które zrozumieć wzorce do dopasowania pól przychodzące, zamiast korzystać z pola nazwane

## <a name="how-to-implement-schema-drift"></a>Jak zaimplementować kilka schematu

* Wybierz opcję "Zezwalaj na schemat odejściem od tego stanu" w swojej transformacji źródła

<img src="media/data-flow/schemadrift001.png" width="400">

* Gdy ta opcja jest zaznaczona, wszystkie pola przychodzące będą odczytywane ze źródła przy każdym wykonaniu przepływu danych i zostaną przekazane za pośrednictwem całego przepływu do ujścia.

* Upewnij się, że Użyj "Auto-Map", aby zamapować wszystkie nowe pola podczas przekształcania ujścia, tak aby wszystkie nowe pola Pobierz pobrane w górę i otwarta w lokalizacji docelowej:

<img src="media/data-flow/automap.png" width="400">

* Wszystko, co będzie działać, gdy zostaną wprowadzone nowe pola, w tym scenariuszu przy użyciu prostego źródła -> ujścia (zwane również skopiować) mapowania.

* Aby dodać przekształcenia w tym przepływie pracy, obsługujący dryfu schematu, można użyć dopasowywania do wzorca aby dopasować kolumny według nazwy, typu i wartości.

* Polecenie "Dodaj kolumnę wzorzec" w kolumnie pochodnej lub przekształcenie agregacji, jeśli chcesz utworzyć przekształcenie, który rozumie "Dryfu schematu".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Należy podjąć decyzję architektury przepływu danych do akceptowania dryfu schematu w całym przepływowi. Gdy to zrobisz, można zabezpieczać się przed zmiany schematu ze źródeł. Jednak użytkownik utraci wczesne powiązania kolumn i typy w całym przepływowi danych. Usługa Azure Data Factory traktuje przepływów dryfu schematu jako przepływów późne powiązania, więc w przypadku tworzenia przekształceń, nazw kolumn nie będą udostępniane użytkownikowi w widokach schematu w całym przepływie.

<img src="media/data-flow/taxidrift1.png" width="400">

Próbka Demo taksówek przepływu danych istnieje przykładu dryfu schematu w dolnej przepływ danych ze źródłem TripFare. Na przekształcenie agregacji Zwróć uwagę dla pól agregacji używamy projektu "kolumna wzorzec". Zamiast nazw określonych kolumn lub szukasz kolumny według położenia, przyjęto założenie, że dane można zmienić i nie może występować w tej samej kolejności między działa.

W tym przykładzie przepływ danych fabryki danych Azure schematu odejściem od tego stanu obsługi utworzyliśmy i agregacji skanuje dla kolumny typu "double", wiedząc, że domena danych zawiera ceny dla każdej podróży. Firma Microsoft następnie wykonywać obliczenia matematyczne agregacji we wszystkich polach double w źródle, niezależnie od tego, gdzie kolumna umieszczać swoje dokumenty i niezależnie od nazwy kolumny.

Składnia przepływ danych fabryki danych Azure używa $$, który reprezentuje każdej dopasowane kolumny od Twojego dopasowania wzorca. Można także dopasować nazw kolumn przy użyciu złożonych ciągu wyszukiwania i funkcje wyrażenia regularnego. W tym przypadku będziemy tworzyć nową nazwę pola zagregowane oparte na każdym dopasowaniu typu "double" kolumny i dołączyć tekst ```_total``` do każdego z tych dopasowane nazwy: 

```concat($$, '_total')```

Następnie firma Microsoft zostanie zaokrąglona i zsumować wartości dla każdego z tych dopasowane kolumny:

```round(sum ($$))```

Możesz przetestować ten w poziomie przy użyciu przykładowy przepływ danych fabryki danych platformy Azure "Taksówek pokaz". Przełącz na sesji debugowania przy użyciu przełącznika debugowania w górnej części powierzchni projektowej przepływ danych tak, aby interaktywnie zobaczyć wyniki:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Dostęp do podrzędnych nowych kolumn

Podczas generowania nowych kolumn z wzorcami kolumny, możesz uzyskać dostęp tych nowych kolumn w dalszej części przekształceń przepływu danych przy użyciu funkcji wyrażenia "byName".

## <a name="next-steps"></a>Kolejne kroki

W [język wyrażeń przepływu danych](data-flow-expression-functions.md) znajdują się dodatkowe urządzenia do wzorców kolumny i dryfu schematu, w tym "byName" i "byPosition".
