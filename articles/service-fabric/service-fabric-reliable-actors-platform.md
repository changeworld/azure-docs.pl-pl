---
title: Reliable Actors w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Opisuje sposób Reliable Actors są warstwowy na niezawodne usługi i korzystać z funkcji platformy sieć szkieletowa usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: b2369f9468c54f10d01203841b6d7ba44b7ba2de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak elementy Reliable Actors korzystają z platformy usługi Service Fabric
W tym artykule opisano, jak Reliable Actors działają na platformie Azure Service Fabric. Wywołuje Reliable Actors uruchomienia w ramach obsługiwanej w implementacji usługi stanowej niezawodnej *usługi aktora*. Usługa aktora zawiera składniki niezbędne do zarządzania cyklem życia i komunikat podczas wysyłania do sieci złośliwych użytkowników:

* Środowisko uruchomieniowe aktora zarządza cykl życia, wyrzucanie elementów bezużytecznych i wymusza jednowątkowe dostęp.
* Odbiornik komunikacji zdalnej usługi aktora akceptuje wywołań złośliwych użytkowników dostępu zdalnego i wysyła je do dyspozytora rozesłać do wystąpienia odpowiednie aktora.
* Dostawca stanu aktora opakowuje dostawców stanu (na przykład dostawcy stanu niezawodnej kolekcje) i udostępnia adapter dla zarządzania stanem aktora.

Te składniki tworzą razem framework niezawodnego aktora.

## <a name="service-layering"></a>Tworzenie warstw usługi
Ponieważ sama usługa aktora jest niezawodnej usługi, wszystkie [model aplikacji](service-fabric-application-model.md), cykl życia, [pakowania](service-fabric-package-apps.md), [wdrożenia](service-fabric-deploy-remove-applications.md), uaktualnianie i skalowanie pojęcia niezawodne usługi Zastosuj ten sam sposób, aby usługi aktora.

![Tworzenie warstw usługi aktora][1]

Na powyższym diagramie przedstawiono związek między struktur aplikacji usługi Service Fabric i kod użytkownika. Niebieski elementy reprezentują usługi niezawodnej struktury aplikacji, orange reprezentuje framework niezawodnego aktora i zielony reprezentuje kod użytkownika.

W usługach niezawodnej usługi dziedziczy `StatefulService` klasy. Ta klasa jest pochodną `StatefulServiceBase` (lub `StatelessService` usług bezstanowych). W Reliable Actors możesz użyć usługi aktora. Usługa aktora jest różne implementacje `StatefulServiceBase` klasy, która implementuje wzorca aktora, gdzie uruchomić sieci złośliwych użytkowników. Ponieważ sama usługa aktora jest po prostu implementacją `StatefulServiceBase`, można napisać własne usługi, która pochodzi z `ActorService` i implementuje funkcje na poziomie usługi tak samo jak w przypadku dziedziczenia `StatefulService`, takich jak:

* Usługa tworzenia kopii zapasowej i przywracania.
* Udostępnione funkcji dla wszystkich podmiotów, na przykład wyłącznika.
* Zdalne wywoływanie procedur w samej usługi aktora i na każdym poszczególnych aktora.

> [!NOTE]
> Stanowe usług nie są obecnie obsługiwane w języku Java/Linux.

Aby uzyskać więcej informacji, zobacz [implementowania funkcji poziomu usług w usłudze aktora](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Model aplikacji
Usługi aktora są niezawodne usługi, więc model aplikacji jest taka sama. Jednak narzędzi kompilacji framework aktora Generowanie niektórych plików modelu aplikacji za Ciebie.

### <a name="service-manifest"></a>Manifest usługi
Narzędzia kompilacji framework aktora automatycznie generować zawartość pliku ServiceManifest.xml usługi aktora. Ten plik zawiera:

* Typ usługi aktora. Nazwa typu jest generowany na podstawie nazwy projektu z aktora. Na podstawie trwałości atrybutu na Twoje aktora, HasPersistedState zostanie również ustawiona flaga odpowiednio.
* Pakiet kodu.
* Pakiet konfiguracji.
* Zasoby i punktów końcowych.

### <a name="application-manifest"></a>Manifest aplikacji
Narzędzia kompilacji framework aktora automatyczne tworzenie definicji domyślnej usługi dla usługi aktora. Narzędzia kompilacji wypełniania właściwości usługi domyślne:

* Liczba zestawu replik jest określana przez atrybut trwałości na Twoje aktora. Zawsze, gdy atrybut trwałości na Twoje aktora została zmieniona, liczba zestawu replik w definicji domyślnej usługi zostanie zresetowany odpowiednio.
* Schemat partycji i zakres są ustawione na jednolitych Int64 z pełnego zakresu klucza Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Pojęcia dotyczące partycji usługi sieć szkieletowa osób
Usługi aktora są podzielone na partycje usługi stanowej. Każda partycja usługi aktora zawiera zestaw złośliwych użytkowników. Partycji usługi są automatycznie dystrybuowane za pośrednictwem wiele węzłów w sieci szkieletowej usług. W związku z tym rozpowszechnianych wystąpień aktora.

![Aktora partycjonowania i dystrybucji][5]

Niezawodne usługi mogą być tworzone z różnych partycji, systemów i zakresami kluczy partycji. Usługa aktora wykorzystuje schemat partycjonowania Int64 z pełnego zakresu klucza Int64 do mapowania złośliwych użytkowników do partycji.

### <a name="actor-id"></a>Identyfikator aktora
Każdy aktora utworzony w usłudze ma unikatowy identyfikator skojarzony z nim reprezentowany przez `ActorId` klasy. `ActorId` to wartość Identyfikatora nieprzezroczyste, która może służyć do dystrybucji uniform podmiotów między partycji usługi przez Generowanie losowe identyfikatory:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Każdy `ActorId` jest wartość skrótu do postaci liczby Int64. Jest to, dlaczego usługa aktora musi używać schemat partycjonowania Int64 z pełnego zakresu klucza Int64. Jednak można używać niestandardowej wartości Identyfikatora dla `ActorID`, w tym identyfikatory GUID/UUID, ciągi i Int64s.

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

Podczas korzystania z identyfikatorów GUID/UUID i ciągi, wartości są wartość skrótu do postaci liczby Int64. Jednak gdy możesz jest jawnie dostarczanie Int64 do `ActorId`, Int64 przypisze bezpośrednio do partycji bez dalszego wyznaczania wartości skrótu. Można użyć tej metody do partycji, których podmioty są umieszczane w kontroli.


## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Aktora cykl życia i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacji interfejsu API złośliwych użytkowników](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java przykładowy kod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
