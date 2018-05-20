---
title: Polimorfizm w ramach Reliable Actors | Dokumentacja firmy Microsoft
description: Tworzenie hierarchii interfejsów .NET i typów w ramach Reliable Actors do ponownego użycia funkcji i definicje interfejsu API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f03330bbbf2c1c7f92310211058f7ef8858f1941
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizm w ramach Reliable Actors
Framework Reliable Actors umożliwia tworzenie złośliwych użytkowników używających wiele z tych samych metod, które należy użyć w projekcie zorientowane obiektowo. Jeden z tych metod jest polimorfizm, co umożliwia typów i interfejsy odziedziczone po więcej uogólniony elementów nadrzędnych. Dziedziczenie w ramach Reliable Actors zazwyczaj następuje modelu .NET z kilku dodatkowe ograniczenia. W przypadku języka Java/Linux wynika z modelu Java.

## <a name="interfaces"></a>Interfejsy
Reliable Actors framework wymaga zdefiniowania co najmniej jeden interfejs do zaimplementowania przez danego typu aktora. Ten interfejs jest używany do generowania klasy serwera proxy, używaną przez klientów do komunikowania się z sieci złośliwych użytkowników. Interfejsy może dziedziczyć po elemencie inne interfejsy, jak długo każdy interfejs, który jest implementowany przez typ aktora i wszystkich jego obiektów nadrzędnych ostatecznie pochodzić od IActor(C#) lub Actor(Java). IActor(C#) i Actor(Java) są odpowiednio określone platformy interfejsach podstawowych dla uczestników platformy .NET i Java. W związku z tym przykładzie klasycznego polimorfizm kształtów może wyglądać następująco:

![Interfejs hierarchii osób kształtu][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Można również utworzyć hierarchię aktora typów, które są pochodnymi klasy podstawowej aktora, zapewnianej przez platformę. W przypadku kształtów, może mieć podstawy `Shape`(C#) lub `ShapeImpl`typu (Java):

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

Subtypes z `Shape`(C#) lub `ShapeImpl`(Java) można zastąpić metody od podstawy.

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

Uwaga `ActorService` atrybut typu aktora. Ten atrybut informuje framework niezawodnego aktora, automatycznie należy utworzyć usługi hostingu uczestników tego typu. W niektórych przypadkach warto utworzyć podstawowy typ, który wyłącznie jest przeznaczony do udostępniania funkcji podtypów i nigdy nie będzie używany do utworzenia wystąpienia konkretnych osób. W takich przypadkach należy użyć `abstract` — słowo kluczowe, aby wskazać, że nigdy nie utworzysz aktora na podstawie tego typu.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [jak framework Reliable Actors korzysta z platformy sieć szkieletowa usług](service-fabric-reliable-actors-platform.md) zapewnienie niezawodności i skalowalności oraz w spójnym stanie.
* Dowiedz się więcej o [cyklu życia aktora](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
