---
title: Unikaj przerw w działaniu usług w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano wskazówki na tworzenie zadań usługi Stream Analytics uaktualnienia odporne na błędy.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479460"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zagwarantowania niezawodności zadania usługi Stream Analytics podczas aktualizacji usługi

Część jest w pełni zarządzana usługa jest możliwość wprowadzenia nowych funkcji usługi i ulepszenia w szybkim tempie. W rezultacie usługi Stream Analytics może mieć aktualizacji usługi, wdrażanie na podstawie co tydzień (lub częściej). Niezależnie od tego, jaką część testów odbywa się nadal istnieje ryzyko, że istniejący, uruchomione zadania mogą przestać działać z powodu wprowadzenia usterkę. Dla klientów, którzy uruchamiają krytyczne zadania przetwarzania strumieniowego te zagrożenia należy unikać. Klienci mogą używać, aby ograniczyć to ryzyko mechanizm jest platformy Azure **[sparowanym regionie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak regiony sparowane platformy Azure rozwiązania tego problemu?

Stream Analytics gwarancję, że zadania w sparowanych regionach są aktualizowane w oddzielnych plikach wsadowych. W wyniku jest wystarczające odstęp czasu między aktualizacjami, aby zidentyfikować potencjalne błędy podziału i je skorygować.

_Z wyjątkiem Indie środkowe_ (których sparowanym regionie Indie Południowe, nie ma obecności usługi Stream Analytics), wdrożenie aktualizacji do usługi Stream Analytics nie może mieć miejsce, w tym samym czasie w zestawie połączonych w parę regionów. Wdrożenia w wielu regionach **w tej samej grupie** może wystąpić **w tym samym czasie**.

Artykuł dotyczący **[dostępności i sparowane regiony](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje, na którym są sparowane regiony.

Klienci są zaleca się wdrażanie identyczne zadania na obu połączonych w parę regionów. Oprócz Stream Analytics wewnętrznego możliwości monitorowania, klienci także doradza się do monitorowania zadań tak, jakby **zarówno** zadań w środowisku produkcyjnym. Jeśli podział jest identyfikowany jako wynik aktualizacji usługi Stream Analytics, eskalować odpowiednio i wszystkie podrzędne w konsumentach napisanych dane wyjściowe zadania działa prawidłowo w trybie Failover. Podwyższenie poziomu do obsługi będzie zapobiec wpływowi nowe wdrożenie w sparowanym regionie i utrzymania spójności sparowane zadań.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
