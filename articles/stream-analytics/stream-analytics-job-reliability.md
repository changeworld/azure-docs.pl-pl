---
title: Unikaj przerw w działaniu usług w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano wskazówki na tworzenie zadań usługi Stream Analytics uaktualnienia odporne na błędy.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425988"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zagwarantowania niezawodności zadania usługi Stream Analytics podczas aktualizacji usługi

Część jest w pełni zarządzana usługa jest możliwość wprowadzenia nowych funkcji usługi i ulepszenia w szybkim tempie. W rezultacie usługi Stream Analytics może mieć aktualizacji usługi, wdrażanie na podstawie co tydzień (lub częściej). Niezależnie od tego, jaką część testów odbywa się nadal istnieje ryzyko, że istniejący, uruchomione zadania mogą przestać działać z powodu wprowadzenia usterkę. W przypadku uruchamiania zadań o kluczowym znaczeniu należy unikać tych zagrożeń. Możesz zmniejszyć to ryzyko, wykonując na **[sparowanym modelu regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** platformy Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak regiony sparowane platformy Azure rozwiązania tego problemu?

Stream Analytics gwarancję, że zadania w sparowanych regionach są aktualizowane w oddzielnych plikach wsadowych. W związku z tym istnieje wystarczająco dużo czasu między aktualizacjami do identyfikowania potencjalnych problemów i ich korygowania.

_Z wyjątkiem Indie środkowe_ (których sparowanym regionie Indie Południowe, nie ma obecności usługi Stream Analytics), wdrożenie aktualizacji do usługi Stream Analytics nie może mieć miejsce, w tym samym czasie w zestawie połączonych w parę regionów. Wdrożenia w wielu regionach **w tej samej grupie** może wystąpić **w tym samym czasie**.

Artykuł dotyczący **[dostępności i sparowane regiony](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje, na którym są sparowane regiony.

Zaleca się wdrożenie identycznych zadań w obu sparowanych regionach. Następnie należy [monitorować te zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , aby otrzymywać powiadomienia o nieoczekiwanym wystąpieniu. Jeśli jedno z tych zadań kończy [się niepowodzeniem](https://docs.microsoft.com/azure/stream-analytics/job-states) po aktualizacji usługi Stream Analytics, możesz skontaktować się z działem pomocy technicznej, aby pomóc w zidentyfikowaniu głównej przyczyny. Należy również przełączać się do trybu failover dla wszystkich klientów podrzędnych w dobrej kondycji.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
