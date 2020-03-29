---
title: Rozwiązywanie problemów z błędami usługi AMQP w usłudze Azure Service Bus | Dokumenty firmy Microsoft
description: Zawiera listę błędów AMQP, które mogą pojawić się podczas korzystania z usługi Azure Service Bus i przyczyny tych błędów.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402789"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Błędy usługi AMQP w usłudze Azure Service Bus
Ten artykuł zawiera niektóre błędy, które są otrzymywane podczas korzystania z usługi AMQP z usługą Azure Service Bus. Wszystkie są standardowymi zachowaniami usługi. Można ich uniknąć, wykonując połączenia z wysyłaniem/odbieraniem połączeń na połączenie/łącze, które automatycznie odtwarza połączenie/łącze.

## <a name="link-is-closed"></a>Łącze jest zamknięte 
Widzisz następujący błąd, gdy połączenie USŁUGI AMQP i łącze są aktywne, ale żadne połączenia (na przykład wysyłanie lub odbieranie) są nawiązywanych przy użyciu łącza przez 10 minut. Tak więc link jest zamknięty. Połączenie jest nadal otwarte.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Połączenie jest zamknięte
Podczas zamknięcia wszystkich łączy w połączeniu, ponieważ nie było żadnych działań (bezczynnie), pojawia się następujący błąd, a nowe łącze nie zostało utworzone w ciągu 5 minut.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Łącze nie jest tworzone 
Ten błąd jest widoczny po utworzeniu nowego połączenia PROTOKOŁU AMQP, ale łącze nie zostanie utworzone w ciągu 1 minuty od utworzenia połączenia USŁUGI AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o amqp i usługi Service Bus, odwiedź następujące łącza:

* [Omówienie usługi AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [Usługa AMQP w magistrali usług dla systemu Windows Server]

[Omówienie usługi AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Usługa AMQP w magistrali usług dla systemu Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
