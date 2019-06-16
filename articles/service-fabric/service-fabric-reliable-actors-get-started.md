---
title: Tworzenie usługi opartej na aktora w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, debugowanie i wdrażanie pierwszej usługi aktorów w języku C# za pomocą elementów Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: b6ca4810d86bb3c8413f0a740ac4483a848b8e10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726386"
---
# <a name="getting-started-with-reliable-actors"></a>Wprowadzenie do elementów Reliable Actors
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-actors-get-started.md)
> * [Java w systemie Linux](service-fabric-reliable-actors-get-started-java.md)

W tym artykule opisano proces tworzenia i debugowania prostej aplikacji Reliable Actors w programie Visual Studio. Aby uzyskać więcej informacji na temat elementów Reliable Actors, zobacz [wprowadzenie do usługi Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że środowisku programowania usługi Service Fabric, w tym programu Visual Studio na swojej maszynie. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania środowiska deweloperskiego](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Utwórz nowy projekt w programie Visual Studio

Uruchom program Visual Studio 2015 lub nowszym jako administrator, a następnie utwórz nową **aplikacji usługi Service Fabric** projektu:

![Narzędzia usługi Service Fabric dla programu Visual Studio — nowy projekt][1]

W następnym oknie dialogowym wybierz **usługa aktora** w obszarze **.NET Core 2.0** i wprowadź nazwę usługi.

![Szablony projektu usługi Service Fabric][5]

Utworzony projekt zawiera następującą strukturę:

![Struktura projektu usługi Service Fabric][2]

## <a name="examine-the-solution"></a>Sprawdź rozwiązanie

Rozwiązanie zawiera trzy projekty:

* **Projekt aplikacji (MyApplication)** . Pakiety tego projektu, wszystkich usług razem do wdrożenia. Zawiera on *ApplicationManifest.xml* i skryptów programu PowerShell do zarządzania aplikacji.

* **Projekt interfejsu (HelloWorld.Interfaces)** . Ten projekt zawiera definicję interfejsu aktora. W każdym projekcie o dowolnej nazwie można zdefiniować interfejsów aktora.  Interfejs definiuje kontrakt aktora udostępniany implementacji aktora i klientom wywołującym aktora.  Ponieważ projektów klienckich mogą od niej zależne, zazwyczaj warto zdefiniować go w zestawie, który jest oddzielony od implementacji aktora.

* **Projekt usługi aktora (HelloWorld)** . Ten projekt definiuje usługę Service Fabric, która będzie hostować aktora. Zawiera implementację aktora, *HelloWorld.cs*. Implementację aktora to klasa, która pochodzi od typu podstawowego `Actor` i implementuje interfejsy zdefiniowane w *MyActor.Interfaces* projektu. Klasa aktora musi implementować też Konstruktor, który akceptuje `ActorService` wystąpienia i `ActorId` i przekazuje je do podstawy `Actor` klasy.
    
    Ten projekt zawiera także *Program.cs*, który rejestruje przy użyciu środowiska uruchomieniowego usługi Service Fabric aktora klasy `ActorRuntime.RegisterActorAsync<T>()`. `HelloWorld` Klasa jest już zarejestrowany. Wszelkie implementacji aktora dodatkowe dodany do projektu, również musi być zarejestrowana w `Main()` metody.

## <a name="customize-the-helloworld-actor"></a>Dostosowywanie aktora HelloWorld

Szablon projektu definiuje kilka metod w `IHelloWorld` interfejs i zaimplementowano je w `HelloWorld` implementacji aktora.  Zastąp te metody, aby usługa aktora zwraca prosty ciąg "Hello World".

W *HelloWorld.Interfaces* projektu w *IHelloWorld.cs* pliku, Zastąp definicję interfejsu w następujący sposób:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

W **HelloWorld** projektu w **HelloWorld.cs**, Zastąp definicję całej klasy w następujący sposób:

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

Naciśnij klawisz **Ctrl-Shift-B** do skompilowania projektu i sprawdzić, czy wszystko kompiluje.

## <a name="add-a-client"></a>Dodawanie klienta

Tworzenie prostej aplikacji konsolowej do wywołania usługi aktora.

1. Kliknij prawym przyciskiem myszy na rozwiązanie w Eksploratorze rozwiązań > **Dodaj** > **nowy projekt...** .

2. W obszarze **platformy .NET Core** typów projektów, wybierz polecenie **Aplikacja konsoli (.NET Core)** .  Nadaj projektowi nazwę *ActorClient*.
    
    ![Dodaj okno dialogowe Nowy projekt][6]    
    
    > [!NOTE]
    > Aplikacja konsoli nie jest typu aplikacji, które zazwyczaj są używane jako klient w usłudze Service Fabric, ale zapewnia wygodne przykład debugowania i testowania przy użyciu lokalnego klastra usługi Service Fabric.

3. Aplikacja konsoli musi być 64-bitową aplikacją, aby zachować zgodność z projektu interfejsu i inne zależności.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ActorClient** projektu, a następnie kliknij przycisk **właściwości**.  Na **kompilacji** kartę, należy ustawić **platformę docelową** do **x64**.
    
    ![Właściwości kompilacji][8]

4. Projekt klienta wymaga elementów reliable actors pakietu NuGet.  Kliknij pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.  W konsoli Menedżera pakietów wpisz następujące polecenie:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Pakiet NuGet i wszystkich jego zależności są zainstalowane w projekcie ActorClient.

5. Projekt klienta wymaga również odwołanie do projektu interfejsach.  W projekcie ActorClient, kliknij prawym przyciskiem myszy **zależności** a następnie kliknij przycisk **Dodaj odwołanie...** .  Wybierz **projektów > rozwiązania** (Jeśli nie zostanie zaznaczona), a następnie zaznacz pole wyboru obok pozycji **HelloWorld.Interfaces**.  Kliknij przycisk **OK**.
    
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

## <a name="running-and-debugging"></a>Uruchamianie i debugowanie

Naciśnij klawisz **F5** tworzenie, wdrażanie i uruchamianie aplikacji lokalnie w klaster projektowy usługi Service Fabric.  Podczas procesu wdrażania można wyświetlić postęp w **dane wyjściowe** okna.

![Okno danych wyjściowych debugowania w usłudze Service Fabric][3]

Gdy dane wyjściowe zawierają tekst, *aplikacja jest gotowa*, istnieje możliwość przetestować usługę za pomocą aplikacji ActorClient.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ActorClient** projektu, a następnie kliknij przycisk **debugowania** > **Uruchom nowe wystąpienie**.  Aplikacja wiersza polecenia powinny pojawić się dane wyjściowe usługi aktora.

![Dane wyjściowe aplikacji][9]

> [!TIP]
> Środowisko uruchomieniowe aktorów usługi Service Fabric emituje niektóre [zdarzenia i liczniki wydajności powiązane z metody aktora](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Są one przydatne w Diagnostyka i monitorowanie wydajności.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [jak elementy Reliable Actors korzystają z platformy usługi Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png