---
title: Oceń ograniczanie dla programu SMS, wiadomości e-mail, powiadomień wypychanych w aplikacji platformy Azure i elementów webhook
description: Dowiedz się, jak Azure ogranicza liczbę możliwych programu SMS, wiadomości e-mail, powiadomień wypychanych lub elementu webhook aplikacji Azure z grupy akcji.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 1acea47638c75971bad62f28dcd7e96823d84c1d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262960"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Oceń ograniczanie dla głosu, programu SMS, wiadomości e-mail, powiadomień wypychanych w aplikacji platformy Azure i elementu webhook wpisów
Limitów szybkości jest zawieszenie powiadomienia, gdy zbyt wiele są wysyłane do konkretny numer telefonu, adres e-mail lub urządzenia. Limitów szybkości zapewnia, że alerty są łatwe w zarządzaniu i możliwością wykonania akcji.

Progi limit szybkości są:

 - **SMS**: nie więcej niż 1 SMS co 5 minut.
 - **Głos**: głosu nie więcej niż 1 wywołać co 5 minut.
 - **Wiadomości e-mail**: nie więcej niż 100 wiadomości e-mail w ciągu godziny.
 
 Inne akcje nie są ograniczone szybkości.

## <a name="rate-limit-rules"></a>Reguły limit szybkości
- Numer telefonu lub adres e-mail jest szybkość ograniczone, gdy odbiera komunikaty więcej niż próg.
- Numer telefonu lub adres e-mail może być częścią grupy akcji na wiele subskrypcji. Stosuje limitów szybkości dla wszystkich subskrypcji. Ma to zastosowanie zaraz po osiągnięciu progu, nawet jeśli komunikaty są wysyłane z wieloma subskrypcjami.
- Jeśli adres e-mail jest ograniczona, dodatkowe powiadomienie jest wysyłane do komunikowania się limitów szybkości. Stany poczty e-mail, po wygaśnięciu limitów szybkości.

## <a name="next-steps"></a>Następne kroki ##
* Dowiedz się więcej o [SMS alertów zachowanie](monitoring-sms-alert-behavior.md).
* Pobierz [Przegląd alertów dotyczących działań w dzienniku](monitoring-overview-alerts.md)i dowiedzieć się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty, gdy powiadomienie usługi kondycji jest przesyłana](monitoring-activity-log-alerts-on-service-notifications.md).
