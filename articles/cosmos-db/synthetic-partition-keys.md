---
title: Utwórz klucz partycji syntetycznych w usłudze Azure Cosmos DB równomierne rozłożenie danych i obciążenia.
description: Dowiedz się, jak używać kluczy partycji syntetycznych w kontenerach usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 987bfe023e7355a2780af57dc5e5cac3129a5ca1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454152"
---
# <a name="create-a-synthetic-partition-key"></a>Utwórz klucz partycji syntetycznego

Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji przy użyciu wielu różnych wartości, takich jak setkach lub tysiącach. Celem jest, aby rozdystrybuować obciążenie i dane równomiernie elementy skojarzone z tymi wartościami klucza partycji. Jeśli taka właściwość nie istnieje w danych, można konstruować klucza partycji syntetycznych. W poniższych sekcjach opisano kilka podstawowych technik do generowania klucza partycji syntetycznych kontenera.

## <a name="concatenating-multiple-properties-of-an-item"></a>Łączenie wielu właściwości elementu

Tworząc klucza partycji przez złączenie wielu wartości właściwości w jednym sztuczne `partitionKey` właściwości. Te klucze są określane jako klucze syntetycznych. Na przykład rozważmy następujący przykład dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Poprzedni dokument jedną z opcji jest ustawiony /deviceId lub /date jako klucza partycji, jeśli chcesz podzielić kontenera na podstawie Identyfikatora urządzenia lub daty. Innym rozwiązaniem jest połączyć te dwie wartości w syntetycznych `partitionKey` właściwość, która jest używana jako klucz partycji.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

W scenariuszach w czasie rzeczywistym może mieć tysiące dokumenty w bazie danych, więc zamiast ręcznego dodawania syntetycznych klucza, należy zdefiniować logikę po stronie klienta do łączenia wartości i wstawić syntetycznych klucza do dokumentów.

## <a name="using-a-partition-key-with-random-suffix"></a>Użycie klucza partycji z sufiksem losowe

Jest kolejną strategią możliwe, aby bardziej równomiernie rozłożyć obciążenie pracą dołaczenia losową liczbę na końcu wartości klucza partycji. Operacje zapisu w rozproszone elementów w sposób umożliwia wykonywanych równolegle w partycjach.

Na przykład jeśli klucz partycji reprezentuje datę, może być wybierz losową liczbę między 1 a 400 i łączenia go jako sufiks do wartości typu date. Ta metoda powoduje wartości klucza partycji, takich jak 2018-08-09.1 2018-08-09.2 i tak dalej, za pośrednictwem 2018-08-09.400. Ponieważ są losowego generowania klucza partycji, operacje zapisu w kontenerze w każdym dniu zostały rozmieszczone równomiernie w wielu partycjach. Ta metoda powoduje lepsze równoległości i większą ogólną przepływność.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Użycie klucza partycji przy użyciu wstępnie obliczona sufiksy 

Ale losowego generowania strategii może znacznie zwiększyć przepływność zapisu, jest trudny do odczytu określonego elementu, ponieważ nie wiesz, wartość sufiksu, która jest używana podczas zapisu elementu. Aby ułatwić odczytywanie poszczególnych elementów, można użyć strategii wstępnie obliczona sufiksy. Zamiast używania losową liczbę, aby dystrybuować elementów między partycjami, należy użyć numeru, który obliczania oparte na coś, co chcesz zbadać.

Należy rozważyć poprzedni przykład gdzie kontenera używa datę w kluczu partycji. Teraz załóżmy, że każdy element ma atrybut VIN — Numer identyfikacyjny pojazdu (-) dostępny i jeśli często uruchomić zapytania, aby wyszukiwanie elementów według VIN, dodatkowo do daty. Zanim aplikacja zapisuje element do kontenera, go obliczyć sufiks wyznaczania wartości skrótu, oparte na VIN, a następnie dołącza je do wartości typu date klucza partycji. Obliczenia może generować liczbą z zakresu od 1 do 400 równomierną dystrybucję, podobne do efektów metodą losowego strategii. Wartość klucza partycji będzie data połączona z obliczony wynik.

Ta strategia zapisy są równomiernie rozłożonych różnych wartości klucza partycji i w różnych partycjach. Ponieważ można obliczyć wartości klucza partycji dla określonego-— numer identyfikacyjny można łatwo odczytać określonego elementu i daty. Zaletą tej metody jest to, czy można uniknąć tworzenia pojedynczego gorąca klucza partycji (klucza partycji, który pobiera wszystkie obciążenia). 

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej o partycjonowania pojęcie w następujących artykułach:

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md)
* Dowiedz się więcej o [aprowizowania przepływności na kontenerach Cosmos i baz danych](set-throughput.md)
* Dowiedz się, [jak aprowizować przepływność na kontenerze Cosmos](how-to-provision-container-throughput.md)
* Dowiedz się, [jak aprowizować przepływność mierzoną w bazie danych Cosmos](how-to-provision-database-throughput.md)
