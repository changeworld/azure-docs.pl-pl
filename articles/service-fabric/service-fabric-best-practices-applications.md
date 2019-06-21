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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203451"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Usługa Azure Service Fabric application najlepsze rozwiązania dotyczące projektowania

Ten artykuł zawiera najważniejsze wskazówki dotyczące tworzenia aplikacji i usług w usłudze Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Zapoznaj się z usługą Service Fabric
* Odczyt [więc chcesz dowiedzieć się więcej o usłudze Service Fabric?](service-fabric-content-roadmap.md) artykułu.
* Przeczytaj o [scenariuszy aplikacji usługi Service Fabric](service-fabric-application-scenarios.md).
* Omówienie opcji modelu programowania, zapoznając się [omówienie modelu programowania usługi Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Wskazówki dotyczące projektowania aplikacji
Zapoznanie się z [architektura ogólna](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikacji usługi Service Fabric i ich [zagadnienia dotyczące projektowania](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Wybierz bramę interfejsu API
Przy użyciu usługi bramy interfejsu API, który komunikuje się z usługami zaplecza, które następnie mogą być skalowana w poziomie. Najbardziej typowe usługi bramy interfejsu API, używane są:

- [Usługa Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), czyli [zintegrowane z usługą Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Usługa Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)przy użyciu [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) do odczytywania z partycji Centrum zdarzeń.
- [Træfik zwrotny serwer proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)przy użyciu [dostawcy usługi Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Usługa Azure Application Gateway nie jest bezpośrednio zintegrowana z usługą Service Fabric. Usługa Azure API Management jest zazwyczaj preferowanych przez.
- Własnych niestandardowych [platformy ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) bramy aplikacji sieci web.

### <a name="stateless-services"></a>Usługi bezstanowej
Zalecane jest zawsze zapoznanie się z tworzenie bezstanowych usług przy użyciu [usług Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) i przechowywanie stanu w bazie danych platformy Azure, usługi Azure Cosmos DB lub usługi Azure Storage. Stan zewnętrznych jest więcej na temat podejścia dla większości deweloperów. Takie podejście umożliwia również mogą korzystać z funkcji zapytanie w sklepie.  

### <a name="when-to-use-stateful-services"></a>Kiedy należy używać usług stanowych
Gdy wystąpić scenariusz, w celu uzyskania niskich opóźnień i muszą przechowywać dane blisko zasoby obliczeniowe, należy wziąć pod uwagę usług stanowych. Niektóre przykładowe scenariusze obejmują urządzenia cyfrową bliźniaczą IoT, stan gry, stanu sesji w pamięci podręcznej danych z bazy danych i długotrwałe przepływy pracy do śledzenia wywołań do innych usług.

Określić przedział czasu przechowywania danych:

- **Dane z pamięci podręcznej**. Używaj buforowania, gdy opóźnienie do zewnętrznych magazynów jest problemem. Usługa stanowa jako własnej pamięci podręcznej danych, lub rozważ zastosowanie [typu open-source SoCreate usługi Service Fabric rozproszonej pamięci podręcznej](https://github.com/SoCreate/service-fabric-distributed-cache). W tym scenariuszu nie musisz przejmować się jeśli utracisz wszystkie dane w pamięci podręcznej.
- **Dane czasowo**. W tym scenariuszu należy zachować dane blisko Ciebie do obliczenia na okres czasu opóźnienia, ale można pozwolić sobie na utratę danych w *po awarii*. Na przykład w przypadku wielu rozwiązań IoT, dane muszą znajdować się blisko zasobów obliczeniowych, takich jak podczas obliczania średniej temperatury w ciągu ostatnich kilku dni, ale jeśli te dane zostaną utracone, punktów danych specyficznych dla zarejestrowanych nie oznacza ważne. Ponadto w tym scenariuszu nie zazwyczaj dba o tworzenie kopii zapasowej poszczególnych punktów danych. Kopię zapasową można tylko obliczone wartości średnia, które są okresowo zapisywane do magazynu zewnętrznego.  
- **Długoterminowe dane**. Elementy Reliable collections trwałe przechowywanie danych. Ale w takim przypadku należy [przygotowywanie do odzyskiwania awaryjnego](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), w tym [Konfigurowanie okresowych zasady tworzenia kopii zapasowych](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) klastrów. W efekcie skonfigurujesz co się stanie, jeśli klaster zostanie zniszczony w katastrofie, gdy konieczne będzie utworzenie nowego klastra i jak wdrożyć nowe wystąpienia aplikacji i sprawności po najnowszej kopii zapasowej.

Zmniejsz koszty i zwiększyć dostępność:
- Można zmniejszyć koszty przy użyciu usługi stanowe, ponieważ nie powodują naliczania dostęp do danych i transakcji z magazynu zdalnego, a ponieważ nie musisz za pomocą innej usługi, takich jak pamięci podręcznej Azure redis Cache.
- Za pomocą usług stanowych głównie w celu przechowywania i nie obliczeń jest kosztowne, a nie zalecamy tego. Usługi stanowe należy traktować jako obliczeniowe przy użyciu tani Magazyn lokalny.
- Przez usunięcie zależności od innych usług, można podnieść poziomu dostępności usługi. Zarządzanie stanem przy użyciu wysokiej dostępności w klastrze izoluje możesz z innych przestoje usługi lub problemów z opóźnieniem.

## <a name="how-to-work-with-reliable-services"></a>Jak pracować z usług Reliable Services
Usług Reliable Services usługi Service Fabric umożliwia łatwe tworzenie usług stanowych i bezstanowych. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usług Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Zawsze przestrzegać [token anulowania](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) w `RunAsync()` metody dla usług stanowych i bezstanowych i `ChangeRole()` metodę dla usług stanowych. Jeśli nie, Usługa Service Fabric nie wie, usługa może zostać zamknięty. Na przykład jeśli nie uznaje token anulowania, może wystąpić znacznie dłuższy czas uaktualniania aplikacji.
-   Otwierających i zamykających [odbiorników komunikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) opóźnień z uwzględnieniem tokenów anulowania.
-   Nigdy nie łącz operacji synchronizacji kodu za pomocą kod asynchroniczny. Na przykład nie używaj `.GetAwaiter().GetResult()` w wywołania asynchronicznego. Użyj asynchronicznej *aż* stosu wywołań.

## <a name="how-to-work-with-reliable-actors"></a>Jak pracować z elementami Reliable Actors
Elementy Reliable Actors usługi Service Fabric umożliwia łatwe tworzenie stanowej, wirtualne aktorów. Aby uzyskać więcej informacji, zobacz [wprowadzenie do elementów Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Naszych użytkowników bardzo poważnie należy wziąć pod uwagę przy użyciu publikowania/subskrybowania komunikatów między usługi Reliable actors dla skalowania aplikacji. Narzędzia, które zapewniają tej usługi obejmują [typu open-source SoCreate usługi Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) i [usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Wprowadź stanu aktora jako [szczegółową możliwie](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Zarządzanie [cyklu życia aktora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Usuń aktorów, jeśli nie zamierzasz użyć ich ponownie. Usuwanie niepotrzebnych aktorów jest szczególnie ważne podczas korzystania z [dostawca stanu volatile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), ponieważ wszystkie stany są przechowywane w pamięci.
- Z powodu ich [współbieżności opartej na Włącz](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), aktorów najlepiej sprawdzają się jako niezależne obiekty. Nie twórz wykresy wielu aktora, synchroniczna metoda wywołuje metodę (każdy z nich, które są najbardziej prawdopodobne staje się wywołanie oddzielnej sieci) lub utworzyć żądania aktora cykliczne. Będą one znacznie wpływać na wydajność i skalę.
- Nie łącz operacji synchronizacji kodu za pomocą kod asynchroniczny. Użyj spójnie asynchronicznej, aby zapobiec problemom z wydajnością.
- Nie wprowadzaj długotrwałych wywołań w aktorów. Wywołania długotrwałych zablokuje innych wywołań do tych samych aktora, ze współbieżnością tury.
- Jeśli masz komunikuje się z innymi usługami przy użyciu [komunikacji zdalnej usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) i tworzysz `ServiceProxyFactory`, Utwórz fabrykę na [usługa aktora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) poziom i *nie* na poziomie aktora.


## <a name="application-diagnostics"></a>Usługa Application diagnostics
Być dokładnym o dodawaniu [rejestrowanie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) w wywołaniach usług. Ułatwi to zdiagnozować scenariusze, w których usługi wywołują się wzajemnie. Na przykład po wywołania wywołuje usługę B C D, wywołanie może nie działać wszędzie. Jeśli nie masz wystarczającej liczby rejestrowania błędów są trudne do diagnozowania. Jeśli usługi logowania są zbyt dużo ze względu na liczbę połączeń, pamiętaj, że co najmniej rejestrowanie błędów i ostrzeżeń.

## <a name="iot-and-messaging-applications"></a>IoT i aplikacji do obsługi komunikatów
Jeśli czytasz wiadomości z [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), użyj [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integruje się z usługą Service Fabric usług Reliable Services Aby zachować stan odczytu z Centrum zdarzeń dzieli na partycje i wypycha nowe komunikaty do usługi za pośrednictwem `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Wskazówki dotyczące projektowania na platformie Azure
* Odwiedź stronę [Centrum architektury Azure](https://docs.microsoft.com/azure/architecture/microservices/) dla wskazówki dotyczące projektowania w [tworzenia mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Odwiedź stronę [Rozpoczynanie pracy z platformą Azure dla gier](https://docs.microsoft.com/gaming/azure/) dla wskazówki dotyczące projektowania w [przy użyciu usługi Service Fabric w ramach usług gier](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
