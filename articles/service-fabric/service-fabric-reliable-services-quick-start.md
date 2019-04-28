---
title: Tworzenie pierwszej aplikacji usługi Service Fabric w C# | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji usługi Microsoft Azure Service Fabric za pomocą usługi stanowe i bezstanowe.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: d27702983a4378becdbc67f3f156c92be3dc3af6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130096"
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Aplikacja usługi Azure Service Fabric zawiera jeden lub więcej usług, które uruchomienia kodu. Ten przewodnik pokazuje sposób tworzenia stanowe i bezstanowe aplikacji usługi Service Fabric za pomocą [usług Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z usługami Reliable Services, należy tylko poznać kilka podstawowych pojęć:

* **Typ usługi**: Jest to Twoja implementacja usługi. Jest definicją klasy pisania, która rozszerza `StatelessService` i innego kodu lub zależności razem z nim, nazwę i numer wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, należy utworzyć nazwanych wystąpień danego typu usługi znacznie jak tworzenia wystąpienia obiektu typu klasy. Wystąpienie usługi o nazwie w formie identyfikatora URI za pomocą "Service fabric: /" schemat, takie jak "fabric: / MyApp/Moja_usługa".
* **Host usługi**: Utworzonych wystąpień usługi o nazwie konieczne uruchamiane wewnątrz procesu hosta. Host usługi jest po prostu procesu, gdzie można uruchomić wystąpienia usługi.
* **Usługa rejestracji**: Rejestracja łączy wszystkie elementy. Typ usługi musi być zarejestrowany ze środowiskiem uruchomieniowym usługi Service Fabric na hoście usługi, aby umożliwić usługi Service Fabric utworzyć jego wystąpienia do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Usługa bezstanowa jest typem usługi, która jest obecnie normy w aplikacjach w chmurze. Uznano bezstanowych, ponieważ sama usługa nie zawiera danych, który ma być przechowywane w niezawodny sposób będą ani o wysokiej dostępności. Jeśli wystąpienie usługi bezstanowej zamknięcie, wszystkie jego stanu wewnętrznego zostaną utracone. W tym typie usługi stanu musi zostać utrwalona, do magazynu zewnętrznego, takie jak tabele platformy Azure lub bazę danych SQL, a dla niego ma zostać wykonane, wysoko dostępne i niezawodne.

Uruchom program Visual Studio 2015 lub Visual Studio 2017 jako administrator i Utwórz nowy projekt aplikacji usługi Service Fabric o nazwie *HelloWorld*:

![Użyj okna dialogowego Nowy projekt do tworzenia nowej aplikacji usługi Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Następnie utwórz projekt usługi bezstanowej za pomocą **.NET Core 2.0** o nazwie *HelloWorldStateless*:

![W drugim oknie dialogowym Tworzenie projektu usługi bezstanowej](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Rozwiązanie zawiera teraz dwa projekty:

* *HelloWorld*. Jest to *aplikacji* projektu, który zawiera Twoje *usług*. Zawiera ona także manifest aplikacji, który opisuje aplikację, a także szereg skryptów programu PowerShell, które ułatwiają wdrażanie aplikacji.
* *HelloWorldStateless*. Jest to projekt usługi. Zawiera ona wdrożenia o bezstanowa usługa.

## <a name="implement-the-service"></a>Implementuje usługi
Otwórz **HelloWorldStateless.cs** pliku w projekcie usługi. W usłudze Service Fabric usługa może działać wszelka logika biznesowa. Interfejs API usługi zawiera dwa punkty wejścia w kodzie:

* Metody punktu wejścia nieograniczony, o nazwie *RunAsync*, w którym możesz rozpocząć wykonywanie obciążeń, w tym obciążeń obliczeniowych długoterminowych.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Punkt wejścia komunikacji, gdzie możesz podłączyć stosu komunikacji wyboru, takich jak ASP.NET Core. Jest to, gdzie można uruchomić odbieranie żądań od użytkowników i innych usług.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

W tym samouczku skupimy się na `RunAsync()` metody punktu wejścia. Jest to, gdzie można natychmiast rozpocząć wykonywanie Twojego kodu.
Szablon projektu zawiera przykład implementacji `RunAsync()` , zwiększa się liczba stopniowe.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat pracy z stos komunikacji, zobacz [usług internetowy interfejs API usługi Service Fabric przy użyciu własnym hostingu OWIN](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczony i gotowe do wykonania. Usługi bezstanowej po prostu oznacza to, gdy wystąpienie usługi jest otwarty. Token anulowania jest udostępniane na koordynowanie czasu wystąpienia usługi musi zostać zamknięty. W usłudze Service Fabric ten cykl otwarcie i zamknięcie wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć różnych powodów, takich jak:

* System przenosi wystąpień usługi dla równoważenia zasobów.
* Błędy występują w kodzie.
* Uaktualnieniu aplikacji lub systemu.
* Używany sprzęt, wystąpi awaria.

Takie ograniczenia jest zarządzany przez system w celu zachowania usługi o wysokiej dostępności i prawidłowo o zrównoważonym obciążeniu.

`RunAsync()` należy nie blokuje synchronicznie. Implementacja RunAsync powinna zwracać zadanie lub poczekać na żadnych operacji długotrwałych lub blokowania, aby umożliwić środowiska uruchomieniowego kontynuować. Zwróć uwagę, w `while(true)` pętli w poprzednim przykładzie, zwracając zadanie `await Task.Delay()` jest używany. Jeśli obciążenie należy zablokować synchronicznie, należy zaplanować nowe zadanie z `Task.Run()` w swojej `RunAsync` implementacji.

Anulowanie obciążenia jest wspólnego nakładu pracy, zorganizowanych według podanego tokenu anulowania. System będzie czekać na zadanie zakończenia (za pomyślne wykonanie, anulowania lub błędu), zanim przemieszczał się. Jest ważne uwzględnić token anulowania, Zakończ wszelkie prace i zamknąć `RunAsync()` tak szybko, jak to możliwe, gdy system zażąda anulowania.

W tym przykładzie Usługa bezstanowa liczby są przechowywane w zmiennej lokalnej. Ale ponieważ usługę bezstanową, wartość, która jest przechowywana istnieje tylko dla bieżącego cyklu życia wystąpienia usługi. Gdy usługa przejdzie lub ponowne uruchomienie, wartość jest utracone.

## <a name="create-a-stateful-service"></a>Tworzenie stanowej usługi
Usługa Service Fabric wprowadza nowy rodzaj usługi stanowej. Usługa stanowa może zachowywać stan, niezawodne w ramach usługi, wspólnie z kodem, który jest używany. Stan dokonuje wysokiej dostępności usługi Service Fabric bez konieczności utrwalanie stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanową wysoko dostępnych i trwałych, nawet wtedy, gdy usługa przejdzie lub ponowne uruchomienie, potrzebujesz usługi stanowej.

W tym samym *HelloWorld* aplikacji, można dodać nową usługę, kliknij prawym przyciskiem myszy usług odwołań w projekcie aplikacji i wybierając **Dodaj -> Nowa usługa Service Fabric**.

![Dodać usługę do aplikacji usługi Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wybierz **.NET Core 2.0 -> Usługa stanowa** i nadaj mu nazwę *HelloWorldStateful*. Kliknij przycisk **OK**.

![Użyj okna dialogowego Nowy projekt do utworzenia nowej usługi stanowej usługi Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Twoja aplikacja powinna mieć teraz dwie usługi: usługi bezstanowej *HelloWorldStateless* i usługi stanowej *HelloWorldStateful*.

Usługa stanowa ma ten sam punkty wejścia jako bezstanowej usługi. Główna różnica polega na dostępność *dostawca stanu* , niezawodnego przechowywania stanu. Usługa Service Fabric jest dostarczany z implementacja dostawcy stanu o nazwie [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md), która umożliwia tworzenie replikowane struktur danych za pośrednictwem Reliable State Manager. Domyślnie stanowej usługi Reliable Service używa tego dostawcy stanu.

Otwórz **HelloWorldStateful.cs** w *HelloWorldStateful*, który zawiera następujące metody RunAsync:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync
`RunAsync()` działa podobnie w usługi stanowe i bezstanowe. Jednak w stanowej usłudze platformy wykonuje dodatkowej pracy w Twoim imieniu przed rozpoczęciem wykonywania `RunAsync()`. Ta praca może obejmować zapewnienie, że Reliable State Manager i elementów Reliable Collections są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i niezawodne Menedżer stanu
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) stanowi implementację słownika, która służy do niezawodnego przechowywania stanu usługi. Usługa Service Fabric i elementów Reliable Collections dane można przechowywać bezpośrednio w usłudze bez konieczności dla zewnętrznego magazynu trwałego. Elementy Reliable Collections danych wysokiej dostępności. Usługa Service Fabric to w ramach przez tworzenie i zarządzanie wieloma *replik* usługi dla Ciebie. Udostępnia również interfejs API, który pozwala zmniejszyć wagę komplikacje związane z zarządzaniem tych replik oraz ich stanami.

Elementy Reliable Collections może przechowywać dowolnego typu .NET, w tym usługi typów niestandardowych przy użyciu kilku ostrzeżenia:

* Usługa Service Fabric zapewnia wysoce dostępny stan programu *replikowanie* stanu między węzłami oraz elementów Reliable Collections przechowywania danych na dysku lokalnym na każdej repliki. Oznacza to, że wszystko, co znajduje się w elementach Reliable Collections musi być *serializacji*. Domyślnie używają elementów Reliable Collections [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) serializacji, dlatego ważne jest upewnić się, że Twoje typy są [obsługiwane przez serializator kontraktu danych](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) kiedy używać domyślnego elementu serializującego.
* Obiekty są replikowane w celu zapewnienia wysokiej dostępności, podczas zatwierdzania transakcji dla elementów Reliable Collections. Obiekty przechowywane w elementach Reliable Collections są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalnego odwołania do obiektu.
  
   Należy pamiętać, że mutuje lokalnych wystąpień tych obiektów bez przeprowadzania operacji aktualizacji w niezawodnej kolekcji w ramach transakcji. Jest to spowodowane zmian do lokalnego wystąpienia obiektów, nie będą automatycznie replikowane. Należy ponownie włóż obiekt do słownika lub użyj jednego z *aktualizacji* metod w słowniku.

Reliable State Manager zarządza elementów Reliable Collections. Możesz po prostu poprosić Reliable State Manager for reliable collection według nazwy w dowolnym czasie i w dowolnym miejscu w usłudze. Reliable State Manager zapewnia ponownie uzyskać odwołanie. Nie zaleca się zapisać odwołania do wystąpienia niezawodnej kolekcji w składowej klasy, zmienne lub właściwości. Specjalne należy uważać, aby upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usług. Reliable State Manager obsługuje tę pracę za Ciebie, i jest zoptymalizowany do powtarzania wizyty.

### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Elementy Reliable Collections mają wiele z tych samych operacji, ich `System.Collections.Generic` i `System.Collections.Concurrent` odpowiedniki z wyjątkiem LINQ. Operacje na elementów Reliable Collections są asynchroniczne. Jest to spowodowane operacji zapisu z elementami Reliable Collections wykonywać operacje We/Wy do replikacji i utrwalić dane na dysku.

Niezawodne operacje kolekcji są *transakcyjnych*, dzięki czemu można zachować stanu zgodny w wielu elementów Reliable Collections i operacji. Może na przykład usuwania z kolejki elementu roboczego z kolejką niezawodne, wykonaj operację i zapisać wynik w niezawodnym słowniku, wszystkie w ramach jednej transakcji. Jest ona traktowana jako operację niepodzielną i gwarantuje, że cała operacja powiedzie się lub cała operacja spowoduje przywrócenie. Jeśli błąd wystąpi po usuwania z kolejki elementu, ale przed zapisaniem wynik, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Firma Microsoft teraz wróć do *HelloWorld* aplikacji. Teraz można tworzyć i wdrażać usługi. Po naciśnięciu klawisza **F5**, aplikacji zostanie utworzone i wdrożone w klastrze lokalnym.

Po usługi uruchomione, można wyświetlić wygenerowanych zdarzeń śledzenie zdarzeń dla Windows (ETW) w **zdarzenia diagnostyczne** okna. Pamiętaj, że zdarzenia wyświetlane są zarówno usługę bezstanową, jak i usługi stanowej w aplikacji. Możesz wstrzymać strumienia, klikając **wstrzymać** przycisku. Następnie można sprawdzić szczegółowe informacje o wiadomości, rozwijając ten komunikat.

> [!NOTE]
> Przed uruchomieniem aplikacji upewnij się, że klaster lokalny rozwój. Zapoznaj się z [przewodnik wprowadzenie](service-fabric-get-started.md) informacji na temat konfigurowania środowiska lokalnego.
> 
> 

![Wyświetlanie zdarzeń diagnostycznych w programie Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-debugging-your-application.md)

[Rozpocznij pracę: Usługi internetowego interfejsu API usługi Service Fabric z własnym hostingu OWIN](service-fabric-reliable-services-communication-webapi.md)

[Dowiedz się więcej na temat elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[Uaktualnienie aplikacji](service-fabric-application-upgrade.md)

[Dokumentacja dla deweloperów dla usług Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

