---
title: Harmonogram wysokiej dostępności i niezawodności
description: Harmonogram wysokiej dostępności i niezawodności
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866093"
---
# <a name="scheduler-high-availability-and-reliability"></a>Harmonogram wysokiej dostępności i niezawodności
## <a name="azure-scheduler-high-availability"></a>Harmonogram systemu Azure wysokiej dostępności
Jako podstawowa usługi platformy Azure Harmonogram systemu Azure zapewnia wysoką dostępność i zawiera zarówno wdrożenia usługi geograficznie nadmiarowego i regionalnych geo zadania replikacji.

### <a name="geo-redundant-service-deployment"></a>Wdrożenie usługi geograficznie nadmiarowego
Harmonogram systemu Azure jest dostępna za pośrednictwem interfejsu użytkownika w niemal każdego regionu geograficznego, który jest obecnie na platformie Azure. Lista regionów, które Harmonogram systemu Azure jest dostępna w [wymienione w tym miejscu](https://azure.microsoft.com/regions/#services). Jeśli centrum danych w regionie hostowanej staje się niedostępny, możliwości trybu failover Harmonogram systemu Azure są taki sposób, że usługa jest dostępna z innej centrum danych.

### <a name="geo-regional-job-replication"></a>Replikacja geograficzna regionalne zadania
Nie tylko słowo frontonu, dostępne dla żądań zarządzania, ale własne stanowisko jest również replikacją geograficzną Harmonogram systemu Azure. Przypadku wystąpienia awarii w jeden region, harmonogram Azure awaryjnie i zapewnia, że zadanie jest uruchamiane z innym centrum danych w parach regionu geograficznego.

Na przykład jeśli po utworzeniu zadania w południowo-środkowe stany Harmonogram systemu Azure automatycznie replikuje tego zadania w północno-środkowe Stany. Jeśli wystąpi awaria w południowo-środkowe stany, harmonogram systemu Azure zapewnia, że zadanie jest uruchamiane z północno-środkowe Stany. 

![][1]

W związku z tym harmonogram systemu Azure zapewnia, że danych pozostaje w tym samym regionie geograficznym szerszych w razie awarii usługi Azure. W związku z tym nie wymagają zduplikowane zadanie tak, aby dodać wysokiej dostępności — Harmonogram systemu Azure automatycznie zapewnia funkcje wysokiej dostępności dla zadań.

## <a name="azure-scheduler-reliability"></a>Niezawodność Harmonogram systemu Azure
Harmonogram systemu Azure gwarancje własne wysokiej dostępności i mają inne podejście do zadań utworzonych przez użytkownika. Na przykład zadanie może wywoływać punktu końcowego HTTP, która jest niedostępna. Niemniej jednak Azure harmonogramu próbuje wykonać swoją pracę pomyślnie, przez alternatywnych opcjami na wypadek awarii. Harmonogram systemu Azure dzieje się tak na dwa sposoby:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Można skonfigurować zasady spróbuj ponownie za pomocą "retryPolicy"
Harmonogram systemu Azure pozwala skonfigurować zasady ponawiania. Domyślnie jeśli zadanie nie powiedzie się, harmonogram próbuje zadanie ponownie cztery razy więcej, w odstępach 30 sekund. Możesz ponownie skonfigurować tej zasady ponawiania być skuteczniejsze (na przykład dziesięć razy w odstępach 30 sekund) lub im (na przykład dwa razy codziennie.)

Jako przykład kiedy może to ułatwić może utworzyć zadania uruchamiane raz w tygodniu, który wywołuje punkt końcowy HTTP. Jeśli punkt końcowy HTTP działa przez kilka godzin po uruchomieniu zadania, może nie chcesz czekać tydzień więcej zadania uruchamiać ponownie, ponieważ nawet domyślne zasady ponawiania zakończy się niepowodzeniem. W takich przypadkach mogą ponownie skonfigurować zasady ponawiania standard, aby ponowić próbę co trzy godziny (na przykład) zamiast co 30 sekund.

Aby dowiedzieć się, jak skonfigurować zasady ponawiania, zajrzyj do [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Alternatywne konfigurowalne punktu końcowego za pośrednictwem "errorAction"
Jeśli punkt końcowy docelowego dla zadania harmonogramu Azure pozostaje jest nieosiągalny, harmonogram systemu Azure powraca do punkcie końcowym alternate obsługi błędów po wykonaniu jej zasady ponawiania. Jeśli skonfigurowano punkcie końcowym alternate obsługi błędów, harmonogram systemu Azure wywołuje go. Z punktem końcowym alternatywne własne zadania są wysokiej dostępności w wypadku awarii.

Na przykład w diagramie poniżej Harmonogram systemu Azure wynika, jego zasady ponawiania trafienie usługi sieci web w Nowym Jorku. Po awarii ponownych prób, sprawdza, czy jest alternatywny. Następnie przejdzie do przodu i uruchamia wysyłania żądań do alternatywnego z tej samej zasady ponawiania.

![][2]

Należy pamiętać, że te same zasady ponawiania dotyczą zarówno oryginalnej operacji, jak i akcja błędu alternatywny. Istnieje również możliwość Akcja alternatywny błędu akcji typ jest inny niż typ działania głównego akcji. Na przykład podczas działania głównego może wywołaniem punktu końcowego HTTP, akcja błędu zamiast tego można kolejki magazynu, kolejką usługi service bus lub Akcja temat magistrali usługi, który wykonuje rejestrowanie błędów.

Aby dowiedzieć się, jak skonfigurować alternatywne punktu końcowego, zapoznaj się [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Tworzenie złożonych harmonogramów i zaawansowanych cykli z użyciem usługi Azure Scheduler](scheduler-advanced-complexity.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
