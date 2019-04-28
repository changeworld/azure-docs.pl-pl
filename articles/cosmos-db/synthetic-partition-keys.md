---
title: Utwórz klucz partycji syntetycznych w usłudze Azure Cosmos DB równomierne rozłożenie danych i obciążenia.
description: Dowiedz się, jak używać kluczy partycji syntetycznych w kontenerach usługi Azure Cosmos
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937104"
---
# <a name="create-a-synthetic-partition-key"></a>Tworzenie syntetycznego klucza partycji

Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji przy użyciu wielu różnych wartości, takich jak setkach lub tysiącach. Celem jest, aby rozdystrybuować obciążenie i dane równomiernie elementy skojarzone z tymi wartościami klucza partycji. Jeśli taka właściwość nie istnieje w danych, możesz utworzyć *klucza partycji syntetycznych*. W tym dokumencie opisano kilka podstawowych technik do generowania klucza partycji syntetycznych dla kontenera usługi Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Łączenie wielu właściwości elementu

Tworząc klucza partycji przez złączenie wielu wartości właściwości w jednym sztuczne `partitionKey` właściwości. Te klucze są określane jako klucze syntetycznych. Na przykład rozważmy następujący przykład dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Poprzedni dokument jedną z opcji jest ustawiona /deviceId lub /date jako klucza partycji. Użyj tej opcji, jeśli chcesz podzielić kontenera na podstawie Identyfikatora urządzenia lub daty. Innym rozwiązaniem jest połączyć te dwie wartości w syntetycznych `partitionKey` właściwość, która jest używana jako klucz partycji.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

W scenariuszach w czasie rzeczywistym może mieć tysięcy elementów w bazie danych. Zamiast ręcznego dodawania syntetycznych klucza, definiować logikę po stronie klienta do łączenia wartości i wstawić syntetycznych klucza do elementów w kontenerach usługi Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Klucz partycji za pomocą losowych sufiks

Jest kolejną strategią możliwe, aby bardziej równomiernie rozłożyć obciążenie pracą dołaczenia losową liczbę na końcu wartości klucza partycji. Podczas dystrybucji elementów w ten sposób mogą wykonywać operacje równoległe zapisu w partycjach.

Przykładem jest, jeśli klucz partycji reprezentuje datę. Może być wybierz losową liczbę między 1 a 400 i łączenia go jako sufiks do wartości typu date. Ta metoda powoduje wartości klucza partycji, takich jak `2018-08-09.1`,`2018-08-09.2`i tak dalej za pośrednictwem `2018-08-09.400`. Ponieważ można wygenerować losowy klucz partycji, operacje zapisu w kontenerze w każdym dniu zostały rozmieszczone równomiernie w wielu partycjach. Ta metoda powoduje lepsze równoległości i większą ogólną przepływność.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Klucz partycji za pomocą wstępnie obliczona sufiksy 

Strategia losowe sufiksu może znacznie zwiększyć przepływność zapisu, ale jest trudny do odczytania określonego elementu. Nie można ustalić wartości sufiks, który został użyty podczas powstała z jednego elementu. Aby ułatwić odczytywanie poszczególnych elementów, użyj strategii wstępnie obliczona sufiksy. Zamiast używania losową liczbę, aby dystrybuować elementów między partycjami, należy użyć numer, który jest obliczany na podstawie coś, co chcesz zbadać.

Należy rozważyć poprzedni przykład gdzie kontenera używa daty jako klucza partycji. Teraz załóżmy, że każdy element ma `Vehicle-Identification-Number` (`VIN`) atrybutu, który chcemy uzyskać dostęp. Dodatkowo załóżmy, że często uruchamiać zapytania, aby znaleźć elementy za `VIN`, oprócz daty. Zanim aplikacja zapisuje element do kontenera, go obliczyć sufiks wyznaczania wartości skrótu, oparte na VIN, a następnie dołącza je do wartości typu date klucza partycji. Obliczenia może generować liczbą z zakresu od 1 do 400, który jest równomiernie rozłożona. Ten wynik jest podobne do efektów metodą losowego sufiks strategii. Wartość klucza partycji jest data połączona z obliczony wynik.

Ta strategia zapisy są równomiernie rozłożonych różnych wartości klucza partycji i w różnych partycjach. Można łatwo znaleźć określonego elementu i daty, ponieważ można obliczyć wartości klucza partycji dla określonego `Vehicle-Identification-Number`. Zaletą tej metody jest to, czy można uniknąć tworzenia pojedynczego gorąca klucza partycji, czyli klucza partycji, który pobiera wszystkie obciążenia. 

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej o partycjonowania pojęcie w następujących artykułach:

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
* Dowiedz się więcej na temat [aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych](set-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
