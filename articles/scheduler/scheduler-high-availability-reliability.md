---
title: Wysoka dostępność i niezawodność
description: Dowiedz się więcej o wysokiej dostępności i niezawodności w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898559"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Wysoka dostępność i niezawodność dla usługi Azure Scheduler

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

Usługa Azure Scheduler zapewnia zarówno [wysoką dostępność,](https://docs.microsoft.com/azure/architecture/framework/#resiliency) jak i niezawodność zadań. Aby uzyskać więcej informacji, zobacz [SLA dla harmonogramu](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Wysoka dostępność

Usługa Azure Scheduler jest [wysoce dostępna] i używa zarówno wdrożenia usługi geograficznie nadmiarowej, jak i replikacji zadań georegionalnych.

### <a name="geo-redundant-service-deployment"></a>Wdrożenie usługi geograficznie nadmiarowej

Usługa Azure Scheduler jest obecnie dostępna w prawie [każdym regionie geograficznym obsługiwanym przez platformę Azure.](https://azure.microsoft.com/global-infrastructure/regions/#services) Jeśli centrum danych platformy Azure w obsługiwanym regionie stanie się niedostępne, nadal można użyć usługi Azure Scheduler, ponieważ możliwości pracy awaryjnej usługi udostępniają harmonogram z innego centrum danych.

### <a name="geo-regional-job-replication"></a>Replikacja zadań georegionalnych

Własne zadania w usłudze Azure Scheduler są replikowane w regionach platformy Azure. Więc jeśli jeden region ma awarię, harmonogram platformy Azure kończy się niepowodzeniem i upewnia się, że zadanie jest uruchamiane z innego centrum danych w sparowanym regionie geograficznym.

Na przykład jeśli utworzysz zadanie w południowo-środkowych stanach USA, usługa Azure Scheduler automatycznie replikuje to zadanie w północno-środkowych stanach USA. Jeśli błąd występuje w południowo-środkowych stanach USA, usługa Azure Scheduler uruchamia zadanie w północno-środkowych stanach USA. 

![Replikacja zadań georegionalnych](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Usługa Azure Scheduler zapewnia również, że dane pozostają w tym samym, ale szerszym regionie geograficznym, na wypadek awarii na platformie Azure. Tak więc nie musisz powielać zadań, gdy chcesz po prostu uzyskać wysoką dostępność. Usługa Azure Scheduler automatycznie zapewnia wysoką dostępność dla zadań.

## <a name="reliability"></a>Niezawodność

Usługa Azure Scheduler gwarantuje własną wysoką dostępność, ale przyjmuje inne podejście do zadań utworzonych przez użytkownika. Załóżmy na przykład, że zadanie wywołuje punkt końcowy HTTP, który jest niedostępny. Usługa Azure Scheduler nadal próbuje pomyślnie uruchomić zadanie, zapewniając alternatywne sposoby obsługi błędów: 

* Konfigurowanie zasad ponawiania prób.
* Konfigurowanie alternatywnych punktów końcowych.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zasady ponawiania prób

Harmonogram platformy Azure umożliwia skonfigurowanie zasad ponawiania prób. Jeśli zadanie nie powiedzie się, to domyślnie harmonogram ponawia zadanie jeszcze cztery razy w odstępach 30-sekundowych. Można uczynić tę zasadę ponawiania bardziej agresywne, takie jak 10 razy w odstępach 30-sekundowych lub mniej agresywne, takie jak dwa razy w odstępach dziennych.

Załóżmy na przykład, że tworzysz zadanie tygodniowe, które wywołuje punkt końcowy HTTP. Jeśli punkt końcowy HTTP staje się niedostępny przez kilka godzin po uruchomieniu zadania, możesz nie chcieć czekać w kolejnym tygodniu na ponowne uruchomienie zadania, co ma miejsce, ponieważ domyślna zasada ponawiania próby nie będzie działać w tym przypadku. Dlatego można zmienić standardowe zasady ponawiania próby, tak aby próby ponawiane, na przykład co trzy godziny, a nie co 30 sekund. 

Aby dowiedzieć się, jak skonfigurować zasady ponawiania prób, zobacz [ponawianie próbyPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternatywne punkty końcowe

Jeśli zadanie planowania platformy Azure wywołuje punkt końcowy, który jest nieosiągalny, nawet po przestrzeganiu zasad ponawiania, harmonogram powraca do alternatywnego punktu końcowego, który może obsługiwać takie błędy. Tak więc jeśli skonfigurujesz ten punkt końcowy, Harmonogram wywołuje ten punkt końcowy, co sprawia, że własne zadania są wysoce dostępne, gdy pojawią się awarie.

Na przykład ten diagram pokazuje, jak harmonogram jest zgodny z zasadami ponawiania podczas wywoływania usługi sieci web w Nowym Jorku. Jeśli ponownych prób zakończyć się niepowodzeniem, harmonogram sprawdza alternatywny punkt końcowy. Jeśli punkt końcowy istnieje, harmonogram rozpoczyna wysyłanie żądań do alternatywnego punktu końcowego. Ta sama zasada ponawiania próby ma zastosowanie zarówno do oryginalnej akcji, jak i akcji alternatywnej.

![Zachowanie harmonogramu z zasadami ponawiania próby i alternatywnym punktem końcowym](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akcji dla akcji alternatywnej może różnić się od oryginalnej akcji. Na przykład chociaż oryginalna akcja wywołuje punkt końcowy HTTP, akcja alternatywna może rejestrować błędy przy użyciu kolejki magazynu, kolejki usługi Service Bus lub akcji tematu usługi Service Bus.

Aby dowiedzieć się, jak skonfigurować alternatywny punkt końcowy, zobacz [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Następne kroki

* [Pojęcia, terminologia oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
* [Limity, limity przydziału, wartości domyślne i kody błędów](scheduler-limits-defaults-errors.md)
