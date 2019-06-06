---
title: Wskazówki dotyczące odzyskiwania po awarii usługi Azure Data Lake Analytics
description: Dowiedz się, jak zaplanować odzyskiwanie po awarii dla kont usługi Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498892"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Wskazówki dotyczące odzyskiwania po awarii usługi Azure Data Lake Analytics

Usługa Azure Data Lake Analytics jest usługą zadań analizy na żądanie, która pozwala uprościć analizowanie danych big data. Zamiast wdrażać, konfigurować i dostosowywać sprzęt, możesz pisać zapytania umożliwiające przekształcanie danych i wyodrębniać wartościowe informacje. Usługa analizy może od razu rozpocząć obsługę zadań w dowolnej skali — należy wybrać ustawienie wskazujące potrzebną moc. Płacisz tylko za uruchomione zadanie, dzięki czemu oszczędzasz pieniądze. Ten artykuł zawiera wskazówki na temat ochrony zadań z rzadko awariami lub przypadkowymi usunięciami.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii

Korzystając z usługi Azure Data Lake Analytics, ważne jest, aby przygotować własny plan odzyskiwania po awarii. W tym artykule zawarto ułatwiają tworzenie planu odzyskiwania po awarii. Istnieją dodatkowe zasoby, które mogą pomóc w tworzeniu własnego planu:
- [Failure and disaster recovery for Azure applications (Awarie i odzyskiwanie po awariach w aplikacjach platformy Azure)](/azure/architecture/reliability/disaster-recovery)
- [Odporność platformy Azure — wskazówki techniczne](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Najlepsze praktyki i wytyczne scenariusza

Cykliczne zadania U-SQL można uruchomić na koncie ADLA w regionie, który odczytuje i zapisuje je w tabelach U-SQL, a także dane bez określonej struktury.  Przygotowanie do awarii, wykonując następujące kroki:

1. Utwórz konta ADLA i Azure Data Lake Store w regionie dodatkowym, które będą używane podczas wystąpienia awarii.

   > [!NOTE]
   > Ponieważ nazwy kont są globalnie unikatowe, należy użyć spójnego nazewnictwa wskazującą, które konto to dodatkowa baza danych.

2. Dla danych bez struktury, należy odwołać [wskazówki dotyczące odzyskiwania po awarii dla danych w usłudze Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Dla ustrukturyzowanymi danymi przechowywanymi w tabelach ADLA i baz danych należy utworzyć kopie artefakty metadane, takie jak bazy danych, tabel, zwracających tabelę i zestawów. Należy okresowo ponownie zsynchronizować te artefakty, w przypadku wprowadzenia zmiany w środowisku produkcyjnym. Na przykład nowo wstawionej dane mają być replikowane do regionu pomocniczego, kopiując dane, a następnie wstawianie do tabeli pomocniczej.

   > [!NOTE]
   > Te nazwy obiektu są ograniczone do pomocniczego konta i nie są globalnie unikatowa, dzięki czemu mogą mieć takich samych nazwach jak konto produkcji podstawowej.

Podczas awarii należy zaktualizować skrypty, tak aby wskazywała ścieżek wejściowych pomocniczego punktu końcowego. Następnie użytkownicy przesyłanie ich zadań na koncie ADLA w regionie pomocniczym. Dane wyjściowe zadania następnie będą zapisywane na koncie ADLA i Azure Data Lake Store w regionie pomocniczym.

## <a name="next-steps"></a>Kolejne kroki

[Wskazówki dotyczące odzyskiwania po awarii dla danych w usłudze Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
