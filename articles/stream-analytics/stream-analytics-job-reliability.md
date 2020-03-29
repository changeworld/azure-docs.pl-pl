---
title: Unikanie przerw w świadczeniu usług w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano wskazówki dotyczące zwiększania możliwości uaktualnienia zadań usługi Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425988"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Gwarancja niezawodności zadań usługi Stream Analytics podczas aktualizacji usług

Częścią w pełni zarządzanej usługi jest możliwość wprowadzania nowych funkcji usługi i ulepszeń w szybkim tempie. W rezultacie usługa Stream Analytics może być wdrażana co tydzień (lub częściej) w ramach aktualizacji usługi. Bez względu na to, jak wiele testów jest wykonywana, nadal istnieje ryzyko, że istniejące, uruchomione zadanie może ulec przerwaniu z powodu wprowadzenia błędu. Jeśli korzystasz z zadań o znaczeniu krytycznym, należy unikać tych zagrożeń. Możesz zmniejszyć to ryzyko, wykonując **[model regionu sparowanego](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** platformy Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>W jaki sposób sparowane regiony platformy Azure rozwiązać ten problem?

Usługa Stream Analytics gwarantuje, że zadania w sparowanych regionach są aktualizowane w oddzielnych partiach. W rezultacie istnieje wystarczająca różnica czasowa między aktualizacjami, aby zidentyfikować potencjalne problemy i je naprawić.

_Z wyjątkiem Indii Centralnych_ (których sparowany region, Indie Południowe, nie ma obecności usługi Stream Analytics), wdrożenie aktualizacji usługi Stream Analytics nie nastąpi w tym samym czasie w zestawie sparowanych regionów. Wdrożenia w wielu regionach **tej samej grupy** mogą wystąpić **w tym samym czasie.**

Artykuł o **[dostępności i sparowanych regionach](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje o tym, które regiony są sparowane.

Zaleca się wdrożenie identycznych zadań w obu sparowanych regionach. Następnie należy [monitorować te zadania,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) aby otrzymywać powiadomienia, gdy dzieje się coś nieoczekiwanego. Jeśli jedno z tych zadań kończy się w [stanie failed](https://docs.microsoft.com/azure/stream-analytics/job-states) po aktualizacji usługi Usługi Usługi Stream Analytics, można skontaktować się z obsługą klienta, aby pomóc zidentyfikować główną przyczynę. Należy również w trybie fail over wszystkich dalszych konsumentów do wydajności pracy w dobrej kondycji.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do analizy strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Odwołanie do języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odwołanie do interfejsu API REST dla zarządzania usługą Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
