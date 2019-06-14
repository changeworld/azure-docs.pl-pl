---
title: Rozwiązywanie problemów z błędami AMQP w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Zawiera listę błędów protokołu AMQP może odbierać za pomocą usługi Azure Service Bus i spowodować, że z tych błędów.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402789"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Błędy protokołu AMQP w usłudze Azure Service Bus
Ten artykuł zawiera niektóre błędy, które otrzymujesz, korzystając z protokołu AMQP z usługą Azure Service Bus. Są one wszystkie standardowe zachowania usługi. Możesz uniknąć ich przez wywołań wysyłania i odbierania na połączenie/łącza, które automatycznie odtworzy połączenie.

## <a name="link-is-closed"></a>Łącze jest zamknięty. 
Zostanie wyświetlony następujący błąd, gdy połączenie AMQP i łącza są aktywne, ale żadne wywołania (na przykład wysłać lub odebrać) są wykonywane przy użyciu linku na 10 minut. Tak łącze jest zamknięty. Połączenie jest wciąż otwarty.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Połączenie zostało zamknięte
Zostanie wyświetlony następujący błąd połączenia AMQP, gdy wszystkie linki w połączeniu zostały zamknięte, ponieważ nie było żadnych działań (bezczynny), a nie został utworzony nowy link w ciągu 5 minut.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link nie zostanie utworzony. 
Zostanie wyświetlony ten błąd, gdy tworzone jest nowe połączenie protokołu AMQP, ale link nie jest tworzony w ciągu 1 minuty tworzenia połączenia protokołu AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołów AMQP oraz usługi Service Bus, skorzystaj z następujących linków:

* [Omówienie AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[Omówienie AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
