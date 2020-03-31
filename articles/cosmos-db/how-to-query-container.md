---
title: Wykonywanie zapytań dla kontenerów w usłudze Azure Cosmos DB
description: Dowiedz się, jak wysyłać zapytania do kontenerów w usłudze Azure Cosmos DB przy użyciu zapytań między partycjami i między partycjami
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131393"
---
# <a name="query-an-azure-cosmos-container"></a>Wykonywanie zapytania dotyczącego kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak wykonywać zapytania dla kontenera (kolekcji, grafu lub tabeli) w usłudze Azure Cosmos DB. W szczególności opisano, jak w partycji i między partycjami kwerendy działają w usłudze Azure Cosmos DB.

## <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

Podczas kwerendy danych z kontenerów, jeśli kwerenda ma filtr klucza partycji określony, usługa Azure Cosmos DB automatycznie optymalizuje kwerendę. Kieruje kwerendę do [partycji fizycznych odpowiadających](partition-data.md#physical-partitions) wartościom klucza partycji określonym w filtrze.

Rozważmy na przykład poniższą kwerendę `DeviceId`z filtrem równości na . Jeśli uruchomimy tę kwerendę w `DeviceId`kontenerze podzielonym na partycje , ta kwerenda będzie filtrować do jednej partycji fizycznej.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Podobnie jak w przypadku wcześniejszego przykładu, ta kwerenda będzie również filtrować do jednej partycji. Dodanie dodatkowego `Location` filtru nie powoduje zmiany:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Oto kwerenda, która ma filtr zakresu na klucz partycji i nie będzie objęty zakresem do jednej partycji fizycznej. Aby kwerenda w partycji, kwerenda musi mieć filtr równości, który zawiera klucz partycji:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Zapytanie obejmujące wiele partycji

Następująca kwerenda nie ma filtru na`DeviceId`kluczu partycji ( ). W związku z tym musi fan-out do wszystkich partycji fizycznych, gdzie jest uruchamiany względem indeksu każdej partycji:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Każda partycja fizyczna ma swój własny indeks. W związku z tym po uruchomieniu kwerendy między partycjami w kontenerze, są skutecznie uruchomione jedno zapytanie *na* partycję fizyczną. Usługa Azure Cosmos DB automatycznie zagreguje wyniki w różnych partycjach fizycznych.

Indeksy w różnych partycjach fizycznych są niezależne od siebie. Nie ma indeksu globalnego w usłudze Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Zapytanie równoległe obejmujące wiele partycji

Zestawy Azure Cosmos DB SDK w wersji 1.9.0 i nowszych obsługują opcje równoległego wykonywania zapytań. Zapytania równoległe obejmujące wiele partycji umożliwiają wykonywanie zapytań o wiele partycji z małym opóźnieniem.

Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

- **MaxConcurrency**: Ustawia maksymalną liczbę jednoczesnych połączeń sieciowych z partycjami kontenera. Jeśli ta właściwość `-1`zostanie ustawiona na , zestaw SDK zarządza stopniem równoległości. Jeśli `MaxConcurrency` ustawiono `0`na , istnieje jedno połączenie sieciowe z partycjami kontenera.

- **MaxBufferedItemCount**: wyznacza kompromis między wykorzystaniem pamięci po stronie klienta i opóźnieniem zapytań. Jeśli ta opcja zostanie pominięta lub ustawiona na wartość -1, zestaw SDK będzie zarządzać liczbą elementów buforowanych podczas równoległego wykonywania zapytań.

Ze względu na możliwość równoległości zapytań między partycjami usługi Azure Cosmos DB, opóźnienie kwerendy będzie zazwyczaj skalowane, a system dodaje [partycje fizyczne.](partition-data.md#physical-partitions) Jednak opłata RU znacznie wzrośnie wraz ze wzrostem całkowitej liczby partycji fizycznych.

Po uruchomieniu kwerendy między partycjami, zasadniczo wykonujesz oddzielne zapytanie na pojedynczą partycję fizyczną. Podczas kwerendy kwerendy między partycjami użyje indeksu, jeśli są dostępne, nadal nie są one tak wydajne, jak kwerendy w partycji.

## <a name="useful-example"></a>Przydatny przykład

Oto analogia do lepszego zrozumienia zapytań między partycjami:

Wyobraźmy sobie, że jesteś kierowcą dostawy, który musi dostarczać paczki do różnych kompleksów mieszkaniowych. Każdy kompleks apartamentów dysponuje listą wszystkich numerów jednostek zamieszkania. Możemy porównać każdy kompleks apartamentów z partycją fizyczną i każdą listą z indeksem partycji fizycznej.

Możemy porównać zapytania w partycji i między partycjami przy użyciu tego przykładu:

### <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

Jeśli kierowca dostawy zna prawidłowy kompleks mieszkania (partycja fizyczna), może natychmiast jechać do właściwego budynku. Kierowca może sprawdzić listę numerów jednostek rezydenta (indeks) i szybko dostarczyć odpowiednie paczki. W takim przypadku kierowca nie traci czasu ani wysiłku na dojazd do kompleksu mieszkalnego, aby sprawdzić i sprawdzić, czy mieszkają tam odbiorcy paczki.

### <a name="cross-partition-query-fan-out"></a>Zapytanie między partycjami (wyjście wentylatora)

Jeśli kierowca dostawy nie zna właściwego kompleksu mieszkalnego (partycja fizyczna), będzie musiał jechać do każdego budynku mieszkalnego i sprawdzić listę ze wszystkimi numerami jednostek rezydenta (indeks). Po przybyciu do każdego kompleksu apartamentów nadal będą mogli korzystać z listy adresów każdego mieszkańca. Będą jednak musieli sprawdzić listę każdego kompleksu apartamentów, niezależnie od tego, czy mieszkają tam odbiorcy paczki, czy nie. Jest to jak działają kwerendy między partycjami. Chociaż mogą używać indeksu (nie trzeba pukać do każdego pojedynczego drzwi), muszą oddzielnie sprawdzić indeks dla każdej partycji fizycznej.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Kwerenda między partycjami (zakres tylko do kilku partycji fizycznych)

Jeśli kierowca dostawy wie, że wszyscy odbiorcy paczki mieszkają w niektórych kilku kompleksach mieszkaniowych, nie będą musieli jechać do każdego z nich. Podczas jazdy do kilku kompleksów mieszkaniowych nadal będzie wymagać więcej pracy niż zwiedzanie tylko jednego budynku, kierowca dostawczy nadal oszczędza dużo czasu i wysiłku. Jeśli kwerenda ma klucz partycji `IN` w filtrze ze słowem kluczowym, będzie sprawdzać tylko odpowiednie indeksy partycji fizycznej dla danych.

## <a name="avoiding-cross-partition-queries"></a>Unikanie zapytań między partycjami

W przypadku większości kontenerów jest nieuniknione, że będziesz miał kilka zapytań między partycjami. Posiadanie niektórych zapytań między partycjami jest ok! Prawie wszystkie operacje kwerendy są obsługiwane przez partycje (zarówno logiczne klucze partycji i partycje fizyczne). Usługa Azure Cosmos DB ma również wiele optymalizacji w aparat zapytań i zestawów SDK klienta do równoległości wykonywania zapytań między partycjami fizycznymi.

W przypadku większości scenariuszy o dużej częstości odczytu zalecamy po prostu wybranie najbardziej typowej właściwości w filtrach zapytań. Należy również upewnić się, że klucz partycji jest zgodny z innymi [najlepszymi praktykami wyboru klucza partycji](partitioning-overview.md#choose-partitionkey).

Unikanie zapytań między partycjami zazwyczaj ma znaczenie tylko w dużych kontenerach. Naliczana jest opłata co najmniej około 2,5 RU za każdym razem, gdy sprawdzasz indeks partycji fizycznej pod kątem wyników, nawet jeśli żadne elementy w partycji fizycznej nie są zgodne z filtrem kwerendy. W związku z tym, jeśli masz tylko jedną (lub tylko kilka) partycji fizycznych, zapytania między partycjami nie zużywają znacznie więcej ru niż w partycji zapytań.

Liczba partycji fizycznych jest powiązana z ilością aprowizowanych ru. Każda partycja fizyczna pozwala na maksymalnie 10 000 aprowizacji ru i może przechowywać do 50 GB danych. Usługa Azure Cosmos DB automatycznie będzie zarządzać partycjami fizycznymi dla Ciebie. Liczba partycji fizycznych w kontenerze zależy od aprowizowanej przepływności i używanego magazynu.

Należy unikać zapytań między partycjami, jeśli obciążenie spełnia poniższe kryteria:
- Planujesz mieć ponad 30.000 RU aprowizowanych
- Planujesz przechowywać ponad 100 GB danych

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat partycjonowania w usłudze Azure Cosmos DB:

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB (Syntetyczne klucze partycji w usłudze Azure Cosmos DB)](synthetic-partition-keys.md)
