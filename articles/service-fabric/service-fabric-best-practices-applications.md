---
title: Usługa Azure Service Fabric application najlepsze rozwiązania dotyczące projektowania | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące tworzenia aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237750"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Usługa Azure Service Fabric application najlepsze rozwiązania dotyczące projektowania

Ten artykuł zawiera najważniejsze wskazówki dotyczące tworzenia aplikacji i usług w usłudze Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Zapoznaj się z usługą Service Fabric
* [Dlatego potrzebujesz więcej informacji na temat usługi Service Fabric?](service-fabric-content-roadmap.md)
* Przeczytaj o [scenariuszy aplikacji usługi Service Fabric](service-fabric-application-scenarios.md)
* Następnie zrozumieć opcje modelu programowania przy użyciu [omówienie modelu programowania usługi Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Wskazówki dotyczące projektowania aplikacji
Zapoznanie się z [architektura ogólna](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikację usługi Service Fabric i jego [zagadnienia dotyczące projektowania](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Wybierz bramę interfejsu API
Przy użyciu usługi bramy interfejsu API, który komunikuje się z usługami zaplecza, które następnie mogą być skalowana w poziomie. Najbardziej typowe usługi bramy interfejsu API, używane są:

- [Usługa Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), czyli [zintegrowane z usługą Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Usługa Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) przy użyciu [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) do odczytywania z partycji Centrum zdarzeń
- [Træfik zwrotny serwer proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) przy użyciu [dostawcy usługi Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) Uwaga: to nie jest bezpośrednio zintegrowany z usługą Service Fabric i usługi Azure API Management jest zazwyczaj preferowanych przez
- Utwórz swoje własne [platformy ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) bramy aplikacji sieci web

### <a name="choose-stateless-services"></a>Wybieranie usługi bezstanowej
Zalecane jest zawsze zapoznanie się z kompilowania przy użyciu bezstanowej usługi [usług Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) przechowywania stanu w bazie danych Azure, Azure cosmos DB lub usługi Azure storage. Istnienie stanu zewnętrznych jest lepiej rozwiązaniem dla większości deweloperów i umożliwia również mogą korzystać z funkcji zapytanie w sklepie.  

### <a name="when-to-choose-stateful-services"></a>Kiedy należy wybrać usług stanowych
Gdy wystąpić scenariusz, w celu uzyskania niskich opóźnień i muszą przechowywać dane blisko zasoby obliczeniowe, należy wziąć pod uwagę usług stanowych. Przykłady: IoT cyfrową bliźniaczą urządzeń, stanu gry i stanu sesji, buforowanie danych z bazy danych i długotrwałe przepływy pracy służące do śledzenia wywołań do innych usług.

Wybierz przedział czasu przechowywania danych:

- Dane w pamięci podręcznej — używasz buforowania opóźnienia zewnętrzne magazyny w przypadku problemu. Użyj usługi stanowej, zgodnie z własnych danych w pamięci podręcznej, lub rozważ zastosowanie [open source SoCreate-Service fabric — rozproszonych — pamięć podręczna usługi](https://github.com/SoCreate/service-fabric-distributed-cache). W tym scenariuszu może spowodować utratę wszystkich danych w pamięci podręcznej i nie ma znaczenia.
- Dane czasowo — należy zachować dane blisko Ciebie do obliczenia opóźnienia w okresie czasu, ale dane akceptowalny jest utracone w *po awarii* scenariusza. Na przykład w przypadku wielu rozwiązań IoT, które dane mają być Zamknij, aby mocy obliczeniowej, na przykład obliczanie średniej temperatury z ostatnich kilku dni, jednak jeśli te dane zostaną utracone, następnie określonych danych wskazuje rejestrowane nie są to ważne. Również w tym scenariuszu nie są zwykle istotne o kopii zapasowej poszczególnych punktów danych, tylko z obliczone wartości średnie, które są okresowo zapisywane do magazynu zewnętrznego.  
- Długoterminowe dane — elementów Reliable collections przechowywanie danych trwałe. Jednak w takim przypadku należy [przygotowywanie do odzyskiwania awaryjnego](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) tym [Konfigurowanie okresowych zasady tworzenia kopii zapasowych](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) klastrów. W efekcie skonfigurujesz co się stanie, jeśli klaster jest niszczony, w przypadku awarii, gdy konieczne będzie utworzenie nowego klastra i jak wdrożyć nowe wystąpienia aplikacji i sprawności po najnowszej kopii zapasowej.

Zmniejsz koszty i zwiększyć dostępność:
- Możesz obniżyć koszty przy użyciu usługi stanowe, ponieważ nie są naliczane koszty dostępu i transakcji danych z magazynu zdalnego, a nie ma potrzeby się za pomocą innej usługi, takie jak usługi Azure Redis.
- Za pomocą usług stanowych głównie w celu przechowywania i nie obliczeń jest kosztowne, a nie jest zalecane. Jako obliczeniowe przy użyciu tanich magazynu lokalnego, należy wziąć pod uwagę usług stanowych.
- Przez usunięcie zależności od innych usług, można podnieść poziomu dostępności usługi. O stanie zarządzane o wysokiej dostępności w klastrze izoluje możesz z innych przestoje usługi lub problemów z opóźnieniem.

## <a name="how-to-properly-work-with-reliable-services"></a>Jak prawidłowo pracować przy użyciu usług Reliable Services
Usług Reliable Services umożliwiają łatwe tworzenie usług stanowych i bezstanowych. Odczyt [wprowadzenie do usług Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Zawsze przestrzegać [token anulowania](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) w `RunAsync()` metody dla usług stanowych i bezstanowych i `ChangeRole()` metodę dla usług stanowych. Bez tego usługi Service Fabric nie wiedzieć, jeśli usługa może zostać zamknięty. Na przykład nie zapewniane token anulowania może spowodować znacznie dłuższy czas uaktualniania aplikacji.
-   Otwierających i zamykających [odbiorników komunikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) w odpowiednim czasie odebranej z uwzględnieniem tokenów anulowania.
-   Nigdy nie łącz operacji synchronizacji kodu za pomocą kod asynchroniczny. Na przykład nie używaj `.GetAwaiter().GetResult()` wywołania asynchroniczne; musi być asynchroniczny *aż* stosu wywołań.

## <a name="how-to-properly-work-with-reliable-actors"></a>Jak prawidłowo pracować z elementami Reliable Actors
Elementy Reliable Actors umożliwia łatwe tworzenie stanowej, wirtualne aktorów. Odczyt [wprowadzenie do elementów Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Zdecydowanie należy wziąć pod uwagę przy użyciu publikowania/subskrybowania komunikatów między usługi Reliable actors dla skalowania aplikacji. Na przykład [open source SoCreate publikowania/subskrybowania](https://service-fabric-pub-sub.socreate.it/) lub [usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Wprowadź stanu aktora jako [szczegółową możliwie](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Zarządzanie [cyklu życia aktora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Usuń aktorów, jeśli nie zamierzasz kiedykolwiek ponownie ich użyć. Jest to szczególnie istotne, korzystając z [dostawcy VolatileState](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) jak wszystkie stany są przechowywane w pamięci.
- Ze względu na ich [współbieżności Włącz na podstawie](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) aktorów najlepiej sprawdzają się jako niezależne obiekty. Nie twórz wykresy wielu aktora, synchroniczna metoda wywołuje metodę (każdy z nich, które są najbardziej prawdopodobne staje się wywołanie oddzielnej sieci) lub żądania aktora cykliczne; będą one znacznie wpływać na wydajność i skalę.
- Nie można mieszać synchronizacji kodu za pomocą kod asynchroniczny; musi ona być async aż, aby zapobiec problemom z wydajnością.
- Nie wprowadzaj wywołania długo działające w aktorów, spowoduje to zablokowanie innych wywołań do tych samych aktora, ze współbieżnością tury.
- Jeśli podczas komunikowania się z innymi usługami przy użyciu [komunikacji zdalnej usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) , w przypadku tworzenia `ServiceProxyFactory`, następnie utwórz fabrykę na [usługa aktora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) poziom i *nie*na poziomie aktora.


## <a name="application-diagnostics"></a>Diagnostyka aplikacji
- Być dokładnym podczas dodawania [rejestrowanie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) w wywołaniach usług. Pomaga w diagnostyce scenariuszy, w którym usługi wywołują się wzajemnie. Na przykład, gdy element -> B -> C -> D wywołania może zakończyć się niepowodzeniem w dowolnym miejscu; Jeśli nie jest wystarczająco dużo rejestrowania, jest trudny do zdiagnozowania. Jeśli usługi logowania są zbyt dużo ze względu na liczbę połączeń, co najmniej należy rejestrować błędy i ostrzeżenia.

## <a name="iot-and-messaging-applications"></a>IoT i aplikacji do obsługi komunikatów
Podczas odczytywania komunikatów z [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) użyj [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) , integruje się z usługą Service Fabric usług Reliable Services do obsługi Stan odczytu z Centrum zdarzeń dzieli na partycje i wypycha nowe komunikaty do usługi za pośrednictwem `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Wskazówki dotyczące projektowania na platformie Azure
* Odwiedź stronę [Centrum architektury Azure](https://docs.microsoft.com/azure/architecture/microservices/) dla wskazówki dotyczące projektowania w [tworzenia mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Odwiedź stronę [Rozpoczynanie pracy z platformą Azure dla gier](https://docs.microsoft.com/gaming/azure/) dla wskazówki dotyczące projektowania w [przy użyciu usługi Service Fabric w ramach usług gier](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
