---
title: Utwórz Twojego pierwszego mikrousługi Azure na podstawie aktora w języku C# | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia kroki tworzenia, debugowania i wdrażania prostego usługi aktora na podstawie przy użyciu usługi sieci szkieletowej Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 32d3fa09c863c47753267e97e7c4730dff869887
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211409"
---
# <a name="getting-started-with-reliable-actors"></a>Wprowadzenie do korzystania z Reliable Actors
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-actors-get-started.md)
> * [Java w systemie Linux](service-fabric-reliable-actors-get-started-java.md)

W tym artykule przedstawiono tworzenie i debugowanie prostą aplikację niezawodnego aktora w programie Visual Studio. Aby uzyskać więcej informacji dotyczących Reliable Actors, zobacz [wprowadzenie do usługi sieci szkieletowej Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz środowisko projektowe sieci szkieletowej usług, w tym Visual Studio na komputerze. Aby uzyskać więcej informacji, zobacz [jak skonfigurować środowisko projektowe](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Utwórz nowy projekt w programie Visual Studio

Uruchom program Visual Studio 2015 lub nowszego jako administrator, a następnie utwórz nową **aplikacji sieci szkieletowej usług** projektu:

![Narzędzia usługi sieć szkieletowa usług dla programu Visual Studio — nowy projekt][1]

W następnym oknie dialogowym wybierz **usługi aktora** w obszarze **.Net Core 2.0** , a następnie wprowadź nazwę usługi.

![Szablony projektu sieci szkieletowej usług][5]

Utworzony projekt zawiera następującą strukturę:

![Struktury projektu sieci szkieletowej usług][2]

## <a name="examine-the-solution"></a>Sprawdź rozwiązania

Rozwiązanie zawiera trzy projekty:

* **Projekt aplikacji (moja_aplikacja)**. Ten projekt pakiety wszystkich usług jednocześnie do wdrożenia. Zawiera on *ApplicationManifest.xml* i skrypty programu PowerShell do zarządzania aplikacji.

* **Projekt interfejsu (HelloWorld.Interfaces)**. Ten projekt zawiera definicję interfejsu aktora. Interfejsy aktora można zdefiniować w każdym projekcie z żadną nazwą.  Interfejs definiuje kontraktu aktora, który jest współużytkowany przez implementacji aktora i klientów wywoływania aktora.  Ponieważ projektów klienta może od niej zależne, zazwyczaj warto definiować go w zestawie, do którego jest oddzielony od implementacji aktora.

* **Projekt usługi aktora (HelloWorld)**. Ten projekt definiuje usługi sieci szkieletowej usług, który ma obsługiwać aktora. Zawiera ona wdrożenia aktora, *HellowWorld.cs*. Implementacja aktora jest klasa, która pochodzi z typu podstawowego `Actor` i implementuje interfejsów zdefiniowanych w *MyActor.Interfaces* projektu. Klasa aktora musi także implementować konstruktora akceptującego `ActorService` wystąpienia i `ActorId` i przekazuje je do podstawy `Actor` klasy.
    
    Ten projekt zawiera także *Program.cs*, która rejestruje klasy aktora przy użyciu środowiska uruchomieniowego platformy Service Fabric `ActorRuntime.RegisterActorAsync<T>()`. `HelloWorld` Klasa jest już zarejestrowany. Dodatkowe aktora implementacjami dodane do projektu, również musi być zarejestrowana w `Main()` metody.

## <a name="customize-the-helloworld-actor"></a>Dostosowywanie aktora HelloWorld

Szablon projektu definiuje niektóre metody w `IHelloWorld` interfejsu i implementuje je w `HelloWorld` implementacji aktora.  Zastąp tych metod, dlatego usługa aktora zwraca prosty ciąg "Hello World".

W *HelloWorld.Interfaces* projektu w *IHelloWorld.cs* pliku, Zastąp definicję interfejsu w następujący sposób:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

W **HelloWorld** projektu w **HelloWorld.cs**, Zastąp definicję całą grupę w następujący sposób:

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

Naciśnij klawisz **Ctrl-Shift-B** Aby skompilować projekt i upewnij się, wszystko kompiluje.

## <a name="add-a-client"></a>Dodawanie klienta

Tworzenie prostej aplikacji konsolowej do wywołania tej usługi aktora.

1. Kliknij prawym przyciskiem myszy w ramach rozwiązania w Eksploratorze rozwiązań > **Dodaj** > **nowy projekt...** .

2. W obszarze **.NET Core** typy projektów, wybierz **aplikacji konsoli (.NET Core)**.  Nazwij projekt *ActorClient*.
    
    ![Dodaj okno dialogowe Nowy projekt][6]    
    
    > [!NOTE]
    > Aplikacja konsolowa nie jest typu aplikacji, które należy zwykle użyć jako klient w sieci szkieletowej usług, ale umożliwia wygodne przykład do debugowania i testowania przy użyciu lokalnego klastra usługi sieć szkieletowa usług.

3. Aplikacja konsoli musi mieć aplikacji 64-bitowych, aby zachować zgodność z projektu interfejsu i inne zależności.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ActorClient** projektu, a następnie kliknij przycisk **właściwości**.  Na **kompilacji** ustaw **platformy docelowej** do **x64**.
    
    ![Właściwości kompilacji][8]

4. Projekt klienta wymaga pakietu NuGet niezawodnej złośliwych użytkowników.  Kliknij pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.  W konsoli Menedżera pakietów wprowadź następujące polecenie:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Pakiet NuGet i wszystkie jego zależności są zainstalowane w projekcie ActorClient.

5. Projekt klienta wymaga również odwołanie do projektu interfejsów.  W projekcie ActorClient, kliknij prawym przyciskiem myszy **zależności** , a następnie kliknij przycisk **dodać odwołania...** .  Wybierz **projekty > rozwiązania** (Jeśli nie jest już zaznaczone), a następnie zaznacz pole wyboru obok pozycji **HelloWorld.Interfaces**.  Kliknij przycisk **OK**.
    
    ![Dodaj odwołanie do okna dialogowego][7]

6. W projekcie ActorClient Zastąp całą zawartość *Program.cs* następującym kodem:
    
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

## <a name="running-and-debugging"></a>Uruchomione i debugowania

Naciśnij klawisz **F5** tworzenie, wdrażanie i uruchamianie aplikacji lokalnie w klastrze Projektowanie sieci szkieletowej usług.  Podczas procesu wdrażania możesz wyświetlić postęp w **dane wyjściowe** okna.

![Okno danych wyjściowych debugowania sieci szkieletowej usług][3]

Gdy dane wyjściowe zawierają tekst, *aplikacja jest gotowa*, można testować usługę przy użyciu aplikacji ActorClient.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ActorClient** projektu, a następnie kliknij przycisk **debugowania** > **Start nowe wystąpienie**.  Dane wyjściowe z usługi aktora powinien być wyświetlany w aplikacji wiersza polecenia.

![dane wyjściowe aplikacji][9]

> [!TIP]
> Środowisko uruchomieniowe usługi sieć szkieletowa podmiotów emituje niektóre [zdarzenia i liczniki wydajności powiązane z metody aktora](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Są one przydatne w Diagnostyka i monitorowanie wydajności.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wykorzystania platformy sieć szkieletowa usług Reliable Actors](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png