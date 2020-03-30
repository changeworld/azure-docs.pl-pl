---
title: 'Tworzenie pierwszej aplikacji sieci szkieletowej usług w języku C #'
description: Wprowadzenie do tworzenia aplikacji sieci szkieletowej usług Microsoft Azure z usługami bezstanowymi i stanowymi.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083774"
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services

> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)

Aplikacja sieci szkieletowej usługi Azure zawiera co najmniej jedną usługę, która uruchamia kod. W tym przewodniku pokazano, jak tworzyć aplikacje sieci szkieletowej usług bezstanowych i stanowych za pomocą [niezawodnych usług.](service-fabric-reliable-services-introduction.md)  

## <a name="basic-concepts"></a>Podstawowe pojęcia

Aby rozpocząć korzystanie z usług reliable services, wystarczy zrozumieć tylko kilka podstawowych pojęć:

* **Typ usługi:** Jest to implementacja usługi. Jest zdefiniowany przez klasę, którą `StatelessService` piszesz, która rozszerza i wszelkie inne kody lub zależności używane w nim, wraz z nazwą i numerem wersji.
* **Wystąpienie usługi o nazwie:** Aby uruchomić usługę, należy utworzyć nazwane wystąpienia typu usługi, podobnie jak tworzenie wystąpień obiektów typu klasy. Wystąpienie usługi ma nazwę w postaci identyfikatora URI przy użyciu "fabric:/" schemat, taki jak "fabric:/MyApp/MyService".
* **Host usługi:** wystąpienia usługi o nazwie, które tworzysz, muszą być uruchamiane wewnątrz procesu hosta. Host usługi jest tylko procesem, w którym można uruchomić wystąpienia usługi.
* **Rejestracja usługi**: Rejestracja łączy wszystko. Typ usługi musi być zarejestrowany w czasie wykonywania sieci szkieletowej usług w hoście usług, aby umożliwić sieci szkieletowej usług do tworzenia wystąpień go uruchomić.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej

Usługa bezstanowa jest rodzajem usługi, która jest obecnie normą w aplikacjach w chmurze. Jest uważany za bezstanowy, ponieważ sama usługa nie zawiera danych, które muszą być przechowywane niezawodnie lub udostępniane jako wysoce dostępne. Jeśli wystąpienie usługi bezstanowej zostanie zamknięte, cały jej stan wewnętrzny zostanie utracony. W tej usługi stan musi być utrwalony do magazynu zewnętrznego, takich jak tabele platformy Azure lub bazy danych SQL, aby były wysoce dostępne i niezawodne.

Uruchom program Visual Studio 2017 lub Visual Studio 2019 jako administrator i utwórz nowy projekt aplikacji sieci szkieletowej usług o nazwie *HelloWorld:*

