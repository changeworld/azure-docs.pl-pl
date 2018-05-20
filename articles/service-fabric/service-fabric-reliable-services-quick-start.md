---
title: Tworzenie pierwszej aplikacji platformy Service Fabric w języku C# | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji usługi sieć szkieletowa usług Microsoft Azure z usług bezstanowych i stanowych.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 6977fa0a62767cebbd1000335c6c3a33a5991c2c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Aplikację usługi Azure Service Fabric zawiera jeden lub więcej usług, które uruchomić kod. W tym przewodniku przedstawiono sposób tworzenia aplikacji platformy Service Fabric zarówno bezstanowe i stanowe z [niezawodne usługi](service-fabric-reliable-services-introduction.md).  Microsoft Virtual Academy wideo przedstawiono również sposób tworzenia bezstanowej niezawodnej usługi: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=s39AO76yC_7206218965">  
<img src="./media/service-fabric-reliable-services-quick-start/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z usługami Reliable Services, tylko musisz poznać kilka podstawowych pojęć:

* **Typ usługi**: jest to implementacji usługi. Jest on zdefiniowany przez klasę pisania, rozszerzający `StatelessService` i innego kodu lub zależności używany, oraz nazwę i numer wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, tworzenia nazwanego wystąpienia danego typu usługi znacznie tak, jak utworzyć wystąpienia obiektu typu klasy. Wystąpienie usługi ma nazwę w postaci przy użyciu identyfikatora URI "fabric: /" schemat, takich jak "sieci szkieletowej: / MyApp/Moja_usługa".
* **Host usługi**: wystąpień usługi o nazwie, należy utworzyć niezbędne do uruchomienia wewnątrz procesu hosta. Host usługi jest właśnie procesu, których można uruchomić wystąpienia usługi.
* **Usługa rejestracji**: rejestracji, połączono wszystko. Typ usługi musi być zarejestrowana ze środowiskiem uruchomieniowym usługi sieć szkieletowa w umożliwia sieci szkieletowej usług do tworzenia wystąpień usługi hosta do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Usługi bezstanowej jest typem usługi, która jest obecnie normą w aplikacji w chmurze. Uznano bezstanowych, ponieważ sama usługa nie zawiera danych, który musi być niezawodnie przechowywane lub zyskuje dużą dostępność. Jeśli wystąpienie usługi bezstanowej zostanie wyłączony, wszystkie jego stanu wewnętrznego zostaną utracone. W tym typie usługi stanu musi zostać utrwalony na zewnętrznym sklepie, takie jak tabele Azure lub bazy danych SQL, na jego się wysoką dostępność i niezawodne.

Uruchom program Visual Studio 2015 lub Visual Studio 2017 jako administrator, a następnie utwórz nowy projekt aplikacji sieci szkieletowej usług o nazwie *HelloWorld*:

