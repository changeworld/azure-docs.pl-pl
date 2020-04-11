---
title: Ograniczenie stawek dla wiadomości SMS, e-maili, powiadomień push
description: Dowiedz się, jak platforma Azure ogranicza liczbę możliwych powiadomień SMS, e-mail, push aplikacji Azure lub webhook z grupy akcji.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114328"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Ograniczenie szybkości dla poczty głosowej, WIADOMOŚCI SMS, wiadomości e-mail, powiadomień push w aplikacji Azure i postów w interfejsie webhook
Ograniczenie szybkości to zawieszenie powiadomień, które pojawiają się, gdy zbyt wiele osób jest wysyłanych na określony numer telefonu, adres e-mail lub urządzenie. Ograniczenie szybkości zapewnia, że alerty są łatwe do opanowania i zasysania.

Progi limitu stawek to:

- **SMS**: Nie więcej niż 1 SMS co 5 minut.
- **Głos:** Nie więcej niż 1 połączenie głosowe co 5 minut.
- **E-mail:** Nie więcej niż 100 e-maili w ciągu godziny.
 
  Inne działania nie są ograniczone.

## <a name="rate-limit-rules"></a>Reguły limitu stawek
- Określony numer telefonu lub adres e-mail jest ograniczony, gdy odbiera więcej wiadomości niż pozwala na to próg.
- Numer telefonu lub adres e-mail może być częścią grup akcji w wielu subskrypcjach. Ograniczenie stawek ma zastosowanie we wszystkich subskrypcjach. Ma ona zastosowanie natychmiast po osiągnięciu progu, nawet jeśli wiadomości są wysyłane z wielu subskrypcji.
- Gdy cena adresu e-mail jest ograniczona, wysyłane jest dodatkowe powiadomienie w celu poinformowania o ograniczeniu szybkości. Wiadomość e-mail określa, kiedy wygasa ograniczenie stawki.

## <a name="next-steps"></a>Następne kroki ##
* Dowiedz się więcej o [zachowaniu alertów SMS](alerts-sms-behavior.md).
* Zapoznaj się [z omówieniem alertów dziennika aktywności](alerts-overview.md)i dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [konfigurować alerty przy każdym opublikowaniu powiadomienia o kondycji usługi.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)