![Tworzenie nowej aplikacji sieci szkieletowej usługi za pomocą okna dialogowego Nowy projekt](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Następnie utwórz projekt usługi bezstanowej przy użyciu **platformy .NET Core 2.0** o nazwie *HelloWorldStateless*:

![W drugim oknie dialogowym utwórz projekt usługi bezstanowej](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Rozwiązanie zawiera teraz dwa projekty:

* *HelloWorld*. Jest to projekt *aplikacji,* który zawiera *usługi*. Zawiera również manifest aplikacji, który opisuje aplikację, a także wiele skryptów programu PowerShell, które ułatwiają wdrożenie aplikacji.
* *HelloWorldStateless*. Jest to projekt usługi. Zawiera implementację usługi bezstanowej.

## <a name="implement-the-service"></a>Zaimplementuj usługę

Otwórz plik **HelloWorldStateless.cs** w projekcie usługi. W sieci szkieletowej usług usługa może uruchomić dowolną logikę biznesową. Interfejs API usługi udostępnia dwa punkty wejścia dla kodu:

* Metoda otwartego punktu wejścia o nazwie *RunAsync*, w której można rozpocząć wykonywanie wszelkich obciążeń, w tym długotrwałych obciążeń obliczeniowych.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Punkt wejścia komunikacji, w którym można podłączyć wybrany stos komunikacji, na przykład ASP.NET Core. W tym miejscu można rozpocząć odbieranie żądań od użytkowników i innych usług.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

W tym samouczku skupimy `RunAsync()` się na metodzie punktu wejścia. W tym miejscu można natychmiast rozpocząć uruchamianie kodu.
Szablon projektu zawiera przykładową `RunAsync()` implementację tej liczby przyrostów liczby stopniowej.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat pracy ze stosem komunikacji, zobacz [Komunikacja usługi z ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync ( RunAsync )

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

Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczane i gotowe do wykonania. Dla usługi bezstanowej oznacza to po prostu, gdy wystąpienie usługi jest otwarty. Token anulowania jest udostępniany w celu koordynowania, gdy wystąpienie usługi musi zostać zamknięte. W sieci szkieletowej usług ten cykl otwierania/zamykania wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć z różnych powodów, w tym:

* System przenosi wystąpienia usługi do równoważenia zasobów.
* Błędy występują w kodzie.
* Aplikacja lub system zostanie uaktualniony.
* Podstawowy sprzęt doświadcza awarii.

Ta aranżacja jest zarządzana przez system, aby zapewnić wysoką dostępność i równowagę usługi.

`RunAsync()`nie powinny blokować synchronicznie. Implementacja RunAsync należy zwrócić zadanie lub czekać na wszelkie długotrwałe lub blokowanie operacji, aby umożliwić środowisko uruchomieniowe, aby kontynuować. Uwaga w `while(true)` pętli w poprzednim przykładzie jest `await Task.Delay()` używany zwracany zadania. Jeśli obciążenie musi blokować synchronicznie, należy zaplanować nowe zadanie w `Task.Run()` `RunAsync` implementacji.

Anulowanie obciążenia jest wysiłkiem współpracy zaaranżowanym przez dostarczony token anulowania. System będzie czekać na zakończenie zadania (przez pomyślne zakończenie, anulowanie lub błąd), zanim przejdzie dalej. Ważne jest, aby honorować token anulowania, `RunAsync()` zakończyć wszelkie prace i zakończyć tak szybko, jak to możliwe, gdy system żąda anulowania.

W tym przykładzie usługi bezstanowej liczba jest przechowywana w zmiennej lokalnej. Ale ponieważ jest to usługa bezstanowa, wartość, która jest przechowywana istnieje tylko dla bieżącego cyklu życia jego wystąpienia usługi. Gdy usługa zostanie przesunięta lub ponownie uruchomiona, wartość zostanie utracona.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej

Usługa Sieci szkieletowej wprowadza nowy rodzaj usługi, która jest stanowa. Usługa stanowa może niezawodnie utrzymywać stan w obrębie samej usługi, znajdując się z kodem, który go używa. Stan jest udostępniany przez sieci szkieletowej usług bez konieczności utrwalania stanu do magazynu zewnętrznego.

Aby przekonwertować wartość licznika z bezstanowej na wysoce dostępne i trwałe, nawet wtedy, gdy usługa przenosi lub uruchamia ponownie, potrzebujesz usługi stanowej.

W tej samej aplikacji *HelloWorld* można dodać nową usługę, klikając prawym przyciskiem myszy odwołania do usług w projekcie aplikacji i wybierając **pozycję Dodaj -> usługę nowej sieci szkieletowej usług.**

![Dodawanie usługi do aplikacji sieci szkieletowej usług](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wybierz **opcję .NET Core 2.0 -> Stateful Service** i nazwij ją *HelloWorldStateful*. Kliknij przycisk **OK**.

![Tworzenie nowej usługi stanowej sieci szkieletowej usług za pomocą okna dialogowego Nowy projekt](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Aplikacja powinna mieć teraz dwie usługi: bezstanowej usługi *HelloWorldStateless* i usługi stanowej *HelloWorldStateful*.

Usługa stanowa ma takie same punkty wejścia jak usługa bezstanowa. Główną różnicą jest dostępność *dostawcy stanu,* który może niezawodnie przechowywać stan. Sieć szkieletowa usług jest wyposażona w implementację dostawcy stanu o nazwie [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), która umożliwia tworzenie replikowanych struktur danych za pośrednictwem Menedżera stanu niezawodnego. Stanowa usługa niezawodna domyślnie używa tego dostawcy stanu.

Otwórz **HelloWorldStateful.cs** w *HelloWorldStateful*, który zawiera następującą metodę RunAsync:

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

### <a name="runasync"></a>RunAsync ( RunAsync )

`RunAsync()`działa podobnie w służbach stanowych i bezpaństwowych. Jednak w usłudze stanowej platforma wykonuje dodatkową pracę w Twoim `RunAsync()`imieniu przed wykonaniem . Ta praca może obejmować zapewnienie, że menedżer stanu niezawodne i niezawodne kolekcje są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i niezawodny menedżer stanu

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) jest implementacją słownika, której można użyć do niezawodnego przechowywania stanu w usłudze. Dzięki sieci szkieletowej usług i niezawodnym kolekcjom można przechowywać dane bezpośrednio w usłudze bez konieczności korzystania z zewnętrznego magazynu trwałego. Niezawodne kolekcje sprawiają, że twoje dane są wysoce dostępne. Sieci szkieletowej usług realizuje to, tworząc i zarządzając wieloma *replikami* usługi dla Ciebie. Zapewnia również interfejs API, który abstrakcje od złożoności zarządzania tymi replikami i ich przejścia stanu.

Niezawodne kolekcje mogą przechowywać dowolny typ platformy .NET, w tym typy niestandardowe, z kilkoma zastrzeżeniami:

* Sieć szkieletowa usług sprawia, że stan jest wysoce dostępny przez *replikowanie* stanu między węzłami, a niezawodne kolekcje przechowują dane na dysku lokalnym na każdej replice. Oznacza to, że wszystko, co jest przechowywane w niezawodne kolekcje muszą być *serializable*. Domyślnie niezawodne kolekcje używają [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) do serializacji, dlatego ważne jest, aby upewnić się, że typy są [obsługiwane przez serializator kontraktu danych](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) podczas korzystania z domyślnego serializatora.
* Obiekty są replikowane dla wysokiej dostępności podczas zatwierdzania transakcji w niezawodnych kolekcjach. Obiekty przechowywane w niezawodne kolekcje są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalne odwołanie do obiektu.
  
   Ważne jest, aby nie mutować wystąpień lokalnych tych obiektów bez wykonywania operacji aktualizacji na niezawodnej kolekcji w transakcji. Dzieje się tak, ponieważ zmiany w lokalnych wystąpieniach obiektów nie będą replikowane automatycznie. Należy ponownie wstawić obiekt z powrotem do słownika lub użyć jednej z metod *aktualizacji* w słowniku.

Menedżer stanu niezawodne zarządza niezawodne kolekcje dla Ciebie. Można po prostu poprosić Menedżera stanu niezawodne dla niezawodnej kolekcji według nazwy w dowolnym momencie i w dowolnym miejscu w usłudze. Menedżer stanu niezawodne zapewnia, że otrzymasz odwołanie z powrotem. Nie zaleca się zapisywania odwołań do wystąpienia kolekcji niezawodne w zmiennych elementów członkowskich klasy lub właściwości. Należy zwrócić szczególną uwagę na to, aby odwołanie było ustawione na wystąpienie przez cały czas w cyklu życia usługi. Menedżer stanu niezawodnego obsługuje tę pracę za Ciebie i jest zoptymalizowany pod kątem ponownych wizyt.

### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Niezawodne kolekcje mają wiele takich `System.Collections.Generic` `System.Collections.Concurrent` samych operacji, które robią ich i odpowiedniki, z wyjątkiem zintegrowanego zapytania języka (LINQ). Operacje na niezawodne kolekcje są asynchroniczne. Dzieje się tak, ponieważ operacje zapisu za pomocą niezawodnych kolekcji wykonują operacje we/wy w celu replikacji i utrwalania danych na dysku.

Operacje niezawodnej kolekcji są *transakcyjne,* dzięki czemu można zachować spójne stan w wielu niezawodnych kolekcji i operacji. Na przykład można odliczyć element pracy z kolejki niezawodne, wykonać operację na nim i zapisać wynik w słowniku niezawodne, wszystko w ramach jednej transakcji. Jest to traktowane jako operacja niepodzielna i gwarantuje, że cała operacja zakończy się pomyślnie lub cała operacja zostanie wycofana. Jeśli błąd wystąpi po usunięciu w kolejce elementu, ale przed zapisaniem wyniku, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Teraz wracamy do aplikacji *HelloWorld.* Teraz można tworzyć i wdrażać swoje usługi. Po naciśnięciu **klawisza F5**aplikacja zostanie sbudona i wdrożona w klastrze lokalnym.

Po uruchomieniu usług można wyświetlić wygenerowane zdarzenia śledzenia zdarzeń dla systemu Windows (ETW) w oknie **Zdarzenia diagnostyczne.** Należy zauważyć, że zdarzenia wyświetlane są z usługi bezstanowej i usługi stanowej w aplikacji. Strumień można wstrzymać, klikając przycisk **Wstrzymaj.** Następnie można sprawdzić szczegóły wiadomości, rozwiń tę wiadomość.

> [!NOTE]
> Przed uruchomieniem aplikacji upewnij się, że masz uruchomiony klaster lokalnego rozwoju. Zapoznaj się z [przewodnikiem o rozpoczęciu](service-fabric-get-started.md) pracy, aby uzyskać informacje na temat konfigurowania środowiska lokalnego.
> 
> 

![Wyświetlanie zdarzeń diagnostycznych w programie Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Następne kroki
[Debugowanie aplikacji sieci szkieletowej usług w programie Visual Studio](service-fabric-debugging-your-application.md)

[Wprowadzenie: usługi interfejsu API sieci Web usługi sieci Web usługi z hostingiem własnym OWIN](service-fabric-reliable-services-communication-webapi.md)

[Dowiedz się więcej o niezawodnych kolekcjach](service-fabric-reliable-services-reliable-collections.md)

[Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[Uaktualnienie aplikacji](service-fabric-application-upgrade.md)

[Informacje o deweloperze dla niezawodnych usług](https://msdn.microsoft.com/library/azure/dn706529.aspx)

