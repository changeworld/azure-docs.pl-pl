---
title: Unikaj przerw w działaniu usług w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano wskazówki na tworzenie zadań usługi Stream Analytics uaktualnienia odporne na błędy.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620879"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zagwarantowania niezawodności zadania usługi Stream Analytics podczas aktualizacji usługi

Część jest w pełni zarządzana usługa jest możliwość wprowadzenia nowych funkcji usługi i ulepszenia w szybkim tempie. W rezultacie usługi Stream Analytics może mieć aktualizacji usługi, wdrażanie na podstawie co tydzień (lub częściej). Niezależnie od tego, jaką część testów odbywa się nadal istnieje ryzyko, że istniejący, uruchomione zadania mogą przestać działać z powodu wprowadzenia usterkę. Jeśli używasz misji krytyczne zadania, te zagrożenia należy unikać. Można zmniejszyć to ryzyko przez następujące platformy Azure **[sparowanym regionie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak regiony sparowane platformy Azure rozwiązania tego problemu?

Stream Analytics gwarancję, że zadania w sparowanych regionach są aktualizowane w oddzielnych plikach wsadowych. W wyniku jest wystarczające odstęp czasu między aktualizacjami do identyfikowania potencjalnych problemów i je skorygować.

_Z wyjątkiem Indie środkowe_ (których sparowanym regionie Indie Południowe, nie ma obecności usługi Stream Analytics), wdrożenie aktualizacji do usługi Stream Analytics nie może mieć miejsce, w tym samym czasie w zestawie połączonych w parę regionów. Wdrożenia w wielu regionach **w tej samej grupie** może wystąpić **w tym samym czasie**.

Artykuł dotyczący **[dostępności i sparowane regiony](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje, na którym są sparowane regiony.

Zaleca się wdrażanie identyczne zadania na obu połączonych w parę regionów. Następnie należy [monitorowania tych zadań](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) powiadomieniom zdarzy się coś nieoczekiwanego. Jeśli jedno z tych zadań kończy się się w [stanu niepowodzenia](https://docs.microsoft.com/azure/stream-analytics/job-states) po zaktualizowaniu usługi Stream Analytics, możesz skontaktować się ze obsługi klienta, aby ułatwić zidentyfikowanie przyczyny. Należy również przejścia w tryb failover wszystkie podrzędne w konsumentach napisanych dane wyjściowe zadania w dobrej kondycji.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
