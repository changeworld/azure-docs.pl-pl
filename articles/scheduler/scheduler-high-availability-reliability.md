---
title: Wysoka dostępność i niezawodność — usługa Azure Scheduler
description: Dowiedz się więcej o wysokiej dostępności i niezawodności w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533387"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Wysoka dostępność i niezawodność usługi Azure Scheduler

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

Usługa Azure Scheduler umożliwia zarówno [wysokiej dostępności](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) i niezawodność zadań. Aby uzyskać więcej informacji, zobacz [umowa SLA dla usługi Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Wysoka dostępność

Usługa Azure Scheduler jest [o wysokiej dostępności] i korzysta z obu wdrożenia magazynu geograficznie nadmiarowego usługi, jak i replikacja geograficzna regionalne zadania.

### <a name="geo-redundant-service-deployment"></a>Wdrożenie usługi magazynu geograficznie nadmiarowego

Usługa Azure Scheduler jest dostępna w witrynie Azure portal w prawie [co regionie geograficznym obsługiwanym przez platformę Azure już dziś](https://azure.microsoft.com/global-infrastructure/regions/#services). Dlatego jeśli Centrum danych platformy Azure w regionie hostowanej staje się niedostępny, nadal mogą używać usługi Azure Scheduler, ponieważ możliwości trybu failover usługi Scheduler dostępne w innym centrum danych.

### <a name="geo-regional-job-replication"></a>Replikacja geograficzna regionalne zadania

Zadania w usłudze Azure Scheduler są replikowane między regionami platformy Azure. Dlatego jeśli jeden region ulegnie awarii, usługa Azure Scheduler w trybie Failover i upewnia się, że zadanie zostanie uruchomiona w innym centrum danych w sparowanym regionie geograficznym.

Na przykład możesz utworzyć zadanie w południowo-środkowe stany USA, Azure Scheduler automatycznie replikuje dane zadania w północno-środkowe stany USA. Jeśli wystąpi awaria w południowo-środkowe stany USA, Azure Scheduler uruchamia zadanie w północno-środkowe stany USA. 

![Replikacja geograficzna regionalne zadania](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Usługa Azure Scheduler sprawia, że się, że danych pozostaje w obrębie regionu ten sam, ale szersze geograficznym na wypadek awarii odbywa się w usłudze Azure. Nie trzeba więc zduplikowane zadań, jeśli chcesz tylko wysokiej dostępności. Usługa Azure Scheduler automatycznie zapewnia wysoką dostępność dla zadań.

## <a name="reliability"></a>Niezawodność

Usługa Azure Scheduler gwarantuje swój własny wysokiej dostępności, ale trwa innego podejścia do zadań utworzonych przez użytkownika. Na przykład załóżmy, że zadanie wywołuje punkt końcowy HTTP, który jest niedostępny. Usługa Azure Scheduler próbuje nadal pomyślnie uruchomić zadanie, dając alternatywnych sposobów obsługi błędów: 

* Skonfiguruj zasady ponawiania prób.
* Skonfiguruj alternatywne punkty końcowe.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zasady ponawiania prób

Usługa Azure Scheduler umożliwia konfigurowanie zasad ponawiania prób. Jeśli zadanie nie powiedzie się, następnie domyślnie harmonogramu ponawia próbę zadania, cztery razy w odstępach 30 sekund. Możesz ustawić te zasady ponawiania wyższe, takie jak 10 razy w odstępach 30 sekund lub mniej agresywne, takie jak dwa razy na codziennie.

Na przykład załóżmy, że należy utworzyć zadanie co tydzień, który wywołuje punkt końcowy HTTP. Jeśli punkt końcowy HTTP będą niedostępne przez kilka godzin, podczas wykonywania zadania, nie można oczekiwać innego tygodnia dla zadanie zostanie uruchomione ponownie, która wynika z faktu, domyślną zasadę ponownych prób nie będzie działać w tym przypadku. Tak możesz chcieć zmienić zasady ponawiania standard, aby się zdarzyć ponownych prób, na przykład co trzy godziny, a nie co 30 sekund. 

Aby dowiedzieć się, jak skonfigurować zasady ponawiania, zobacz [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternatywne punkty końcowe

Jeśli zadanie usługi Azure Scheduler wywołuje punkt końcowy, który jest niedostępny, mimo zasady ponawiania, harmonogram jest powraca do alternatywnego punktu końcowego, który może obsłużyć takie błędy. Tak Jeśli skonfigurowano ten punkt końcowy usługi Scheduler wywołuje tego punktu końcowego, co sprawia, że własne zadania o wysokiej dostępności po zdarzają się błędy.

Na przykład ten diagram przedstawia, jak harmonogram obowiązują zasady ponawiania podczas wywoływania usługi sieci web w Nowym Jorku. Jeśli ponowne próby się nie powieść, harmonogram sprawdza punkcie końcowym alternate. Jeśli istnieje punkt końcowy, rozpoczyna się harmonogram wysyłania żądań do alternatywnego punktu końcowego. Te same zasady ponawiania dotyczy oryginalnej operacji i alternatywne działanie.

![Zachowanie usługi Scheduler za pomocą zasad ponawiania i punkcie końcowym alternate](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akcji dla alternatywnych akcji może się różnić od oryginalnej operacji. Na przykład mimo że oryginalnej operacji wywołuje punkt końcowy HTTP, alternatywne działanie może on rejestrować błędy przy użyciu kolejki magazynu, kolejki usługi Service Bus lub akcji tematu magistrali usług.

Aby dowiedzieć się, jak skonfigurować alternatywne punktu końcowego, zobacz [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia, terminologia oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Tworzenie złożonych i zaawansowanych harmonogramów cyklicznych](scheduler-advanced-complexity.md)
* [Limity, limity przydziału, wartości domyślne i kody błędów](scheduler-limits-defaults-errors.md)
