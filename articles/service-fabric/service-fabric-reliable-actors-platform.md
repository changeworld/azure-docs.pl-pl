---
title: Niezawodni aktorzy na sieci szkieletowej usług
description: W tym artykule opisano, jak niezawodne podmioty są warstwowe na niezawodne usługi i korzystać z funkcji platformy sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282304"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak niezawodni aktorzy korzystają z platformy sieci szkieletowej usług
W tym artykule wyjaśniono, jak niezawodne podmioty działają na platformie azure service fabric. Niezawodne aktorzy uruchomić w ramach, który jest obsługiwany w implementacji stanowej niezawodnej usługi o nazwie *usługi aktora*. Usługa aktora zawiera wszystkie składniki niezbędne do zarządzania cyklem życia i wysyłaniem komunikatów dla aktorów:

* Środowisko wykonawcze aktora zarządza cyklem życia, wyrzucanie elementów bezużytecznych i wymusza dostęp jednowątkowy.
* Odbiornik komunikacji zdalnej usługi aktora akceptuje wywołania dostępu zdalnego do aktorów i wysyła je do dyspozytora, aby przekierować do odpowiedniego wystąpienia aktora.
* Dostawca stanu aktora zawija dostawców stanu (takich jak dostawca stanu niezawodne kolekcje) i udostępnia kartę do zarządzania stanem aktora.

Te składniki razem tworzą platformę niezawodnego aktora.

## <a name="service-layering"></a>Nakładanie warstw usług
Ponieważ sama usługa aktora jest niezawodną usługą, wszystkie [modele aplikacji, cykl](service-fabric-application-model.md)życia, [pakowanie,](service-fabric-package-apps.md) [wdrażanie,](service-fabric-deploy-remove-applications.md)uaktualnianie i skalowanie koncepcji niezawodnych usług mają zastosowanie w ten sam sposób do usług aktora.

![Nakładanie warstw usługi aktora][1]

Na poprzednim diagramie przedstawiono relację między strukturami aplikacji sieci szkieletowej usług a kodem użytkownika. Niebieskie elementy reprezentują platformę aplikacji reliable services, orange reprezentuje platformę niezawodnego aktora, a zielony reprezentuje kod użytkownika.

W niezawodne usługi dziedziczy `StatefulService` klasy. Ta klasa jest sama `StatefulServiceBase` pochodzi `StatelessService` z (lub dla usług bezstanowych). W reliable actors użyjesz usługi aktora. Usługa aktora jest inna implementacja `StatefulServiceBase` klasy, która implementuje wzorzec aktora, gdzie aktorzy uruchomić. Ponieważ sama usługa aktora jest `StatefulServiceBase`tylko implementacją , można napisać `ActorService` własną usługę, która wywodzi się `StatefulService`z i implementować funkcje na poziomie usługi w taki sam sposób, jak podczas dziedziczenia, takich jak:

* Kopia zapasowa i przywracanie usługi.
* Udostępnione funkcje dla wszystkich podmiotów, na przykład wyłącznik.
* Procedura zdalna wywołuje samą usługę aktora i każdego aktora.

Aby uzyskać więcej informacji, zobacz [Implementowanie funkcji poziomu usług w usłudze aktora](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Model aplikacji
Usługi aktora są niezawodne usługi, więc model aplikacji jest taka sama. Jednak narzędzia kompilacji struktury aktora generują niektóre pliki modelu aplikacji dla Ciebie.

### <a name="service-manifest"></a>Manifest usługi
Narzędzia kompilacji struktury aktora automatycznie generują zawartość pliku ServiceManifest.xml usługi aktora. Ten plik zawiera:

* Typ usługi aktora. Nazwa typu jest generowana na podstawie nazwy projektu aktora. Na podstawie atrybutu trwałości na aktora HasPersistedState flagi jest również ustawiona odpowiednio.
* Pakiet kodu.
* Pakiet konfiguracyjny.
* Zasoby i punkty końcowe.

### <a name="application-manifest"></a>Manifest aplikacji
Narzędzia kompilacji struktury aktora automatycznie tworzą domyślną definicję usługi dla usługi aktora. Narzędzia kompilacji wypełniają domyślne właściwości usługi:

* Liczba zestawów replik jest określana przez atrybut trwałości na aktora. Za każdym razem, gdy atrybut trwałości w aktora zostanie zmieniony, liczba zestawów replik w domyślnej definicji usługi jest odpowiednio resetowana.
* Schemat partycji i zakres są ustawione na Uniform Int64 z pełnym zakresem klawiszy Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Koncepcje partycji sieci szkieletowej usług dla aktorów
Usługi aktora są usługi stanowe podzielone na partycje. Każda partycja usługi aktora zawiera zestaw aktorów. Partycje usługi są automatycznie dystrybuowane przez wiele węzłów w sieci szkieletowej usług. W rezultacie wystąpienia aktora są dystrybuowane.

![Partycjonowanie i dystrybucja aktora][5]

Niezawodne usługi mogą być tworzone przy różnych schematach partycji i zakresach kluczy partycji. Usługa aktora używa schematu partycjonowania Int64 z pełnym zakresem kluczy Int64 do mapowania aktorów na partycje.

### <a name="actor-id"></a>Identyfikator aktora
Każdy aktor, który jest tworzony w usłudze ma unikatowy identyfikator `ActorId` skojarzony z nim, reprezentowane przez klasę. `ActorId`jest nieprzezroczystą wartością identyfikatora, która może służyć do jednolitego rozkładu podmiotów w partycjach usługi przez generowanie losowych identyfikatorów:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Każdy `ActorId` jest hashed do Int64. Dlatego usługa aktora musi używać schematu partycjonowania Int64 z pełnym zakresem klawiszy Int64. Jednak niestandardowe wartości identyfikatorów mogą `ActorID`być używane dla , w tym identyfikatory GUID/UUID, ciągi i Int64s.

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

Gdy używasz identyfikatorów GUID/UUID i ciągów, wartości są mieszane do Int64. Jednak, gdy jesteś jawnie dostarczanie Int64 `ActorId`do , Int64 będzie mapować bezpośrednio do partycji bez dalszego mieszania. Za pomocą tej techniki można kontrolować partycję, w której są umieszczane podmioty.


## <a name="next-steps"></a>Następne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Cykl życia aktora i wyrzucanie elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API podmiotów](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
