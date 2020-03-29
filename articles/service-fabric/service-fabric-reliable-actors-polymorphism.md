---
title: Polimorfizm w ramach Wiarygodnych Aktorów
description: Tworzenie hierarchii interfejsów i typów platformy .NET w ramach reliable actors do ponownego użycia funkcji i definicji interfejsu API.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348939"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizm w ramach Wiarygodnych Aktorów
Struktura Reliable Actors umożliwia tworzenie aktorów przy użyciu wielu tych samych technik, które można użyć w projektowaniu obiektowym. Jedną z tych technik jest polimorfizm, który pozwala typom i interfejsom dziedziczyć po bardziej uogólnionych rodzicach. Dziedziczenie w ramach reliable actors zazwyczaj następuje model .NET z kilku dodatkowych ograniczeń. W przypadku Java / Linux, wynika z modelu Java.

## <a name="interfaces"></a>Interfejsy
Struktura reliable actors wymaga zdefiniowania co najmniej jednego interfejsu, który ma zostać zaimplementowany przez typ aktora. Ten interfejs jest używany do generowania klasy proxy, która może być używana przez klientów do komunikowania się z aktorami. Interfejsy mogą dziedziczyć z innych interfejsów, o ile każdy interfejs zaimplementowany przez typ aktora i wszystkie jego elementy rodziców ostatecznie pochodzą od IActor(C#) lub Actor(Java). IActor(C#) i Actor(Java) to zdefiniowane na platformie interfejsy podstawowe dla aktorów w ramach .NET i Java odpowiednio. Tak więc klasyczny przykład polimorfizmu przy użyciu kształtów może wyglądać mniej więcej tak:

![Hierarchia interfejsu dla aktorów kształtu][shapes-interface-hierarchy]

## <a name="types"></a>Types
Można również utworzyć hierarchię typów aktorów, które pochodzą od podstawowej actor klasy, która jest dostarczana przez platformę. W przypadku kształtów może być typ `Shape`podstawowy (C#) lub `ShapeImpl`(Java):

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

Podtypy `Shape`(C#) `ShapeImpl`lub (Java) można zastąpić metody z podstawy.

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

Zwróć `ActorService` uwagę na atrybut typu aktora. Ten atrybut informuje platformę niezawodnego aktora, że należy automatycznie utworzyć usługę dla podmiotów hostingu tego typu. W niektórych przypadkach można utworzyć typ podstawowy, który jest przeznaczony wyłącznie do udostępniania funkcji z podtypami i nigdy nie będzie używany do tworzenia wystąpienia konkretnych podmiotów. W takich przypadkach należy `abstract` użyć słowa kluczowego, aby wskazać, że nigdy nie utworzy aktora na podstawie tego typu.

## <a name="next-steps"></a>Następne kroki
* Zobacz, [jak platforma reliable actors wykorzystuje platformę sieci szkieletowej usług,](service-fabric-reliable-actors-platform.md) aby zapewnić niezawodność, skalowalność i spójny stan.
* Dowiedz się więcej o [cyklu życia aktora](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
