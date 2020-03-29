---
title: Tworzenie syntetycznego klucza partycji w usłudze Azure Cosmos DB
description: Dowiedz się, jak używać kluczy partycji syntetycznych w kontenerach usługi Azure Cosmos do równomiernego rozmieszczania danych i obciążenia między kluczami partycji
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441217"
---
# <a name="create-a-synthetic-partition-key"></a>Tworzenie syntetycznego klucza partycji

Najlepszym rozwiązaniem jest klucz partycji z wieloma różnymi wartościami, takimi jak setki lub tysiące. Celem jest równomierne rozłożenie danych i obciążenia między elementy skojarzone z tymi wartościami klucza partycji. Jeśli taka właściwość nie istnieje w danych, można utworzyć *syntetyczny klucz partycji*. W tym dokumencie opisano kilka podstawowych technik generowania klucza partycji syntetycznych dla kontenera usługi Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Łączenie wielu właściwości elementu

Klucz partycji można utworzyć, konkadując wiele `partitionKey` wartości właściwości w jedną sztuczną właściwość. Klucze te są określane jako klucze syntetyczne. Rozważmy na przykład następujący przykładowy dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

W poprzednim dokumencie jedną z opcji jest ustawienie /deviceId lub /date jako klucza partycji. Użyj tej opcji, jeśli chcesz podzielić kontener na podstawie identyfikatora urządzenia lub daty. Inną opcją jest łączenie tych dwóch wartości `partitionKey` do syntetycznej właściwości, która jest używana jako klucz partycji.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

W scenariuszach w czasie rzeczywistym można mieć tysiące elementów w bazie danych. Zamiast ręcznie dodawać klucz syntetyczny, zdefiniuj logikę po stronie klienta, aby łączyć wartości i wstawić klucz syntetyczny do elementów w kontenerach usługi Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Używanie klucza partycji z losowym sufiksem

Inną możliwą strategią bardziej równomiernego rozmieszczenia obciążenia jest dołączenie losowej liczby na końcu wartości klucza partycji. Podczas dystrybucji elementów w ten sposób, można wykonywać operacje zapisu równoległego między partycjami.

Przykładem jest, jeśli klucz partycji reprezentuje datę. Możesz wybrać liczbę losową z 1 do 400 i połącz ją jako sufiks do daty. Ta metoda powoduje wartości `2018-08-09.1`klucza partycji, takie jak ,`2018-08-09.2`i tak dalej, przez `2018-08-09.400`. Ponieważ losujesz klucz partycji, operacje zapisu w kontenerze każdego dnia są rozłożone równomiernie na wiele partycji. Ta metoda powoduje lepsze równoległości i ogólnej wyższej przepływności.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Używanie klucza partycji ze wstępnie obliczonymi sufiksami 

Strategia sufiksu losowego może znacznie poprawić przepływność zapisu, ale trudno jest odczytać określony element. Nie znasz wartości sufiksu, która została użyta podczas na pisalicjucz. Aby ułatwić odczytywanie poszczególnych elementów, należy użyć wstępnie obliczonej strategii sufiksów. Zamiast używać liczby losowej do dystrybucji elementów między partycjami, należy użyć liczby, która jest obliczana na podstawie czegoś, co chcesz zbadać.

Rozważmy poprzedni przykład, gdzie kontener używa daty jako klucz partycji. Teraz załóżmy, `Vehicle-Identification-Number` że`VIN`każdy element ma ( ) atrybut, który chcemy uzyskać dostęp. Ponadto załóżmy, że często uruchamiane `VIN`kwerendy, aby znaleźć elementy przez , oprócz daty. Zanim aplikacja zapisuje element do kontenera, może obliczyć sufiks mieszania na podstawie numeru VIN i dołączyć go do daty klucza partycji. Obliczenia mogą generować liczbę od 1 do 400, która jest równomiernie rozłożona. Ten wynik jest podobny do wyników opracowanych przez metodę strategii przyrostka losowego. Wartość klucza partycji jest następnie datą połączoną z obliczonym wynikiem.

Dzięki tej strategii zapisy są równomiernie rozłożone na wartości klucza partycji i na partycje. Można łatwo odczytać określony element i datę, ponieważ można obliczyć wartość klucza partycji dla określonego `Vehicle-Identification-Number`. Zaletą tej metody jest to, że można uniknąć tworzenia jednego klucza partycji skrótowej, czyli klucz partycji, który zajmuje całe obciążenie. 

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat koncepcji partycjonowania można dowiedzieć się w następujących artykułach:

* Dowiedz się więcej o [partycjach logicznych](partition-data.md).
* Dowiedz się więcej o [sposobie aprowizowania przepływności w kontenerach i bazach danych usługi Azure Cosmos.](set-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)
