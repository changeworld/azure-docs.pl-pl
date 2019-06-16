---
title: Oceń, ograniczanie dla wiadomości SMS, wiadomości e-mail, powiadomień wypychanych w aplikacji platformy Azure i elementy webhook
description: Dowiedz się, jak Azure ogranicza liczbę możliwych wiadomości SMS, wiadomości e-mail, powiadomienia wypychane lub elementu webhook aplikacji platformy Azure z grupy akcji.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 11fd6a2c58671cc5d0bcf0593239eb9e62aca834
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60346652"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Oceń, ograniczając połączeń głosowych, wiadomości SMS, wiadomości e-mail, powiadomień wypychanych w aplikacji platformy Azure i elementu webhook wpisów
Ograniczanie szybkości jest zawieszenia powiadomienia, który występuje, gdy za dużo są wysyłane do konkretny numer telefonu, adres e-mail lub urządzenia. Ograniczanie szybkości gwarantuje, że alerty są łatwe w obsłudze i możliwością wykonywania akcji.

Progi limit szybkości są:

- **SMS**: Nie więcej niż 1 SMS co 5 minut.
- **Głos**: Nie więcej niż 1 połączenie głosowe co 5 minut.
- **adres e-mail**: Nie więcej niż 100 wiadomości e-mail w ciągu godziny.
 
  Inne akcje nie są częstość jest ograniczona.

## <a name="rate-limit-rules"></a>Zasady limitu szybkości
- Konkretny numer telefonu lub adres e-mail jest prędkość ograniczoną po odebraniu komunikatów jest większa niż zezwala na wartość progową.
- Numer telefonu lub adresu e-mail może być częścią grupy akcji w wielu subskrypcjach. Ograniczanie szybkości mają zastosowanie we wszystkich subskrypcjach. Ma to zastosowanie zaraz po osiągnięciu progu nawet wtedy, gdy komunikaty są wysyłane z wieloma subskrypcjami.
- Gdy współczynnik ograniczony jest adres e-mail, dodatkowe powiadomienia są wysyłane do komunikacji, ograniczania szybkości. Stany poczty e-mail, po wygaśnięciu ograniczanie szybkości.

## <a name="next-steps"></a>Kolejne kroki ##
* Dowiedz się więcej o [SMS alert zachowanie](alerts-sms-behavior.md).
* Pobierz [Przegląd alertów dziennika aktywności](alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

