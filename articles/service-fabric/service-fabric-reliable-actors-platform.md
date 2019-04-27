---
title: Elementy Reliable Actors w usłudze Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak elementy Reliable Actors przedstawiono nałożone na usług Reliable Services i korzystać z funkcji platformy usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: bc7569c9f230abb7677a8df9fc0cc0268e57296f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725927"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak elementy Reliable Actors korzystają z platformy usługi Service Fabric
W tym artykule wyjaśniono, jak elementy Reliable Actors działają na platformie Azure Service Fabric. Elementy Reliable Actors, uruchom w strukturę, która jest hostowana w implementacji stanowej usługi reliable service o nazwie *usługa aktora*. Usługa aktora zawiera wszystkie składniki niezbędne do zarządzania cyklem życia i wiadomości, wysyła do usługi aktorów:

* W środowisku uruchomieniowym aktora zarządza cyklem życia, wyrzucanie elementów bezużytecznych i wymusza jednowątkowe dostęp.
* Odbiornik komunikacji zdalnej usługi aktora akceptuje dostępu zdalnego wywołania aktorów i wysyła je do dyspozytora kierować do wystąpienia aktora odpowiednie.
* Dostawca stanu aktora otacza dostawców stanu (np. dostawca stanu elementów Reliable Collections) i udostępnia adapter dla zarządzania stanem aktora.

Te składniki tworzą razem framework Reliable Actor.

## <a name="service-layering"></a>Warstw usługi
Ponieważ sama usługa aktora usługi reliable service wszystkich [model aplikacji](service-fabric-application-model.md), cykl życia, [pakowania](service-fabric-package-apps.md), [wdrożenia](service-fabric-deploy-remove-applications.md), uaktualnianie i skalowanie pojęcia niezawodna Usługi zastosować ten sam sposób, aby usługi aktora.

![Warstw usługi aktora][1]

Na powyższym diagramie przedstawiono relację między platformami aplikacji usługi Service Fabric i kod użytkownika. Niebieskie elementy reprezentują struktury aplikacji usług Reliable Services, orange reprezentuje framework Reliable Actor i zielony reprezentuje kod użytkownika.

Usług Reliable Services, usługi dziedziczy `StatefulService` klasy. Ta klasa jest pochodną `StatefulServiceBase` (lub `StatelessService` przypadku usług bezstanowych). W elementach Reliable Actors możesz korzystać z usługi aktora. Usługa aktora jest z inną implementacją `StatefulServiceBase` klasę, która implementuje wzorzec aktora, w którym są uruchamiane usługi aktorów. Ponieważ sama usługa aktora jest po prostu implementacją `StatefulServiceBase`, można napisać własne usługa, która jest pochodną `ActorService` i zaimplementuj funkcje na poziomie usługi tak samo jak w przypadku dziedziczenia `StatefulService`, takich jak:

* Usługa tworzenia kopii zapasowych i przywracania.
* Udostępnione funkcje dla wszystkich podmiotów, na przykład wyłącznika.
* Zdalne wywołania procedur na samą usługę aktora i każdego poszczególnych aktora.

Aby uzyskać więcej informacji, zobacz [Implementowanie funkcji na poziomie usługi w usłudze aktora](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Model aplikacji
Usług aktora są usług Reliable Services, dzięki czemu model aplikacji jest taki sam. Jednak narzędzia do kompilacji framework aktora wygenerować niektóre z plików modelu aplikacji za Ciebie.

### <a name="service-manifest"></a>Manifest usługi
Narzędzia kompilacji aktora framework automatycznie generuje zawartość pliku ServiceManifest.xml usługi aktora. Ten plik zawiera:

* Typ usługi aktora. Nazwa typu jest generowany na podstawie nazwy projekt usługi aktora. Na podstawie stanu trwałego atrybutu na usługi aktora, HasPersistedState flaga jest również ustawiona odpowiednio.
* Pakiet kodu.
* Pakiet konfiguracji.
* Zasoby i punktów końcowych.

### <a name="application-manifest"></a>Manifest aplikacji
Narzędzia do kompilacji framework aktora automatycznie tworzyć definicję usługi domyślne dla usługi aktora. Narzędzia do kompilacji wypełnić właściwości usługi domyślne:

* Liczba zestawu replik jest określana przez atrybut utrzymywania na usługi aktora. Każdorazowo, gdy atrybut utrzymywania na usługi aktora ulegnie zmianie, liczba zestawu replik w definicji usługi domyślne jest resetowany odpowiednio.
* Schemat partycji oraz zakres są ustawione na jednolitych Int64 dla pełnego zakresu kluczy Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Usługa Service Fabric partycji pojęcia związane z aktorów
Usługi aktora są podzielone na partycje usług stanowych. Każda partycja usługi aktora zawiera zestaw podmiotów. Partycje usługi są automatycznie rozpraszane w wielu węzłach w usłudze Service Fabric. Wystąpienia aktora są dystrybuowane w wyniku.

![Partycjonowanie aktora i dystrybucji][5]

Usług Reliable Services mogą być tworzone za pomocą zakresów kluczy partycji i schematy partycjonowania. Usługa aktora używa schematu partycjonowania Int64 z pełnego zakresu kluczy Int64 do mapowania partycje aktorów.

### <a name="actor-id"></a>Identyfikator aktora
Każdego aktora, który jest tworzony w usłudze ma unikatowy identyfikator skojarzony z nim, reprezentowane przez `ActorId` klasy. `ActorId` jest nieprzezroczysta wartość Identyfikatora, który może służyć równomiernego rozkładu aktorów partycjach usługi przez Generowanie losowe identyfikatory:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Każdy `ActorId` wyznaczany jest skrót Int64. Jest to, dlaczego usługa aktora musi używać schematu partycjonowania Int64 dla pełnego zakresu kluczy Int64. Jednak wartości Identyfikatora niestandardowe mogą służyć do `ActorID`, w tym identyfikatory GUID/UUID, ciągi i Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Gdy używasz GUID/UUID i ciągi, wartości są przekazywane do Int64. Jednak gdy jawnie udostępniasz Int64 do `ActorId`, Int64 będzie mapować bezpośrednio do partycji bez dalszego wyznaczania wartości skrótu. Można użyć tej techniki wysyłania do sterowania partycję Aktorzy są umieszczane w.


## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Kolekcja aktora cykl życia i odzyskiwanie](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API aktorów](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Przykładowy kod .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowego kodu Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
