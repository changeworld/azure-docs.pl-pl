---
title: Najważniejsze wskazówki dotyczące projektowania aplikacji usługi Azure Service Fabric
description: Najważniejsze wskazówki i zagadnienia dotyczące projektowania dotyczące tworzenia aplikacji i usług przy użyciu sieci szkieletowej usług Azure.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991658"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Najważniejsze wskazówki dotyczące projektowania aplikacji usługi Azure Service Fabric

Ten artykuł zawiera wskazówki dotyczące najlepszych rozwiązań dotyczących tworzenia aplikacji i usług w sieci szkieletowej usług Azure.
 
## <a name="get-familiar-with-service-fabric"></a>Zapoznaj się z siecią szkieletową usług
* Przeczytaj artykuł [Więc chcesz dowiedzieć się więcej o sieci szkieletowej usług?](service-fabric-content-roadmap.md)
* Przeczytaj o [scenariuszach aplikacji sieci szkieletowej usług](service-fabric-application-scenarios.md).
* Poznaj wybór modelu programowania, czytając [omówienie modelu programowania sieci szkieletowej usług](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Wskazówki dotyczące projektowania aplikacji
Zapoznaj się z [ogólną architekturą](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikacji sieci szkieletowej usług i ich [zagadnieniami projektowymi.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Wybieranie bramy interfejsu API
Użyj usługi bramy interfejsu API, która komunikuje się z usługami zaplecza, które następnie mogą być skalowane w poziomie. Najczęściej używane usługi bramy interfejsu API to:

- [Usługa Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), która jest [zintegrowana z siecią szkieletową usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Usługa Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub [Usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), przy użyciu [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) do odczytu z partycji Centrum zdarzeń.
- [Træfik odwrotnego serwera proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), przy użyciu [dostawcy usługi Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Brama aplikacji platformy Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Usługa Azure Application Gateway nie jest bezpośrednio zintegrowana z siecią szkieletową usług. Usługa Azure API Management jest zazwyczaj preferowanym wyborem.
- Twoja własna, specjalnie zbudowana [ASP.NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) podstawowa brama aplikacji sieci web.

### <a name="stateless-services"></a>Usługi bezstanowe
Zaleca się, aby zawsze rozpocząć od tworzenia usług bezstanowych przy użyciu [niezawodnych usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) i przechowywania stanu w bazie danych platformy Azure, usługi Azure Cosmos DB lub usługi Azure Storage. Stan zewnętrzny jest bardziej znane podejście dla większości deweloperów. Takie podejście umożliwia również korzystanie z możliwości zapytań w magazynie.  

### <a name="when-to-use-stateful-services"></a>Kiedy korzystać z usług stanowych
Należy wziąć pod uwagę usługi stanowe, gdy masz scenariusz dla małych opóźnień i trzeba zachować dane blisko obliczeń. Niektóre przykładowe scenariusze obejmują cyfrowe urządzenia bliźniaczej reprezentacji IoT, stan gry, stan sesji, buforowanie danych z bazy danych i długotrwałe przepływy pracy do śledzenia wywołań do innych usług.

Zdecyduj o przedziale czasu przechowywania danych:

- **Dane w pamięci podręcznej**. Użyj buforowania, gdy opóźnienie do magazynów zewnętrznych jest problemem. Użyj usługi stanowej jako własnej pamięci podręcznej danych lub rozważ użycie [rozproszonej pamięci podręcznej sieci szkieletowej sieci szkieletowej usługi typu open source.](https://github.com/SoCreate/service-fabric-distributed-cache) W tym scenariuszu nie trzeba się martwić, jeśli utracisz wszystkie dane w pamięci podręcznej.
- **Dane związane z czasem**. W tym scenariuszu należy zachować dane blisko obliczeń przez okres czasu opóźnienia, ale można sobie pozwolić na utratę danych w *przypadku awarii*. Na przykład w wielu rozwiązaniach IoT dane muszą być zbliżone do obliczeń, na przykład podczas obliczania średniej temperatury w ciągu ostatnich kilku dni, ale jeśli te dane zostaną utracone, zarejestrowane określone punkty danych nie są tak ważne. Ponadto w tym scenariuszu zazwyczaj nie dbają o tworzenie kopii zapasowych poszczególnych punktów danych. Kopii zapasowej tylko obliczone wartości średnie, które są okresowo zapisywane do magazynu zewnętrznego.  
- **Dane długoterminowe**. Niezawodne kolekcje mogą przechowywać twoje dane na stałe. Ale w tym przypadku należy przygotować się do [odzyskiwania po awarii,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)w tym [konfigurowanie okresowych zasad tworzenia kopii zapasowych](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) dla klastrów. W efekcie można skonfigurować, co się stanie, jeśli klaster zostanie zniszczony w przypadku awarii, gdzie trzeba utworzyć nowy klaster i jak wdrożyć nowe wystąpienia aplikacji i odzyskać z najnowszej kopii zapasowej.

Zaoszczędź koszty i zwiększ dostępność:
- Możesz obniżyć koszty przy użyciu usług stanowych, ponieważ nie ponosisz kosztów dostępu do danych i transakcji z magazynu zdalnego i ponieważ nie trzeba używać innej usługi, takiej jak Usługa Azure Cache for Redis.
- Korzystanie z usług stanowych głównie do przechowywania, a nie do obliczeń jest kosztowne i nie zalecamy go. Pomyśl o stanowych usługach jako o obliczeniach z tanim magazynem lokalnym.
- Usuwając zależności od innych usług, można poprawić dostępność usługi. Zarządzanie stanem za pomocą usługi ha w klastrze izoluje cię od innych przestojów lub problemów z opóźnieniami usługi.

## <a name="how-to-work-with-reliable-services"></a>Jak pracować z niezawodnymi usługami
Usługa Fabric Reliable Services umożliwia łatwe tworzenie usług bezstanowych i stanowych. Aby uzyskać więcej informacji, zobacz [wprowadzenie do reliable services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Zawsze honor [token anulowania](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) w metodzie `RunAsync()` dla usług `ChangeRole()` bezstanowych i stanowych i metody dla usług stanowych. Jeśli nie, sieci szkieletowej usług nie wie, czy usługa może być zamknięta. Na przykład jeśli nie honorujesz tokenu anulowania, może wystąpić znacznie dłuższy czas uaktualniania aplikacji.
-    Otwórz i zamknij [słuchaczy komunikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) w odpowiednim czasie i honor tokeny anulowania.
-    Nigdy nie mieszaj kodu synchronizacji z kodem asynchroniowym. Na przykład nie należy `.GetAwaiter().GetResult()` używać w wywołaniach asynchronii. Użyj asynchronii *przez całą drogę* przez stos wywołań.

## <a name="how-to-work-with-reliable-actors"></a>Jak pracować z wiarygodnymi aktorami
Usługa Fabric Reliable Actors umożliwia łatwe tworzenie stanowych, wirtualnych aktorów. Aby uzyskać więcej informacji, zobacz [wprowadzenie do reliable actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Poważnie rozważyć użycie pub/sub wiadomości między podmiotami do skalowania aplikacji. Narzędzia, które zapewniają tę usługę obejmują [open-source SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) i [Azure Service Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Spraw, aby stan aktora był jak [najbardziej szczegółowy.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- Zarządzanie [cyklem życia aktora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Usuń aktorów, jeśli nie zamierzasz ich ponownie używać. Usuwanie niepotrzebnych aktorów jest szczególnie ważne, gdy używasz [dostawcy stanu nietrwałego,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)ponieważ cały stan jest przechowywany w pamięci.
- Ze względu na ich [koncepcjonacjonalność,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)aktorzy są najlepiej używane jako niezależne obiekty. Nie należy tworzyć wykresów wieloaktorowych, synchronicznych wywołań metody (z których każdy najprawdopodobniej staje się oddzielne wywołanie sieciowe) lub tworzenie cyklicznych żądań aktora. Będą one znacząco wpływać na wydajność i skalę.
- Nie mieszaj kodu synchronizacji z kodem asynchroniowym. Użyj asynchronii konsekwentnie, aby zapobiec problemom z wydajnością.
- Nie wykonuj długotrwałych połączeń u aktorów. Długotrwałe wywołania będą blokować inne wywołania do tego samego aktora, ze względu na współbieżność turową.
- Jeśli komunikujesz się z innymi usługami przy użyciu [komunikacji zdalnej sieci szkieletowej sieci szkieletowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) usług i tworzysz , utwórz `ServiceProxyFactory`fabrykę na poziomie [usługi aktora,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) a *nie* na poziomie aktora.


## <a name="application-diagnostics"></a>Diagnostyka aplikacji
Należy dokładnie dodawać [logowania aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) w wywołaniach usługi. Pomoże to zdiagnozować scenariusze, w których usługi dzwonią do siebie nawzajem. Na przykład, gdy A wywołuje B wywołuje C wywołuje D, wywołanie może zakończyć się niepowodzeniem w dowolnym miejscu. Jeśli nie masz wystarczającej liczby rejestrowania, błędy są trudne do zdiagnozowania. Jeśli usługi są rejestrowanie zbyt wiele z powodu woluminów wywołań, należy co najmniej dziennika błędów i ostrzeżeń.

## <a name="iot-and-messaging-applications"></a>IoT i aplikacje do obsługi wiadomości
Podczas odczytywania wiadomości z [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) lub Usługi Azure Event [Hubs](https://docs.microsoft.com/azure/event-hubs/)należy użyć [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor integruje się z usługami niezawodne usługi sieci szkieletowej usług, aby zachować `IEventProcessor::ProcessEventsAsync()` stan odczytu z partycji centrum zdarzeń i wypycha nowe komunikaty do usług za pomocą metody.


## <a name="design-guidance-on-azure"></a>Wskazówki dotyczące projektowania na platformie Azure
* Odwiedź [centrum architektury platformy Azure,](https://docs.microsoft.com/azure/architecture/microservices/) aby uzyskać wskazówki dotyczące projektowania [dotyczące tworzenia mikrousług na platformie Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

* Odwiedź [witrynę Wprowadzenie do platformy Azure dla gier,](https://docs.microsoft.com/gaming/azure/) aby uzyskać wskazówki dotyczące projektowania [dotyczące korzystania z sieci szkieletowej usług w usługach gier.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
