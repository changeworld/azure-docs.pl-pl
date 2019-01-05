---
title: Wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia
description: Czas wygaśnięcia Microsoft Azure Cosmos DB zapewnia możliwość wprowadzenia dokumentów automatycznie usuwane z systemu po upływie określonego czasu.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fbbaefc62adedc2374c47fd0736368d3dec3e6a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043434"
---
# <a name="time-to-live-for-azure-cosmos-db-data"></a>Czas wygaśnięcia danych usługi Azure Cosmos DB

Z czasem wygaśnięcia lub "Time to Live" usługi Azure Cosmos DB zapewnia możliwość automatycznie usunąć elementy z kontenera po upływie pewnego czasu. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąp wartość na podstawie poszczególnych elementów. Po ustawieniu czas wygaśnięcia w kontenerze, lub na poziomie elementu, usługi Azure Cosmos DB automatycznie usunie te elementy po okresie od czasu ostatniej modyfikacji. Wartość czasu wygaśnięcia skonfigurowano w ciągu kilku sekund. Podczas konfigurowania czasu wygaśnięcia, system automatycznie usunie wygasłych elementów na podstawie wartości TTL, w przeciwieństwie do operacji usuwania, jawnie wydawanego przez aplikację klienta.

## <a name="time-to-live-for-containers-and-items"></a>Czas wygaśnięcia dla kontenerów i elementów

Wartość czasu wygaśnięcia jest ustawiany w ciągu kilku sekund, a zostanie on zinterpretowany jako zmian od czasu ostatniej modyfikacji elementu. Można ustawić czas wygaśnięcia w kontenerze lub elementu w kontenerze:

1. **Czas wygaśnięcia w kontenerze** (można ustawić przy użyciu `DefaultTimeToLive`):

   - Jeśli brak (lub ustawionej na wartość null), elementy nie są automatycznie wygasł.

   - Jeśli istnieje, a wartość jest równa "-1", jest równa nieskończonej — elementy nie wygasają domyślnie.

   - Jeśli istnieje, a wartość jest równa niektóre numer ("n") — elementy wygasają "n" sekund po ich ostatniej modyfikacji.

2. **Czas wygaśnięcia elementu** (można ustawić przy użyciu `TimeToLive`):

   - Ta właściwość ma zastosowanie tylko wtedy, gdy `DefaultTimeToLive` jest obecny i nie jest ustawiona na wartość null dla kontenera nadrzędnego.

   - Jeśli jest obecna, zastępuje ona `DefaultTimeToLive` wartość kontenera nadrzędnego.

## <a name="time-to-live-configurations"></a>Czas do konfiguracji na żywo

* Jeśli czas wygaśnięcia jest równa "n" w kontenerze, elementy w danym kontenerze będzie tracą ważność po n sekund.  Jeśli istnieją elementy w tym samym kontenerze, które mają czas wprowadzenia na na żywo, ustawioną wartość -1 (wskazującą, że nie wygasną) lub jeśli niektóre elementy przesłonili czas wygaśnięcia ustawienie z różnymi liczbami, te elementy wygasają w oparciu skonfigurowana wartość czasu wygaśnięcia. 

* Jeśli czas wygaśnięcia nie jest ustawiona na kontenerze, czas wygaśnięcia elementu w tym kontenerze jest ignorowany. 

* Jeśli czas wygaśnięcia w kontenerze jest ustawiony na wartość -1, element w tym kontenerze, który ma czas na żywo wartość n, wygasną po upływie n sekund, a pozostałe elementy nie wygasną. 

Usuwanie elementów, w oparciu o czas wygaśnięcia jest bezpłatne. Nie ma żadnych dodatkowych kosztów (czyli nie dodatkowych jednostek żądań są używane) w momencie usunięcia elementu wyniku z upływem czasu wygaśnięcia.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować Kafelek, aby na żywo w następujących artykułach:

* [Jak skonfigurować czas wygaśnięcia](how-to-time-to-live.md)
