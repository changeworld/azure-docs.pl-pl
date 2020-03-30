---
title: Wygasają dane w usłudze Azure Cosmos DB z czasem wygaśnięcia
description: Dzięki ttl, Microsoft Azure Cosmos DB zapewnia możliwość dokumentów automatycznie usuwane z systemu po pewnym czasie.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188717"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Czas wygaśnięcia (TTL) w usłudze Azure Cosmos DB 

Z **time to live** lub TTL usługi Azure Cosmos DB zapewnia możliwość automatycznego usuwania elementów z kontenera po pewnym czasie. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąpić wartość na podstawie dla elementu. Po ustawieniu czasu wygaśnięcia w kontenerze lub na poziomie elementu usługa Azure Cosmos DB automatycznie usunie te elementy po upływie tego okresu, od czasu, w którego zostały ostatnio zmodyfikowane. Czas na żywo wartość jest skonfigurowany w sekundach. Podczas konfigurowania czasu wygaśnięcia system automatycznie usunie wygasłe elementy na podstawie wartości TTL, bez konieczności operacji usuwania, która jest jawnie wystawiona przez aplikację kliencką.

Usuwanie wygasłych elementów jest zadaniem w tle, które zużywa resztki [jednostek żądań,](request-units.md)czyli jednostek żądań, które nie zostały wykorzystane przez żądania użytkowników. Nawet po wygaśnięciu czasu wygaśnięcia, jeśli kontener jest przeciążony żądaniami i jeśli nie ma wystarczającej ilości dostępnych ru, usunięcie danych jest opóźnione. Dane są usuwane, gdy jest wystarczająco dużo procesorów operacyjnych dostępnych do wykonania operacji usuwania. Chociaż usunięcie danych jest opóźnione, dane nie są zwracane przez żadne zapytania (przez dowolny interfejs API) po wygaśnięciu czasu wygaśnięcia.

## <a name="time-to-live-for-containers-and-items"></a>Czas na życie dla kontenerów i przedmiotów

Czas na żywo wartość jest ustawiona w sekundach i jest interpretowany jako delta od czasu, że element został ostatnio zmodyfikowany. Można ustawić czas życia w kontenerze lub elemencie w kontenerze:

1. **Czas na żywo na kontenerze** (zestaw za pomocą): `DefaultTimeToLive`

   - Jeśli brakuje (lub ustawiono wartość null), elementy nie wygasają automatycznie.

   - Jeśli obecny i wartość jest ustawiona na "-1", jest równa nieskończoności, a elementy nie wygasa domyślnie.

   - Jeśli jest obecny, a wartość jest ustawiona na pewną liczbę *"n"* - elementy wygasną *"n"* sekund po ich ostatniej modyfikacji czasu.

2. **Czas na żywo na elemencie** (zestaw za pomocą): `ttl`

   - Ta właściwość ma `DefaultTimeToLive` zastosowanie tylko wtedy, gdy jest obecny i nie jest ustawiona na wartość null dla kontenera nadrzędnego.

   - Jeśli jest obecny, zastępuje `DefaultTimeToLive` wartość kontenera nadrzędnego.

## <a name="time-to-live-configurations"></a>Konfiguracje time to live

* Jeśli TTL jest ustawiona na *"n"* w kontenerze, elementy w tym kontenerze wygasną po *n* sekund.  Jeśli istnieją elementy w tym samym kontenerze, które mają swój własny czas do żywo, ustawiona na -1 (wskazując, że nie wygasają) lub jeśli niektóre elementy zostały zastąpione ustawieniem czasu na żywo z inną liczbą, te elementy wygasają na podstawie ich własnej skonfigurowaną wartość TTL. 

* Jeśli TTL nie jest ustawiona na kontenerze, a następnie czas do zamieszkania na element w tym kontenerze nie ma wpływu. 

* Jeśli czas wygaśnięcia w kontenerze jest ustawiony na -1, element w tym kontenerze, który ma czas na żywo ustawiony na n, wygaśnie po n sekund, a pozostałe elementy nie wygasną.

## <a name="examples"></a>Przykłady

W tej sekcji przedstawiono kilka przykładów z różnymi wartościami czasu do żywo przypisanymi do kontenera i elementów:

### <a name="example-1"></a>Przykład 1

TTL w kontenerze jest ustawiony na wartość null (DefaultTimeToLive = null)

|Czas wygaśnięcia towaru| Wynik|
|---|---|
|ttl = null|    Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie (domyślnie).|
|ttl = -1   |Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie.|
|ttl = 2000 |Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie.|


### <a name="example-2"></a>Przykład 2

Czas wygaśnięcia w kontenerze jest ustawiony na -1 (DefaultTimeToLive = -1)

|Czas wygaśnięcia towaru| Wynik|
|---|---|
|ttl = null |TTL jest włączona. Element nigdy nie wygaśnie (domyślnie).|
|ttl = -1   |TTL jest włączona. Element nigdy nie wygaśnie.|
|ttl = 2000 |TTL jest włączona. Przedmiot wygaśnie po 2000 sekundach.|


### <a name="example-3"></a>Przykład 3

Czas wygaśnięcia w kontenerze jest ustawiony na 1000 (DefaultTimeToLive = 1000)

|Czas wygaśnięcia towaru| Wynik|
|---|---|
|ttl = null|    TTL jest włączona. Element wygaśnie po 1000 sekundach (domyślnie).|
|ttl = -1   |TTL jest włączona. Element nigdy nie wygaśnie.|
|ttl = 2000 |TTL jest włączona. Przedmiot wygaśnie po 2000 sekundach.|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować czas do życia w następujących artykułach:

* [Jak skonfigurować czas na żywo](how-to-time-to-live.md)
