---
title: Polimorfizm w strukturze elementów Reliable Actors | Dokumentacja firmy Microsoft
description: Tworzenie hierarchii .NET interfejsów i typów w ramach elementów Reliable Actors do ponownego użycia funkcji i definicji interfejsu API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662962"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizm w strukturze elementów Reliable Actors
Struktura elementów Reliable Actors umożliwia tworzenie złośliwych użytkowników używających wiele z tych samych technik, które są używane w projekcie zorientowane obiektowo. Jedną z tych technik jest polimorfizm, która zezwala na typy i interfejsy odziedziczone z więcej uogólniony elementów nadrzędnych. Dziedziczenie w ramach elementów Reliable Actors zwykle następuje modelu .NET z kilku dodatkowe ograniczenia. W przypadku języka Java/Linux jest zgodna z modelu języka Java.

## <a name="interfaces"></a>Interfejsy
Struktura elementów Reliable Actors wymaga można zdefiniować co najmniej jeden interfejs implementowany przez użytkownika typ aktora. Ten interfejs jest używany do generowania klasy serwera proxy, które mogą być używane przez klientów do komunikowania się z Twojego aktorów. Interfejsy może dziedziczyć z innych interfejsów, tak długo, jak długo każdy interfejs, który jest implementowany przez typ aktora i wszystkie jego elementy nadrzędne ostatecznie dziedziczyć IActor (C#) lub Actor(Java). IActor (C#) i Actor(Java) są odpowiednio zdefiniowanej platformy interfejsy podstawowe dla uczestników platform .NET i Java. W związku z tym w przykładzie polimorfizm klasycznej za pomocą kształtów może wyglądać mniej więcej tak:

![Hierarchia interfejsu dla kształtu aktorów][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Można również utworzyć hierarchię typów aktora, które pochodzą z klasy bazowej aktora, który jest dostarczany przez platformę. W przypadku kształtów, Niewykluczone, że podstawowej `Shape`(C#) lub `ShapeImpl`(Java), wpisz:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Subtypes z `Shape`(C#) lub `ShapeImpl`(Java), można zastąpić metody od podstawy.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Uwaga `ActorService` atrybut typ aktora. Ten atrybut informuje szablon Reliable Actors, że usługa do hostowania aktorów tego typu należy utworzyć automatycznie. W niektórych przypadkach warto utworzyć typu podstawowego, który jest wyłącznie przeznaczony do udostępniania funkcji podtypy i nigdy nie będzie używany do utworzenia wystąpienia konkretnych aktorów. W takich przypadkach należy użyć `abstract` — słowo kluczowe, aby wskazać, że nigdy nie spowoduje utworzenie aktora, na podstawie tego typu.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [jak korzysta z platformy usługi Service Fabric Reliable Actors w ramach](service-fabric-reliable-actors-platform.md) zapewnienie niezawodności, skalowalności i spójne.
* Dowiedz się więcej o [cyklu życia aktora](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
