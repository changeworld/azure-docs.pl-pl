---
title: Wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia
description: Czas wygaśnięcia Microsoft Azure Cosmos DB zapewnia możliwość wprowadzenia dokumentów automatycznie usuwane z systemu po upływie określonego czasu.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 0b32665b09eb02c337a12ac3cfc2b474fa82711a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447242"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Czas wygaśnięcia (TTL) w usłudze Azure Cosmos DB 

Za pomocą **czas wygaśnięcia** lub czas wygaśnięcia, usługa Azure Cosmos DB zapewnia możliwość automatycznie usunąć elementy z kontenera po upływie pewnego czasu. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąp wartość na podstawie poszczególnych elementów. Po ustawieniu czas wygaśnięcia w kontenerze, lub na poziomie elementu, usługi Azure Cosmos DB automatycznie usunie te elementy po okresie od czasu ostatniej modyfikacji. Wartość czasu wygaśnięcia skonfigurowano w ciągu kilku sekund. Podczas konfigurowania czasu wygaśnięcia, system automatycznie usunie wygasłych elementów na podstawie wartości TTL bez konieczności używania operacji usuwania, jawnie wydawanego przez aplikację klienta.

## <a name="time-to-live-for-containers-and-items"></a>Czas wygaśnięcia dla kontenerów i elementów

Wartość czasu wygaśnięcia jest ustawiana w ciągu kilku sekund, a zostanie on zinterpretowany jako zmian od czasu ostatniej modyfikacji elementu. Można ustawić czas wygaśnięcia w kontenerze lub elementu w kontenerze:

1. **Czas wygaśnięcia w kontenerze** (można ustawić przy użyciu `DefaultTimeToLive`):

   - Jeśli brak (lub ustawionej na wartość null), elementy nie są automatycznie wygasł.

   - Jeśli istnieje, a wartość jest równa "-1", jest równa nieskończoność, a elementy nie wygasają domyślnie.

   - Jeśli istnieje, a wartość jest równa niektóre *"n"* — elementy wygasną *"n"* (w sekundach) po ich ostatniej modyfikacji.

2. **Czas wygaśnięcia elementu** (można ustawić przy użyciu `ttl`):

   - Ta właściwość ma zastosowanie tylko wtedy, gdy `DefaultTimeToLive` jest obecny i nie jest ustawiona na wartość null dla kontenera nadrzędnego.

   - Jeśli jest obecna, zastępuje ona `DefaultTimeToLive` wartość kontenera nadrzędnego.

## <a name="time-to-live-configurations"></a>Czas do konfiguracji na żywo

* Jeśli czas wygaśnięcia jest równa *"n"* w kontenerze, następnie elementy w danym kontenerze wygasną po upływie *n* sekund.  Jeśli istnieją elementy w tym samym kontenerze, które mają czas wprowadzenia na na żywo, ustawioną wartość -1 (wskazującą, że nie wygasną) lub jeśli niektóre elementy przesłonili czas wygaśnięcia ustawienie z różnymi liczbami, te elementy wygasają oparte na ich własnych skonfigurowana wartość czasu wygaśnięcia. 

* Jeśli czas wygaśnięcia nie jest ustawiona na kontenerze, czas wygaśnięcia elementu w tym kontenerze jest ignorowany. 

* Jeśli czas wygaśnięcia w kontenerze jest ustawiony na wartość -1, element w tym kontenerze, który ma czas na żywo wartość n, wygasną po upływie n sekund, a pozostałe elementy nie wygasną. 

Usuwanie elementów, w oparciu o czas wygaśnięcia jest bezpłatne. Nie ma żadnych dodatkowych kosztów (czyli nie dodatkowych jednostek żądań są używane) w momencie usunięcia elementu wyniku z upływem czasu wygaśnięcia.

## <a name="examples"></a>Przykłady

W tej sekcji przedstawiono kilka przykładów przy użyciu innego czasu wygaśnięcia wartości przypisane do kontenera i elementy:

### <a name="example-1"></a>Przykład 1

Czas wygaśnięcia w kontenerze jest ustawiona na wartość null (DefaultTimeToLive = null)

|Czas wygaśnięcia elementu| Wynik|
|---|---|
|czas wygaśnięcia = null|    Czas wygaśnięcia jest wyłączona. Element nigdy nie wygasa (ustawienie domyślne).|
|ttl = -1   |Czas wygaśnięcia jest wyłączona. Element nigdy nie wygasa.|
|ttl = 2000 |Czas wygaśnięcia jest wyłączona. Element nigdy nie wygasa.|


### <a name="example-2"></a>Przykład 2

Czas wygaśnięcia w kontenerze jest ustawiona na wartość -1 (DefaultTimeToLive = -1)

|Czas wygaśnięcia elementu| Wynik|
|---|---|
|czas wygaśnięcia = null |Czas wygaśnięcia jest włączona. Element nigdy nie wygasa (ustawienie domyślne).|
|ttl = -1   |Czas wygaśnięcia jest włączona. Element nigdy nie wygasa.|
|ttl = 2000 |Czas wygaśnięcia jest włączona. Element wygaśnie po 2000 sekundach.|


### <a name="example-3"></a>Przykład 3

Czas wygaśnięcia w kontenerze jest ustawiony na 1000 (DefaultTimeToLive = 1000)

|Czas wygaśnięcia elementu| Wynik|
|---|---|
|czas wygaśnięcia = null|    Czas wygaśnięcia jest włączona. Element wygaśnie po 1 000 sekund (ustawienie domyślne).|
|ttl = -1   |Czas wygaśnięcia jest włączona. Element nigdy nie wygasa.|
|ttl = 2000 |Czas wygaśnięcia jest włączona. Element wygaśnie po 2000 sekundach.|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować czas wygaśnięcia w następujących artykułach:

* [Jak skonfigurować czas wygaśnięcia](how-to-time-to-live.md)
