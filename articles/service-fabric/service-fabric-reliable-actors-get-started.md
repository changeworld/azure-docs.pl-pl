---
title: Tworzenie usługi opartej na usłudze Azure Service Fabric
description: Dowiedz się, jak tworzyć, debugować i wdrażać pierwszą usługę opartą na aktorach w języku C# przy użyciu niezawodnych aktorów sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466252"
---
# <a name="getting-started-with-reliable-actors"></a>Pierwsze kroki z wiarygodnymi aktorami
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-actors-get-started.md)
> * [Java w systemie Linux](service-fabric-reliable-actors-get-started-java.md)

W tym artykule oględziny tworzenia i debugowania prostej aplikacji niezawodnego aktora w programie Visual Studio. Aby uzyskać więcej informacji na temat wiarygodnych aktorów, zobacz [Wprowadzenie do sieci szkieletowej usług Wiarygodnych aktorów.](service-fabric-reliable-actors-introduction.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz środowisko programistyczne sieci szkieletowej usług, w tym Visual Studio, skonfigurowane na komputerze. Aby uzyskać szczegółowe informacje, [zobacz, jak skonfigurować środowisko programistyczne](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Utworzenie nowego projektu w Visual Studio

Uruchom program Visual Studio 2019 lub nowszy jako administrator, a następnie utwórz nowy projekt **aplikacji sieci szkieletowej usług:**

![Narzędzia sieci szkieletowej usług dla programu Visual Studio — nowy projekt][1]

W następnym oknie dialogowym wybierz pozycję **Usługa aktora** w obszarze **.NET Core 2.0** i wprowadź nazwę usługi.

![Szablony projektów sieci szkieletowej usług][5]

Utworzony projekt przedstawia następującą strukturę:

![Struktura projektu sieci szkieletowej usług][2]

## <a name="examine-the-solution"></a>Zbadaj rozwiązanie

Rozwiązanie zawiera trzy projekty:

* **Projekt aplikacji (MyApplication)**. Ten projekt pakiety wszystkich usług razem do wdrożenia. Zawiera *skrypty ApplicationManifest.xml* i PowerShell do zarządzania aplikacją.

* **Projekt interfejsu (HelloWorld.Interfaces)**. Ten projekt zawiera definicję interfejsu dla aktora. Interfejsy aktora można zdefiniować w dowolnym projekcie o dowolnej nazwie.  Interfejs definiuje kontrakt aktora, który jest współużytkowane przez implementację aktora i klientów wywołujących aktora.  Ponieważ projekty klienta może zależeć od niego, zazwyczaj ma sens, aby zdefiniować go w zestawie, który jest oddzielony od implementacji aktora.

* **Projekt usługi aktora (HelloWorld)**. Ten projekt definiuje usługę sieci szkieletowej usług, która będzie hostować aktora. Zawiera implementację aktora, *HelloWorld.cs*. Implementacja aktora jest klasą, która `Actor` wywodzi się z typu podstawowego i implementuje interfejsy zdefiniowane w projekcie *MyActor.Interfaces.* Klasa aktora musi również implementować konstruktora, który akceptuje wystąpienie `ActorService` i `ActorId` i przekazuje je do klasy podstawowej. `Actor`
    
    Ten projekt zawiera również *Program.cs*, który rejestruje klasy aktora w `ActorRuntime.RegisterActorAsync<T>()`czasie wykonywania sieci szkieletowej usług przy użyciu . Klasa `HelloWorld` jest już zarejestrowana. Wszelkie dodatkowe implementacje aktora dodane do projektu `Main()` muszą być również zarejestrowane w metodzie.

## <a name="customize-the-helloworld-actor"></a>Dostosowywanie aktora HelloWorld

Szablon projektu definiuje niektóre metody `IHelloWorld` w interfejsie i `HelloWorld` implementuje je w implementacji aktora.  Zastąp te metody, aby usługa aktora zwracała prosty ciąg "Hello World".

W projekcie *HelloWorld.Interfaces* w pliku *IHelloWorld.cs* zastąp definicję interfejsu w następujący sposób:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

W projekcie **HelloWorld** w **HelloWorld.cs**zastąpi całą definicję klasy w następujący sposób:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Naciśnij **klawisze Ctrl-Shift-B,** aby utworzyć projekt i upewnić się, że wszystko się kompiluje.

## <a name="add-a-client"></a>Dodawanie klienta

Utwórz prostą aplikację konsoli, aby wywołać usługę aktora.

1. Kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań > **Dodaj** > **nowy projekt...**.

2. W obszarze typy projektów **.NET Core** wybierz pozycję **Aplikacja konsoli (.NET Core)**.  Nazwij projekt *ActorClient*.
    
    ![Okno dialogowe Dodawanie nowego projektu][6]    
    
    > [!NOTE]
    > Aplikacja konsoli nie jest typem aplikacji, której zazwyczaj używasz jako klienta w sieci szkieletowej usług, ale stanowi wygodny przykład do debugowania i testowania przy użyciu lokalnego klastra sieci szkieletowej usług.

3. Aplikacja konsoli musi być aplikacją 64-bitową, aby zachować zgodność z projektem interfejsu i innymi zależnościami.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **ActorClient,** a następnie kliknij polecenie **Właściwości**.  Na karcie **Kompilacja** ustaw **docelową platformę** na **x64**.
    
    ![Właściwości kompilacji][8]

4. Projekt klienta wymaga pakietu NuGet wiarygodnych aktorów.  Kliknij pozycję **Narzędzia** > **NuGet Package Manager** > **Package Manager Console**.  W konsoli Menedżera pakietów wprowadź następujące polecenie:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Pakiet NuGet i wszystkie jego zależności są instalowane w projekcie ActorClient.

5. Projekt klienta wymaga również odwołania do projektu interfejsów.  W projekcie ActorClient kliknij prawym przyciskiem myszy **pozycję Zależności,** a następnie kliknij polecenie **Dodaj odwołanie...**.  Wybierz **opcję Projekty > rozwiązanie** (jeśli nie jest jeszcze zaznaczone), a następnie zaznacz pole wyboru obok **helloworld.interfaces**.  Kliknij przycisk **OK**.
    
    ![Okno dialogowe Dodawanie odwołania][7]

6. W projekcie ActorClient zastąp całą zawartość *Program.cs* następującym kodem:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Uruchamianie i debugowanie

Naciśnij **klawisz F5,** aby skompilować, wdrożyć i uruchomić aplikację lokalnie w klastrze deweloperów sieci szkieletowej usług.  Podczas procesu wdrażania można zobaczyć postęp w oknie **Dane wyjściowe.**

![Okno wyjściowe debugowania sieci szkieletowej usług][3]

Gdy dane wyjściowe zawiera tekst, *Aplikacja jest gotowa*, jest możliwe, aby przetestować usługę przy użyciu aplikacji ActorClient.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **ActorClient,** a następnie kliknij polecenie**Rozpocznij nowe wystąpienie** **debugowania** > .  Aplikacja wiersza polecenia powinna wyświetlać dane wyjściowe z usługi aktora.

![Dane wyjściowe aplikacji][9]

> [!TIP]
> Środowisko uruchomieniowe Aktorzy sieci szkieletowej usług emituje niektóre [zdarzenia i liczniki wydajności związane z metodami aktora.](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters) Są one przydatne w diagnostyce i monitorowaniu wydajności.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [tym, jak niezawodni aktorzy korzystają z platformy sieci szkieletowej usług.](service-fabric-reliable-actors-platform.md)


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png