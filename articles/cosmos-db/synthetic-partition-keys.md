---
title: Utwórz klucz partycji syntetycznych w usłudze Azure Cosmos DB równomierne rozłożenie danych i obciążenia.
description: Dowiedz się, jak używać kluczy partycji syntetycznych w kontenerach usługi Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997124"
---
# <a name="create-a-synthetic-partition-key"></a>Tworzenie syntetycznego klucza partycji

Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji przy użyciu wielu różnych wartości, takich jak setkach lub tysiącach. Celem jest, aby rozdystrybuować obciążenie i dane równomiernie elementy skojarzone z tymi wartościami klucza partycji. Jeśli taka właściwość nie istnieje w danych, można utworzyć klucza partycji syntetycznych. W poniższych sekcjach opisano kilka podstawowych technik do generowania klucza partycji syntetycznych kontenera.

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

W scenariuszach w czasie rzeczywistym może mieć tysiące dokumenty w bazie danych. Zamiast ręcznego dodawania syntetycznych klucza, definiować logikę po stronie klienta do łączenia wartości i wstawić syntetycznych klucza do dokumentów.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Klucz partycji za pomocą losowych sufiks

Jest kolejną strategią możliwe, aby bardziej równomiernie rozłożyć obciążenie pracą dołaczenia losową liczbę na końcu wartości klucza partycji. Podczas dystrybucji elementów w ten sposób mogą wykonywać operacje równoległe zapisu w partycjach.

Przykładem jest, jeśli klucz partycji reprezentuje datę. Może być wybierz losową liczbę między 1 a 400 i łączenia go jako sufiks do wartości typu date. Ta metoda powoduje wartości klucza partycji, takich jak 2018-08-09.1 2018-08-09.2 i tak dalej, za pośrednictwem 2018-08-09.400. Ponieważ można wygenerować losowy klucz partycji, operacje zapisu w kontenerze w każdym dniu zostały rozmieszczone równomiernie w wielu partycjach. Ta metoda powoduje lepsze równoległości i większą ogólną przepływność.

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>Klucz partycji za pomocą wstępnie obliczone sufiksy 

Strategia randomizing może znacznie zwiększyć przepływność zapisu, ale trudno odczytać określonego elementu. Nie można ustalić wartość sufiksu, która jest używana podczas zapisu elementu. Aby ułatwić odczytywanie poszczególnych elementów, użyj strategii sufiksy wstępnie obliczone. Zamiast używania losową liczbę, aby dystrybuować elementów między partycjami, należy użyć numeru, który oblicza oparte na coś, co chcesz zbadać.

Należy rozważyć poprzedni przykład gdzie kontenera używa datę w kluczu partycji. Teraz załóżmy, że każdy element ma atrybut dostępny numer identyfikacyjny pojazdu (VIN). Dodatkowo załóżmy, że często uruchamiać zapytań, aby znaleźć elementy za VIN, oprócz daty. Zanim aplikacja zapisuje element do kontenera, go obliczyć sufiks wyznaczania wartości skrótu, oparte na VIN, a następnie dołącza je do wartości typu date klucza partycji. Obliczenia może generować liczbą z zakresu od 1 do 400, który jest równomiernie rozłożona. Ten wynik jest podobne do efektów metodą losowego strategii. Wartość klucza partycji jest data połączona z obliczony wynik.

Ta strategia zapisy są równomiernie rozłożonych różnych wartości klucza partycji i w różnych partycjach. Ponieważ można obliczyć wartości klucza partycji dla określonego-— numer identyfikacyjny można łatwo odczytać określonego elementu i daty. Zaletą tej metody jest, czy można uniknąć tworzenia klucza jednej partycji w warstwie gorąca. Klucz partycji gorąca jest klucz partycji, który pobiera wszystkie obciążenia. 

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej o partycjonowania pojęcie w następujących artykułach:

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
* Dowiedz się więcej na temat [aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych](set-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