![Użyj okna dialogowego Nowy projekt do tworzenia nowej aplikacji sieci szkieletowej usług](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Następnie utwórz projekt usługi bezstanowej za pomocą **.Net Core 2.0** o nazwie *HelloWorldStateless*:

![W oknie dialogowym drugi utworzyć projekt usługi bezstanowej](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Rozwiązanie zawiera teraz dwa projekty:

* *HelloWorld*. Jest to *aplikacji* projekt, który zawiera Twoje *usług*. Zawiera ona także manifest aplikacji, który opisuje aplikacji, a także wiele skryptów programu PowerShell, które ułatwiają wdrażanie aplikacji.
* *HelloWorldStateless*. Jest to projekt usługi. Zawiera ona wdrożenia usługi bezstanowej.

## <a name="implement-the-service"></a>Wdrożenie usługi
Otwórz **HelloWorldStateless.cs** plik w projekcie usługi. W sieci szkieletowej usług usługi można uruchomić wszelka logika biznesowa. Interfejs API usługi zawiera dwa punkty wejścia kodu:

* Metoda punktu wejścia otwarty, nazywany *RunAsync*, w którym można rozpocząć wykonywania dowolnych zadań, w tym obciążeń obliczeniowych długotrwałe.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Punkt wejścia komunikacji, gdzie można podłączyć stosu komunikacji wyboru, takich jak ASP.NET Core. Jest to, gdzie można uruchomić odbieranie żądań od użytkowników i innych usług.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

W tym samouczku, firma Microsoft będzie skupić się na `RunAsync()` metoda punktu wejścia. Jest to, gdzie natychmiast można rozpocząć uruchamianie kodu.
Szablon projektu zawiera implementację próbki `RunAsync()` który zwiększa liczbę stopniowe.

> [!NOTE]
> Aby uzyskać szczegółowe informacje dotyczące pracy z stosu komunikacji, zobacz [usług interfejsu API sieci Web sieci szkieletowej usług za pomocą hostingu samodzielnego OWIN](service-fabric-reliable-services-communication-webapi.md)
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

Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczony i gotowa do wykonania. Dla usługi bezstanowej po prostu oznacza to, gdy wystąpienie usługi jest otwarty. Token anulowania został dostarczony do koordynowania, gdy wystąpienie usługi musi zostać zamknięty. W sieci szkieletowej usług ten cykl otwarcie i zamknięcie wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całość. Może się to zdarzyć z różnych powodów, w tym:

* System przeniesie swoich wystąpień usługi dla równoważenia zasobów.
* Błędy występują w kodzie.
* Uaktualnianie aplikacji lub systemu.
* Sprzętu źródłowego ulegnie awarii.

Aranżacja jest zarządzany przez system do zachowania usługi wysokiej dostępności i nieprawidłowo zrównoważone.

`RunAsync()` nie zablokować synchronicznie. Implementacji RunAsync powinna zwracać zadanie lub oczekiwania na żadnych operacji długotrwałe lub blokowania, aby umożliwić środowiska wykonawczego kontynuować. Należy zwrócić uwagę w `while(true)` pętli w poprzednim przykładzie, umożliwiające zwracanie zadań `await Task.Delay()` jest używany. Jeśli obciążenie może zablokować synchronicznie, należy zaplanować nowe zadanie z `Task.Run()` w Twojej `RunAsync` implementacji.

Anulowanie obciążenie jest współpraca wysiłku, zorkiestrowana przez token anulowania podanego. System będzie oczekiwał na zadania zakończenia (przez pomyślne zakończenie anulowania lub fault), zanim przesyłane w. Ważne jest, aby uwzględnić token anulowania, Zakończ pracę i zamknąć `RunAsync()` tak szybko jak to możliwe, gdy system żąda anulowania.

W tym przykładzie usługi bezstanowej licznika są przechowywane w zmiennej lokalnej. Ale jest usługi bezstanowej, dlatego wartość przechowywana istnieje tylko dla bieżącego cyklu życia jego wystąpienia usługi. Gdy usługa przenosi lub ponownego uruchomienia, wartość jest utracone.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej
Sieć szkieletowa usług wprowadzono nowy rodzaj usługi, który jest obiektem stanowym. Usługa stanowa można zachować stanie niezawodnie w ramach usługi, wspólnie z kodem, który go używa. Stan dokonuje wysokiej dostępności sieci szkieletowej usług bez konieczności utrwalić stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanowej wysokiej dostępności i trwałe, nawet wtedy, gdy usługi są przenoszone lub ponownego uruchomienia, należy usługi stanowej.

W tym samym *HelloWorld* aplikacji, można dodać nową usługę prawym przyciskiem myszy w ramach usług odwołań w projekcie aplikacji i wybierając **Nowa usługa sieci szkieletowej usług -> Dodaj**.

![Dodawanie usługi do aplikacji sieci szkieletowej usług](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wybierz **.Net Core 2.0 -> Usługa Stateful** i nadaj mu nazwę *HelloWorldStateful*. Kliknij przycisk **OK**.

![Użyj okna dialogowego Nowy projekt do utworzenia nowej usługi stanowej sieci szkieletowej usług](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Aplikacja powinna mieć teraz dwie usługi: usługi bezstanowej *HelloWorldStateless* i usługi stanowej *HelloWorldStateful*.

Usługa stanowa ma tego samego punkty wejścia jako usługę bezstanową. Główna różnica polega na dostępność *dostawcy stanu* który niezawodne przechowywanie stanu. Sieć szkieletowa usług jest dostarczany z implementacja dostawcy stanu o nazwie [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md), która umożliwia tworzenie replikowane struktury danych za pośrednictwem niezawodnych menedżera stanu. Domyślnie usługa stanowa niezawodnej używa tego dostawcy stanu.

Otwórz **HelloWorldStateful.cs** w *HelloWorldStateful*, który zawiera następujące metodzie RunAsync:

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
`RunAsync()` działa podobnie, w usługach stanowe i bezstanowe. Jednak w usługi stanowej, platforma wykonuje dodatkowych działań w Twoim imieniu przed rozpoczęciem wykonywania `RunAsync()`. Te działania mogą obejmować zapewnienie, że Menedżer niezawodnej stanu i niezawodne kolekcji są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Kolekcje niezawodnych i niezawodne Menedżer stanu
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) jest implementacji słownik, który umożliwia niezawodne przechowywanie stanu usługi. Z sieci szkieletowej usług i niezawodne kolekcji można przechowywać danych bezpośrednio w usłudze bez konieczności zewnętrznych magazynu trwałego. Niezawodne kolekcje danych wysokiej dostępności. Sieć szkieletowa usług rozwiązanie to tworzenie i zarządzanie wieloma *replik* usługi dla Ciebie. Udostępnia również interfejs API, który abstracts optymalizacji złożoności Zarządzanie tych replik, a ich przejścia stanu.

Kolekcje niezawodnej może przechowywać żadnych typ architektury .NET, łącznie z niestandardowych typów, za pomocą paru ostrzeżenia:

* Usługa sieci szkieletowej udostępnia swój stan wysokiej przez *replikowanie* stanu między węzły i niezawodne kolekcje przechowywania danych na dysku lokalnym na każdej repliki. To oznacza, że wszystkie zasoby, które są przechowywane w niezawodnej kolekcje muszą zostać *serializacji*. Domyślnie używają niezawodnej kolekcje [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) serializacji, dlatego ważne jest, aby upewnić się, że Twoje typy są [obsługiwane przez serializator kontraktu danych](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) używając domyślnego elementu serializującego.
* Obiekty są replikowane wysokiej dostępności po zatwierdzeniu transakcji na niezawodne kolekcji. Obiekty przechowywane w niezawodnej kolekcje są przechowywane w lokalnej pamięci w usłudze. Oznacza to, że masz lokalnego odwołania do obiektu.
  
   Należy pamiętać, że nie zmodyfikować lokalnych wystąpień tych obiektów, bez wykonywania operacji update na niezawodne kolekcji w transakcji. Jest to spowodowane zmiany do lokalnego wystąpienia obiektów, nie będą automatycznie replikowane. Musisz ponownie wstawić obiekt do słownika lub użyj jednej z *aktualizacji* metody w słowniku.

Menedżer niezawodnej stanu zarządza niezawodnej kolekcji. Po prostu poproś niezawodnej Menedżer stanu niezawodnej kolekcji o nazwie w dowolnym momencie i w dowolnym miejscu w usłudze. Niezawodne Menedżer stanu zapewnia ponownie uzyskać odwołanie. Nie zaleca się zapisywania odwołań do kolekcji niezawodnej instancji w elemencie członkowskim klasy zmiennych lub właściwości. Szczególną uwagę należy upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usługi. Niezawodne Menedżer stanu obsługuje tę pracę za Ciebie i jest zoptymalizowany do powtarzania wizytach.

### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Kolekcje niezawodnej mają wiele operacji, która ich `System.Collections.Generic` i `System.Collections.Concurrent` odpowiednikami z wyjątkiem LINQ. Operacje na niezawodne kolekcje są asynchroniczne. Jest to spowodowane operacji zapisu z kolekcjami niezawodne wykonywanie operacji We/Wy do replikacji i utrwalić danych na dysku.

Są niezawodne operacje kolekcji *transakcyjnych*, dzięki czemu można zachować stanu spójne w wielu kolekcjach niezawodnej i operacji. Może na przykład usuwania z kolejki elementu roboczego z kolejką niezawodne, wykonaj operację i zapisać wynik w słowniku niezawodne, wszystkie w ramach pojedynczej transakcji. Jest ona traktowana jako operacją niepodzielną i gwarantuje, że cała operacja zostanie wykonana pomyślnie lub całej operacji cofnie. Jeśli błąd wystąpi po dequeue elementu, ale przed zapisaniem wynik, cała transakcja zostanie wycofana i element pozostaje w kolejce do przetworzenia.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Teraz zwróconych do *HelloWorld* aplikacji. Teraz możesz skompilować i wdrażanie usług. Po naciśnięciu **F5**, aplikacja zostanie utworzony i wdrożyć w klastrze lokalnym.

Po usługi uruchomione, można przejrzeć wygenerowane zdarzenia funkcji Śledzenie zdarzeń systemu Windows () w **zdarzeń diagnostycznych** okna. Należy pamiętać, że są wyświetlone zdarzenia z usługi bezstanowej i usługi stanowej w aplikacji. W przypadku wstrzymania strumienia, klikając **wstrzymać** przycisku. Następnie można sprawdzić szczegóły komunikatu rozwijając tej wiadomości.

> [!NOTE]
> Przed uruchomieniem aplikacji upewnij się, że masz lokalne działania projektowe klastra z systemem. Zapoznaj się z [Wprowadzenie — przewodnik](service-fabric-get-started.md) informacji na temat konfigurowania środowiska lokalnego.
> 
> 

![Wyświetl zdarzenia diagnostycznego w programie Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie aplikacji sieci szkieletowej usług w programie Visual Studio](service-fabric-debugging-your-application.md)

[Wprowadzenie: usług interfejsu API sieci Web sieci szkieletowej usług za pomocą hostingu samodzielnego OWIN](service-fabric-reliable-services-communication-webapi.md)

[Dowiedz się więcej o niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md)

[Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[Uaktualnienie aplikacji](service-fabric-application-upgrade.md)

[Dokumentacja dla deweloperów dla niezawodne usługi](https://msdn.microsoft.com/library/azure/dn706529.aspx)

