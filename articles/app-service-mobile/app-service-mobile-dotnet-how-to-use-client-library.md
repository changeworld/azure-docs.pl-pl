---
title: Praca z biblioteką klientów zarządzanych aplikacji usługi Mobile Apps (z systemem Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą klienta .NET dla usługi Azure App Service Mobile Apps w aplikacjach systemu Windows i Xamarin.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: f0f28d4b6573e4e6fecf0e6dd84814d4fc66cd60
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050502"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak używać zarządzanego klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu biblioteki zarządzanego klienta dla usługi Azure App Service Mobile aplikacji dla systemu Windows i aplikacje platformy Xamarin. Jeśli jesteś nowym użytkownikiem Mobile Apps, należy rozważyć wykonanie najpierw [szybkiego startu usługi Azure Mobile Apps] [ 1] samouczka. W tym przewodniku możemy skupić się na SDK zarządzanego klienta. Aby dowiedzieć się więcej o zestawów SDK po stronie serwera dla aplikacji mobilnych, zobacz dokumentację [.NET SDK serwera] [ 2] lub [Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacji zestawu SDK klienta jest zlokalizowana tutaj: [odwołania klienta usługi Azure Mobile Apps .NET][4].
Możesz również znaleźć kilka przykładów klienta w [repozytorium GitHub przykłady Azure][5].

## <a name="supported-platforms"></a>Obsługiwane platformy
Platforma .NET obsługuje następujące platformy:

* Dla systemu Xamarin Android wersje interfejsu API 19 do 24 (KitKat za pośrednictwem nugacie)
* Zwalnia systemu Xamarin iOS dla systemu iOS w wersji 8.0 lub nowszej
* Platforma uniwersalna systemu Windows
* Windows Phone 8.1
* Windows Phone 8.0, z wyjątkiem aplikacji Silverlight

Uwierzytelnianie "serwer flow" używa WebView przedstawioną interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie przedstawić WebView interfejsu użytkownika, potrzebne są inne metody uwierzytelniania.  Zestaw SDK w związku z tym nie jest odpowiedni dla typu czujki lub podobnie ograniczeniami urządzeń.

## <a name="setup"></a>Instalacji i wymagania wstępne
Przyjęto założenie, że możesz już utworzono i opublikowano projektu zaplecza aplikacji mobilnej, który zawiera co najmniej jedna tabela.  Kod używany w tym temacie, nosi nazwę tabeli `TodoItem` i ma następujące kolumny: `Id`, `Text`, i `Complete`. Ta tabela jest tej samej tabeli utworzone po zakończeniu [szybkiego startu usługi Azure Mobile Apps][1].

Odpowiedni typ maszynowy po stronie klienta w języku C# jest następującej klasy:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute] [ 6] służy do definiowania *PropertyName* mapowanie między pole klienta i pola w tabeli.

Informacje na temat tworzenia tabel w zapleczu swojej Mobile Apps, zobacz [temacie .NET SDK serwera] [ 7] lub [tematu Node.js Server SDK][8]. Jeśli utworzono zaplecza aplikacji mobilnej w portalu Azure przy użyciu opcji szybkiego startu, można również użyć **łatwe tabel** w [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Porady: Instalowanie pakietu SDK zarządzanego klienta
Użyj jednej z następujących metod można zainstalować pakietu SDK zarządzanego klienta dla aplikacji mobilnych z [NuGet][9]:

* **Visual Studio** kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj `Microsoft.Azure.Mobile.Client` pakietu, a następnie kliknij przycisk **zainstalować**.
* **Program Xamarin Studio** kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj** > **Dodawanie pakietów NuGet**, wyszukaj `Microsoft.Azure.Mobile.Client `pakietu, a następnie kliknij przycisk **Dodawanie pakietu** .

W pliku głównym działaniu, pamiętaj, aby dodać następujące **przy użyciu** instrukcji:

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Porady: Praca z symboli debugowania w programie Visual Studio
Symbole dla przestrzeni nazw Microsoft.Azure.Mobile są dostępne na [SymbolSource][10].  Zapoznaj się [instrukcje SymbolSource] [ 11] SymbolSource jest integrowana z programu Visual Studio.

## <a name="create-client"></a>Tworzenie klienta Mobile Apps
Poniższy kod tworzy [MobileServiceClient] [ 12] obiekt, który umożliwia dostęp do zaplecza aplikacji mobilnej.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

W powyższym kodzie Zamień `MOBILE_APP_URL` z adresem URL zaplecza aplikacji mobilnej, który znajduje się w bloku dla zaplecza aplikacji mobilnej w [Azure Portal]. Obiekt MobileServiceClient powinny być jako pojedyncza.

## <a name="work-with-tables"></a>Praca z tabelami
Poniższa sekcja zawiera szczegóły dotyczące jak wyszukiwanie i pobieranie rekordów i modyfikować danych w tabeli.  Omówiono następujące tematy:

* [Tworzenie odwołania do tabeli](#instantiating)
* [Dane zapytania](#querying)
* [Filtr zwrócił danych](#filtering)
* [Sortowanie zwrócone dane](#sorting)
* [Zwróć dane strony](#paging)
* [Wybrać określone kolumny](#selecting)
* [Wyszukaj według identyfikatora](#lookingup)
* [Dotyczących zapytań bez typu](#untypedqueries)
* [Wstawianie danych](#inserting)
* [Aktualizowanie danych](#updating)
* [Usuwanie danych](#deleting)
* [Rozwiązywanie konfliktów i optymistycznej współbieżności](#optimisticconcurrency)
* [Powiązanie z interfejsem użytkownika systemu Windows](#binding)
* [Zmiana rozmiaru strony](#pagesize)

### <a name="instantiating"></a>Porady: Tworzenie odwołania do tabeli
Kod, który uzyskuje dostęp do lub modyfikuje dane w tabeli wewnętrznej bazy danych wymaga funkcji na `MobileServiceTable` obiektu. Uzyskaj odwołanie do tabeli przez wywołanie metody [GetTable] metody, w następujący sposób:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Zwrócony obiekt korzysta z modelu typu serializacji. Obsługiwane jest również modelem serializacji bez typu. Poniższy przykład [tworzy odwołania do tabeli bez typu]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

W zapytaniach bez typu należy określić podstawowy ciąg zapytania OData.

### <a name="querying"></a>Porady: zapytania na danych z aplikacji mobilnej
W tej sekcji opisano sposób wysyłania zapytań do zaplecza aplikacji mobilnej, która obejmuje następujące funkcje:

* [Filtr zwrócił danych](#filtering)
* [Sortowanie zwrócone dane](#sorting)
* [Zwróć dane strony](#paging)
* [Wybrać określone kolumny](#selecting)
* [Wyszukiwać dane według Identyfikatora](#lookingup)

> [!NOTE]
> Rozmiar strony oparte na serwerze jest wymuszana aby uniemożliwić wszystkie wiersze zostały zwrócone.  Stronicowanie zachowuje żądań domyślny dla dużych zestawów danych z niekorzystnego wpływu na usługi.  Aby przywrócić więcej niż 50 wierszy, użyj `Skip` i `Take` metody, zgodnie z opisem w [zwrócić dane na stronach](#paging).

### <a name="filtering"></a>Porady: Filtr zwrócił danych
Poniższy kod ilustruje sposób filtrowania danych, umieszczając w niej `Where` klauzuli w zapytaniu. Zwraca wszystkie elementy z `todoTable` których `Complete` właściwości jest równa `false`. [Where] funkcja ma zastosowanie wiersza filtrowania predykatu do zapytania dotyczącego tabeli.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Możesz wyświetlić identyfikator URI żądania wysyłane do wewnętrznej bazy danych za pomocą komunikatów inspekcji oprogramowania, takich jak narzędzia dla deweloperów w przeglądarce lub [Fiddler]. Jeśli przyjrzymy się identyfikator URI żądania, zwróć uwagę zmodyfikowania ciągu kwerendy:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

To żądanie OData jest przekształcana na zapytanie SQL przez zestaw SDK serwera:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkcję, która została przekazana do `Where` metody może mieć dowolną liczbę warunków.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

W tym przykładzie będzie można przekształcić w kwerendzie SQL przez zestaw SDK serwera:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

To zapytanie również może zostać podzielony na wiele klauzul:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Te dwie metody są równoważne i mogą być używane zamiennie.  Opcja wcześniejsze&mdash;z łączenie wielu predykatów w jednym zapytaniu&mdash;jest bardziej compact i zalecanym.

`Where` Klauzuli obsługuje operacje, które można przetłumaczyć podzestawu OData. Dostępne są następujące operacje:

* Operatory relacyjne (==,! =, <, < =, >, > =),
* Operatory arytmetyczne (+, -, /, *, %),
* Liczba precision (Math.Floor —, Math.Ceiling)
* Funkcje ciągów (długość, Substring, Zamień, IndexOf, StartsWith, EndsWith)
* Data właściwości (rok, miesiąc, dzień, godzina, minuty, sekundy),
* Dostęp do właściwości obiektu i
* Każdej z tych operacji łączenia wyrażenia.

Rozważając Server SDK obsługuje, można rozważyć [OData v3 dokumentacji].

### <a name="sorting"></a>Porady: sortowanie zwrócone dane
Poniższy kod ilustruje sposób sortowania danych w tym [OrderBy] lub [OrderByDescending] funkcji w zapytaniu. Zwraca elementy z `todoTable` sortowane w kolejności rosnącej przez `Text` pola.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Porady: zwrócić dane na stronach
Domyślnie wewnętrznej bazy danych zwraca tylko pierwsze 50 wierszy. Można zwiększyć liczbę wierszy zwróconych przez wywołanie metody [Take] metody. Użyj `Take` wraz z [Skip] metodę, aby żądania dotyczące "page" całkowita zestawu danych zwróconych przez kwerendę. Następujące zapytanie po wykonaniu zwraca pierwszych trzech elementów w tabeli.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Następujące zapytanie poprawione pomija pierwszych trzech wyniki i zwraca wyniki trzech kolejnych. To zapytanie tworzy drugi "page" dane, których rozmiar strony jest trzy elementy.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] metoda żąda łączną liczbę dla *wszystkie* rekordów, które mogłyby być zwracane, ignorowanie klauzuli stronicowania/limit określony:

```
query = query.IncludeTotalCount();
```

W aplikacji rzeczywistych służy zapytania podobne jak w poprzednim przykładzie z formantem pagera lub porównywalny interfejsu użytkownika do przechodzenia między stronami.

> [!NOTE]
> Aby zastąpić limit 50 wiersza w zaplecza aplikacji mobilnej, również należy zastosować [EnableQueryAttribute] publicznie GET, metoda i Określ zachowanie stronicowania. Po zastosowaniu do metody poniżej maksymalny ustawiony zwrócone wiersze do 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Porady: Wybieranie określonych kolumn
Można określić, do uwzględnienia w wynikach, dodając zbiór właściwości [wybierz] klauzuli do zapytania. Na przykład poniższy kod przedstawia sposób wybierania tylko jednego pola, a także jak wybierz i format wielu pól:

```
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Wszystkie funkcje opisane dotąd są dodatku, dlatego firma Microsoft może zapewnić łańcucha je. Każde wywołanie łańcuchowa dotyczy jednego zapytania. Przykładem więcej:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Porady: wyszukiwanie danych według Identyfikatora
[LookupAsync] funkcja może służyć do wyszukiwania obiektów z bazy danych przy użyciu określonego identyfikatora.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Porady: wykonywanie zapytań bez typu
Podczas wykonywania zapytania za pomocą obiektu tabeli bez typu, należy jawnie określić parametry zapytania OData, wywołując [ReadAsync], jak w poniższym przykładzie:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Możesz odzyskać wartości JSON, które można używać jak zbioru właściwości. Aby uzyskać więcej informacji dotyczących JToken i Newtonsoft Json.NET, zobacz [Struktury Json.NET] lokacji.

### <a name="inserting"></a>Porady: wstawianie danych do zaplecza aplikacji mobilnej
Wszystkie typy klientów musi zawierać element członkowski o nazwie **identyfikator**, który jest domyślnie ciąg. To **identyfikator** jest wymagane do przeprowadzenia operacji CRUD i synchronizacji w trybie offline. Poniższy kod przedstawia sposób użycia [InsertAsync] metodę, aby wstawić nowe wiersze do tabeli. Parametr zawiera dane są wstawiane jako obiektu .NET.

```
await todoTable.InsertAsync(todoItem);
```

Jeśli wartość Unikatowy identyfikator niestandardową nie wchodzi w `todoItem` podczas wstawiania identyfikator GUID jest generowany przez serwer.
Możesz pobrać wygenerowanego identyfikatora, sprawdzając obiektu po wywołaniu zwraca.

Do wstawiania danych bez typu, może korzystać z Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Oto przykład przy użyciu adresu e-mail jako identyfikator unikatowy ciąg:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Praca z wartościami identyfikatorów
Aplikacje mobilne obsługuje niestandardowy ciąg unikatowe wartości w tabeli **identyfikator** kolumny. Wartość ciągu umożliwia aplikacjom użyć niestandardowej wartości, takie jak adresy e-mail lub nazwy użytkownika dla identyfikatora.  Identyfikatory ciągów dostarczyć następujące korzyści:

* Identyfikatory są generowane bez wprowadzania podróż do bazy danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości identyfikatorów można lepiej zintegrować z logiki aplikacji.

Jeśli wartość Identyfikatora ciągu nie jest ustawiony na wstawionego rekordu, zaplecza aplikacji mobilnej generuje unikatową wartość dla identyfikatora. Można użyć [Guid.NewGuid] do generowania własnych wartości Identyfikatora klienta albo w wewnętrznej bazie danych.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Porady: modyfikowanie danych w zaplecza aplikacji mobilnej
Poniższy kod przedstawia sposób użycia [UpdateAsync] metodę, aby zaktualizować istniejący rekord z tym samym Identyfikatorem nowymi informacjami. Parametr zawiera dane mają zostać zaktualizowane w obiektu .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Aby zaktualizować dane bez typu, użytkownik może skorzystać z [Struktury Json.NET] w następujący sposób:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

`id` Należy podać wartość pola podczas wprowadzania aktualizacji. Używa wewnętrznej bazy danych `id` pola do identyfikowania wiersza, który do aktualizacji. `id` Pola można uzyskać od wyniku `InsertAsync` wywołania. `ArgumentException` Jest zgłaszane, gdy zostanie podjęta próba zaktualizowania elementu bez podawania `id` wartość.

### <a name="deleting"></a>Porady: usuwanie danych zaplecza aplikacji mobilnej
Poniższy kod przedstawia sposób użycia [DeleteAsync] do usunięcia istniejącego wystąpienia. Wystąpienie jest identyfikowane przez `id` zestawie pól na `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Aby usunąć dane bez typu, możesz może korzystać z struktury Json.NET w następujący sposób:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Po wprowadzeniu żądanie usunięcia, należy określić identyfikator. Inne właściwości nie są przekazywane do usługi lub są ignorowane w usłudze. Wynik `DeleteAsync` wywołanie jest zwykle `null`. Identyfikator, który umożliwia przekazywanie można uzyskać od wyniku `InsertAsync` wywołania. A `MobileServiceInvalidOperationException` jest generowany podczas próby usunięcia elementu bez określania `id` pola.

### <a name="optimisticconcurrency"></a>Porady: użycie optymistycznej współbieżności do rozwiązywania konfliktów
Dwa lub więcej klientów może zapisać zmiany na tym samym elemencie w tym samym czasie. Bez wykrywania konfliktów ostatniego zapisu zastąpiłaby wszelkie poprzednie aktualizacje. **Optymistyczne sterowanie współbieżnością** założono, że każdej transakcji można zatwierdzić i dlatego nie używa żadnych zasobów blokowania.  Przed zatwierdzeniem transakcji, optymistyczne sterowanie współbieżnością sprawdza, czy nie inne transakcje nie zmienione dane. W przypadku modyfikacji danych przekazywanie transakcja zostanie wycofana.

Aplikacje mobilne obsługuje optymistyczne sterowanie współbieżnością za śledzenie zmian przy użyciu każdego elementu `version` kolumny właściwości systemu, który jest zdefiniowany dla każdej tabeli w zaplecza aplikacji mobilnej. Przy każdym aktualizacji rekordu Mobile Apps ustawia `version` właściwości dla tego rekordu na nową wartość. Podczas każdego żądania aktualizacji `version` właściwości rekordu dołączony do żądania jest porównywany z tą samą właściwością dla rekordu na serwerze. Jeśli wersja przekazany z żądania jest niezgodna z wewnętrznej bazy danych, a następnie wywołuje biblioteki klienta `MobileServicePreconditionFailedException<T>` wyjątku. Typ dołączonego wyjątek jest rekord z wewnętrznej bazy danych zawierającą wersję serwerów rekordu. Aplikacja może następnie dzięki tym informacjom można zdecydować, czy można wykonać żądania aktualizacji ponownie, podając poprawny `version` wartość z wewnętrznej bazy danych w celu zatwierdzenia zmian.

Zdefiniuj kolumny w klasie tabeli dla `version` właściwości systemu, aby włączyć optymistycznej współbieżności. Na przykład:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Aplikacje przy użyciu tabel bez typu Włącz optymistycznej współbieżności, ustawiając `Version` Flaga na `SystemProperties` tabeli w następujący sposób.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Oprócz włączenia optymistycznej współbieżności, musi również catch `MobileServicePreconditionFailedException<T>` wyjątek w kodzie podczas wywoływania metody [UpdateAsync].  Rozwiąż konflikt, stosując poprawny `version` zaktualizowany rekord i wywołanie [UpdateAsync] z rekordem rozwiązane. Poniższy kod przedstawia sposób rozwiązania konfliktu zapisu raz wykryto:

```
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Aby uzyskać więcej informacji, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps] tematu.

### <a name="binding"></a>Porady: powiązanie Mobile Apps danych interfejsu użytkownika systemu Windows
W tej sekcji przedstawiono sposób wyświetlania obiektów zwracanych danych za pomocą elementów interfejsu użytkownika w aplikacji systemu Windows.  Poniższy przykładowy kod wiąże źródła listy przy użyciu zapytania niezakończonych elementów. [MobileServiceCollection] tworzy kolekcję przenośnych aplikacji obsługujących powiązania.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Niektóre formanty w zarządzanego środowiska wykonawczego obsługują interfejs o nazwie [ISupportIncrementalLoading]. Ten interfejs umożliwia formantów zażądać dodatkowych danych, gdy użytkownik przewija. Jest wbudowaną obsługę ten interfejs dla uniwersalnych aplikacji systemu Windows za pośrednictwem [MobileServiceIncrementalLoadingCollection], który automatycznie obsługuje wywołania z kontrolki. Użyj `MobileServiceIncrementalLoadingCollection` w aplikacjach systemu Windows w następujący sposób:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Aby użyć nowej kolekcji na aplikacje dla systemu Windows Phone 8 i "Silverlight", użyj `ToCollection` metody rozszerzenia na `IMobileServiceTableQuery<T>` i `IMobileServiceTable<T>`. Aby załadować dane, należy wywołać `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Jeśli używasz kolekcji utworzona przez wywołanie metody `ToCollectionAsync` lub `ToCollection`, pobrać kolekcję, która może być powiązana z kontrolek interfejsu użytkownika.  Ta kolekcja jest obsługujący stronicowania.  Ponieważ kolekcja jest podczas ładowania danych z sieci, podczas ładowania czasami kończy się niepowodzeniem. Do obsługi tych błędów, należy zastąpić `OnException` metoda `MobileServiceIncrementalLoadingCollection` do obsługi wyjątków w wyniku wywołania `LoadMoreItemsAsync`.

Należy wziąć pod uwagę, jeśli ta tabela ma wiele pól, ale chcesz wyświetlić niektóre z nich w formantu. Wskazówki można używać w poprzedniej sekcji "[wybrać określone kolumny](#selecting)" Aby wybrać określone kolumny do wyświetlenia w interfejsie użytkownika.

### <a name="pagesize"></a>Zmień rozmiar strony
Aplikacje mobilne platformy Azure zwraca maksymalnie 50 elementów na żądanie domyślnie.  Aby zmienić rozmiar stronicowania, należy zwiększyć maksymalny rozmiar strony na kliencie i serwerze.  Aby zwiększyć rozmiar żądanej strony, określ `PullOptions` przy użyciu `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Zakładając, że zostały wykonane `PageSize` równa lub większa niż 100 w ramach serwera żądanie zwraca maksymalnie 100 elementów.

## <a name="#offlinesync"></a>Praca z tabelami w trybie Offline
Tabele w trybie offline używają lokalnych danych sklepu SQLite do użycia w trybie offline.  Tabela wszystkie operacje są wykonywane na lokalnym serwerze SQLite przechowywać zamiast magazynu na serwerze zdalnym.  Aby utworzyć tabelę w trybie offline, należy najpierw przygotować projektu:

1. W programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet rozwiązania...** , następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet dla wszystkich projektów w rozwiązaniu.
2. (Opcjonalnie) Do obsługi urządzeń z systemem Windows, należy zainstalować jedną z następujących pakietów środowiska uruchomieniowego SQLite:

   * **Środowisko uruchomieniowe Windows 8.1:** zainstalować [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** zainstalować [SQLite dla Windows Phone 8.1][4].
   * **Platforma uniwersalna systemu Windows** zainstalować [SQLite dla uniwersalnych systemu Windows][5].
3. (Opcjonalnie). Dla urządzeń z systemem Windows, kliknij przycisk **odwołania** > **Dodawanie odwołania...** , rozwiń węzeł **Windows** folder > **rozszerzenia**, następnie włączyć odpowiednie **SQLite dla systemu Windows** zestawu SDK oraz **2013 środowiska uruchomieniowego visual C++ dla systemu Windows** zestawu SDK.
    Nazwy zestawu SDK SQLite się nieco różnić z każdej z platform Windows.

Aby można było utworzyć odwołania do tabeli, należy przygotować magazynu lokalnego:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Zainicjowanie magazynu odbywa się normalnie natychmiast po utworzeniu klienta.  **OfflineDbPath** powinny być odpowiednie do użycia na wszystkich platformach obsługiwanych nazwę pliku.  Jeśli ścieżka jest w pełni kwalifikowana (to znaczy rozpoczyna się od ukośnika), zostanie użyta w tej ścieżce.  Jeśli ścieżka nie jest w pełni kwalifikowana, plik jest umieszczany w lokalizacji specyficzne dla platformy.

* Dla urządzeń iOS i Android domyślna ścieżka to folder "Osobiste pliki".
* W przypadku urządzeń z systemem Windows domyślna ścieżka to folder "AppData" specyficzne dla aplikacji.

Odwołanie do tabeli można uzyskać za pomocą `GetSyncTable<>` metody:

```
var table = client.GetSyncTable<TodoItem>();
```

Nie trzeba przeprowadzać uwierzytelnienia do użycia w trybie offline tabeli.  Wystarczy do uwierzytelniania, gdy komunikują się przy użyciu usługi wewnętrznej bazy danych.

### <a name="syncoffline"></a>Trwa synchronizowanie tabelę w trybie Offline
Tabele w trybie offline nie są zsynchronizowane z wewnętrznej bazy danych domyślnie.  Synchronizacja jest podzielony na dwie części.  Wypchnij zmiany oddzielnie pobierania nowych elementów.  Oto metoda typowe synchronizacji:

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Jeśli pierwszy argument `PullAsync` ma wartość null, synchronizacja przyrostowa nie będzie używana.  Każda operacja synchronizacji powoduje pobranie wszystkich rekordów.

Zestaw SDK wykonuje niejawny `PushAsync()` przed ściąganie rekordów.

Obsługa konflikt się dzieje na `PullAsync()` metody.  Konflikty mogą dotyczyć w taki sam sposób jak tabele online.  Konflikt jest generowany po `PullAsync()` jest wywoływana zamiast podczas insert, update lub delete. Jeśli wystąpi konflikt wiele, są one dołączone do pojedynczego MobileServicePushFailedException.  Obsługi każdego błędów oddzielnie.

## <a name="#customapi"></a>Praca z niestandardowego interfejsu API
Niestandardowy interfejs API umożliwia zdefiniowanie niestandardowe punkty końcowe, które udostępniają funkcje serwera które nie mapowania insert, update, usuwania lub operacja odczytu. Przy użyciu niestandardowego interfejsu API, może mieć większą kontrolę nad wiadomości, w tym odczytywanie ustawienie nagłówki komunikatów HTTP i Definiowanie formatu treści wiadomości innych niż JSON.

Niestandardowy interfejs API należy wywołać przez wywoływanie jednej z [InvokeApiAsync] metod na kliencie. Na przykład następujący wiersz kodu wysyła żądanie POST **completeAll** interfejsu API do wewnętrznej bazy danych:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ten formularz jest wywołanie metody typu i wymaga, aby **MarkAllResult** zwracany typ jest zdefiniowany. Obsługiwane są zarówno typu, jak i bez typu metody.

Metoda InvokeApiAsync() dołącza "/ api /" do interfejsu API, którą chcesz wywołać, chyba że interfejsu API, który rozpoczyna się od '/'.
Na przykład:

* `InvokeApiAsync("completeAll",...)` wywołuje /api/completeAll do wewnętrznej bazy danych
* `InvokeApiAsync("/.auth/me",...)` /.auth/me wywołania do wewnętrznej bazy danych

InvokeApiAsync służy do wywołań dowolnej WebAPI, łącznie z tymi WebAPIs, które nie są zdefiniowane z usługą Azure Mobile Apps.  Gdy używasz InvokeApiAsync() odpowiednie nagłówki, włącznie z nagłówkami uwierzytelniania są wysyłane z żądaniem.

## <a name="authentication"></a>Uwierzytelnianie użytkowników
Aplikacje mobilne obsługuje uwierzytelniania i autoryzacji użytkowników aplikacji przy użyciu różnych dostawców tożsamości zewnętrznych: Facebook, Google, Microsoft Account, Twitter i Azure Active Directory. Tabele, aby ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników można ustawić uprawnienia. Umożliwia także tożsamość uwierzytelnionych użytkowników do zaimplementowania reguły autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Dodawanie uwierzytelniania do aplikacji].

Obsługiwane są dwa przepływy uwierzytelniania: *zarządzanych przez klienta* i *serwer zarządzany* przepływu. Serwer zarządzany przepływu zapewnia najprostszą środowisko uwierzytelniania, zależy od interfejsu uwierzytelniania sieci web dostawcy. Przepływ zarządzanych przez klienta umożliwia lepszą integrację z funkcji specyficznych dla urządzenia zależy od zestawy SDK urządzenia specyficznego dla dostawcy.

> [!NOTE]
> Zalecamy używanie przepływem zarządzanych przez klienta w aplikacjach produkcyjnych.

Aby skonfigurować uwierzytelnianie, należy zarejestrować aplikację z co najmniej jednego dostawcy tożsamości.  Dostawca tożsamości generuje identyfikator klienta i klucz tajny klienta aplikacji.  Te wartości są ustawiane następnie w sieci wewnętrznej bazy danych, aby umożliwić usłudze Azure App Service uwierzytelniania/autoryzacji.  Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami szczegółowe samouczka [Dodawanie uwierzytelniania do aplikacji].

W tej sekcji omówiono następujące tematy:

* [Zarządzane przez klienta do uwierzytelniania](#clientflow)
* [Serwer zarządzany uwierzytelniania](#serverflow)
* [Buforowanie tokenu uwierzytelniania](#caching)

### <a name="clientflow"></a>Zarządzane przez klienta do uwierzytelniania
Aplikację można niezależnie skontaktuj się z dostawcą tożsamości, a następnie podaj token zwrócony podczas logowania, z poziomu zaplecza. Ten przepływ klienta umożliwia zapewnienie jednego środowiska logowania jednokrotnego dla użytkowników lub pobrać użytkownika dodatkowe dane dostawcy tożsamości. Klient przepływ uwierzytelniania jest preferowana względem przy użyciu przepływu serwera jako dostawca tożsamości zestawu SDK zawiera więcej natywnego działania środowiska użytkownika i zezwala na dodatkowe dostosowanie.

Przykłady są dostępne następujące wzorców uwierzytelniania przepływu klienta:

* [Biblioteki uwierzytelniania usługi Active Directory](#adal)
* [Facebook lub Google](#client-facebook)
* [Zestaw Live SDK](#client-livesdk)

#### <a name="adal"></a>Uwierzytelnianie użytkowników z biblioteki uwierzytelniania usługi Active Directory
Active Directory Authentication Library (ADAL) do uwierzytelniania użytkowników inicjować służy od klienta przy użyciu uwierzytelniania usługi Azure Active Directory.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w usłudze AAD, postępując [Konfigurowanie usługi aplikacji — dla nazwy logowania usługi Active Directory] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowania aplikację native client.
2. W programie Visual Studio lub Xamarin Studio Otwórz projekt i Dodaj odwołanie do `Microsoft.IdentityModel.CLients.ActiveDirectory` pakietu NuGet. Podczas wyszukiwania, obejmują wersje wstępne.
3. Dodaj następujący kod do aplikacji, zależnie od używanej platformy. W każdej wprowadź następujące elementy zastępcze:

   * Zastąp **INSERT urzędu tutaj** o nazwie dzierżawy, w którym są udostępniane aplikacji. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com. Tę wartość można skopiować na karcie domeny w usłudze Azure Active Directory w [Azure Portal].
   * Zastąp **Wstaw zasób — identyfikator-tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Możesz uzyskać identyfikator klienta z **zaawansowane** w obszarze **ustawień usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT klienta-ID-tutaj** z Identyfikatorem klienta, którego skopiowano aplikację native client.
   * Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, za pomocą schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*.

     Następujący kod wymagane dla każdej platformy:

     **System Windows:**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

     **Xamarin.iOS**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ```

#### <a name="client-facebook"></a>Logowanie jednokrotne przy użyciu tokenu z usługi Facebook lub Google
Można użyć przepływu klienta, jak pokazano w ta Wstawka kodu dla usługi Facebook lub Google.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

#### <a name="client-livesdk"></a>Jednokrotne przy użyciu Account Microsoft przy użyciu zestawu SDK na żywo
Do uwierzytelniania użytkowników, musisz zarejestrować aplikację w Centrum deweloperów konta Microsoft. Skonfiguruj szczegóły rejestracji na zaplecza aplikacji mobilnej. Aby utworzyć rejestrację konta Microsoft i podłącz go do zaplecza aplikacji mobilnej, wykonaj czynności opisane w [Rejestrowanie aplikacji do użycia logowania konta Microsoft]. Jeżeli masz zarówno Microsoft Store, jak i Windows Phone 8/Silverlight wersje aplikacji, należy najpierw zarejestrować wersję Microsoft Store.

Poniższy kod jest uwierzytelniany przy użyciu zestaw Live SDK i używa tokenu zwrócony logować się do zaplecza aplikacji mobilnej.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Microsoft Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [Zestaw SDK usługi Windows Live] dokumentacji.

### <a name="serverflow"></a>Serwer zarządzany uwierzytelniania
Po zarejestrowaniu dostawcy tożsamości, wywołaj [LoginAsync] metody w [MobileServiceClient] z [MobileServiceAuthenticationProvider] wartość dostawcy. Na przykład następujący kod inicjuje serwera przepływu logowania za pomocą usługi Facebook.

```
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, zmień wartość [MobileServiceAuthenticationProvider] wartości dla dostawcy.

W przepływie serwera usługi Azure App Service zarządza przepływ uwierzytelniania OAuth poprzez wyświetlenie strony logowania wybranego dostawcy.  Po zwraca dostawcy tożsamości, usługa aplikacji Azure generuje token uwierzytelniania usługi aplikacji. [LoginAsync] metoda zwraca [MobileServiceUser], który zapewnia zarówno [Nazwa użytkownika] uwierzytelnionego użytkownika i [MobileServiceAuthenticationToken], jako tokenu web JSON (JWT). Ten token można zapisać w pamięci podręcznej i ponownie go używać, dopóki nie wygaśnie. Aby uzyskać więcej informacji, zobacz [buforowania tokenu uwierzytelniania](#caching).

### <a name="caching"></a>Buforowanie tokenu uwierzytelniania
W niektórych przypadkach można uniknąć wywołanie do metody logowania po pierwszym pomyślnym uwierzytelnieniu przez zapisanie token uwierzytelniania od dostawcy.  Aplikacje Microsoft Store i platformy uniwersalnej systemu Windows mogą używać [PasswordVault] do buforowania tokenu uwierzytelniania bieżącego po pomyślnym zalogowaniu, w następujący sposób:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Wartość Nazwa użytkownika jest przechowywana jako nazwa użytkownika poświadczeń i token jest przechowywane jako hasło. Na kolejnych rozruchów, możesz sprawdzić **PasswordVault** dla buforowanych poświadczeń. W poniższym przykładzie użyto buforowanych poświadczeń, gdy zostaną znalezione, a w przeciwnym razie podejmuje próbę uwierzytelnienia z wewnętrznej bazy danych:

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Po zalogowaniu się użytkownika należy również usunąć przechowywanych poświadczeń w następujący sposób:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Użyj aplikacji platformy Xamarin [Xamarin.Auth] interfejsy API w celu bezpiecznego przechowywania poświadczeń w **konta** obiektu. Na przykład za pomocą tych interfejsów API, zobacz [AuthStore.cs] pliku kodu w [udostępniania próbki zdjęć ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Korzystając z uwierzytelniania zarządzanych przez klienta, można buforować tokenu dostępu uzyskane od dostawcy, takich jak Facebook lub Twitter. Token ten można podać, aby zażądać nowego tokenu uwierzytelniania z wewnętrznej bazy danych, w następujący sposób:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Powiadomienia wypychane
W poniższych tematach znajdziesz powiadomień wypychanych:

* [Zarejestruj się, aby powiadomienia wypychane](#register-for-push)
* [Uzyskaj identyfikator SID pakietu Microsoft Store](#package-sid)
* [Rejestrowanie przy użyciu szablonów i platform](#register-xplat)

### <a name="register-for-push"></a>Porady: rejestrowanie się w celu powiadomienia wypychane
Klient Mobile Apps umożliwia rejestrowanie do powiadomień wypychanych przy użyciu usługi Azure Notification Hubs. Podczas rejestrowania, można uzyskać uchwytu, którą można uzyskać z specyficzne dla platformy Push Notification Service (PNS). Podczas tworzenia rejestracji, następnie podaj tę wartość, wraz ze wszystkimi tagami. Poniższy kod rejestruje aplikację systemu Windows, aby powiadomienia wypychane z usługi powiadomień systemu Windows (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Jeśli push usługi WNS, musisz [pobrania identyfikatora SID pakietu Microsoft Store](#package-sid).  Aby uzyskać więcej informacji na aplikacje systemu Windows, w tym jak zarejestrować do rejestracji szablonów, zobacz [Dodawanie powiadomień wypychanych do aplikacji].

Żądanie tagi od klienta nie jest obsługiwana.  Tag żądań dyskretnie są usuwane z rejestracji.
Jeśli chcesz zarejestrować urządzenie z tagami, należy utworzyć niestandardowego interfejsu API, który używa interfejsu API centra powiadomień w celu przeprowadzenia rejestracji w Twoim imieniu.  [Wywołanie interfejsu API niestandardowe](#customapi) zamiast `RegisterNativeAsync()` metody.

### <a name="package-sid"></a>Porady: uzyskiwanie identyfikatora SID pakietu Microsoft Store
Identyfikator SID pakietu jest wymagany dla Włączanie powiadomień wypychanych w aplikacji Microsoft Store.  Aby uzyskać identyfikator SID pakietu, należy zarejestrować aplikację w firmie Microsoft Store.

Aby uzyskać tę wartość:

1. W Eksploratorze rozwiązań programu Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji Microsoft Store, kliknij przycisk **magazynu** > **Skojarz aplikację ze sklepem...** .
2. W kreatorze kliknij **dalej**, zaloguj się przy użyciu konta Microsoft, wpisz nazwę aplikacji w **Zarezerwuj nazwę nowej aplikacji**, następnie kliknij przycisk **rezerwy**.
3. Po pomyślnym utworzeniu rejestracji aplikacji, wybierz nazwę aplikacji, kliknij przycisk **dalej**, a następnie kliknij przycisk **skojarzyć**.
4. Zaloguj się do [Centrum deweloperów systemu Windows] przy użyciu Account firmy Microsoft. W obszarze **Moje aplikacje**, kliknij przycisk rejestracji aplikacji utworzony.
5. Kliknij przycisk **Zarządzanie aplikacjami** > **tożsamości aplikacji**, a następnie przewiń w dół, aby znaleźć użytkownika **identyfikatora SID pakietu**.

Wiele zastosowań identyfikator SID pakietu traktować go jako identyfikatora URI, w którym to przypadku należy użyć *ms-app: / /* schemat. Zwróć uwagę na wersję pakietu utworzonego przez łączenie tej wartości jako prefiks identyfikatora SID.

Aplikacje platformy Xamarin wymagają niektórych dodatkowy kod, aby można było zarejestrować aplikację z systemem iOS lub Android platform. Aby uzyskać więcej informacji zobacz temat dla danej platformy:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Porady: szablony wypychania rejestru do wysyłania powiadomień i platform
Aby zarejestrować szablonów, należy użyć `RegisterAsync()` metody za pomocą szablonów, w następujący sposób:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Szablony powinna być `JObject` typów i może zawierać wiele szablonów w następującym formacie JSON:

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

Metoda **RegisterAsync()** również akceptuje dodatkowej Kafelki:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Wszystkie tagi są odrzucane optymalizacji podczas rejestracji dla zabezpieczeń. Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz [Praca z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps].

Aby wysyłać powiadomienia przy użyciu tych szablonów w zarejestrowany, zapoznaj się [Interfejsy API centra powiadomień].

## <a name="misc"></a>Tematy dodatkowe
### <a name="errors"></a>Porady: obsługa błędów
Po wystąpieniu błędu w wewnętrznej bazie danych, klient SDK zgłasza `MobileServiceInvalidOperationException`.  Poniższy przykład przedstawia sposób obsługi wyjątku, który jest zwracany przez zaplecze:

```
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Innym przykładem dotyczącym warunki błędów można znaleźć w [Przykładowe pliki aplikacji mobilnych]. [LoggingHandler] przykładzie przedstawiono obsługi delegata rejestrowania do rejestrowania żądań wysyłanych do wewnętrznej bazy danych.

### <a name="headers"></a>Porady: dostosowywanie nagłówki żądań
Do obsługi danego scenariusza specyficzne dla aplikacji, może być konieczne dostosowanie komunikację z zaplecza aplikacji mobilnej. Na przykład można dodać niestandardowego nagłówka do wszystkich żądań wychodzących lub nawet zmienić kody stanu odpowiedzi. Można użyć niestandardowego [DelegatingHandler], jak w poniższym przykładzie:

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Dodawanie uwierzytelniania do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Dodawanie powiadomień wypychanych do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Rejestrowanie aplikacji do użycia logowania konta Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurowanie usługi aplikacji — dla nazwy logowania usługi Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[tworzy odwołania do tabeli bez typu]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Wybierz]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Nazwa użytkownika]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum deweloperów systemu Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Zestaw SDK usługi Windows Live]: https://msdn.microsoft.com/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Interfejsy API centra powiadomień]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Przykładowe pliki aplikacji mobilnych]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentacji]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Struktury Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
