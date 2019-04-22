---
title: Praca z biblioteki klienta zarządzanego App Service Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki klienckiej platformy .NET dla usługi Azure App Service Mobile Apps za pomocą aplikacji Windows i Xamarin.
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
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 8f014f1cb40e1a629d1989f00805fc91015a3ae9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886016"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak używać zarządzanego klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą biblioteki klienta zarządzanego dla aplikacji usługi Azure Mobile usługi aplikacji dla Windows i aplikacje platformy Xamarin. Jeśli jesteś nowym użytkownikiem funkcji Mobile Apps, należy rozważyć najpierw przeczytanie [szybkiego startu usługi Azure Mobile Apps] [ 1] samouczka. W tym przewodniku skupimy się na zestawie SDK zarządzanego klienta. Aby dowiedzieć się więcej na temat zestawów SDK po stronie serwera dla aplikacji mobilnych, zajrzyj do dokumentacji [zestawem SDK .NET Server] [ 2] lub [zestaw Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dla klienta usługi SDK znajduje się tutaj: [Dokumentacja usługi Azure klienta platformy .NET usług Mobile Apps][4].
Możesz również znaleźć kilka przykładów klienta w [repozytorium GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Obsługiwane platformy
Platforma .NET obsługuje następujące platformy:

* Wersje platformy Xamarin Android API 19-24 (KitKat za pośrednictwem określić wersję Nougat)
* Wersje platformy Xamarin iOS dla systemu iOS w wersji 8.0 lub nowszej
* Platforma uniwersalna systemu Windows
* Windows Phone 8.1
* Windows Phone 8.0, z wyjątkiem aplikacji Silverlight

Uwierzytelnianie "serwera przepływ" używa widoku internetowego prezentowane interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie przedstawić interfejsu użytkownika widoku sieci Web, potrzebne są inne metody uwierzytelniania.  Ten zestaw SDK nie nadaje się zatem Obejrzyj type lub podobnie urządzeń z ograniczeniami.

## <a name="setup"></a>Instalacji i wymagania wstępne
Przyjęto założenie, że możesz mieć już utworzonych i opublikowanych projektu zaplecza aplikacji mobilnej, która zawiera co najmniej jedna tabela.  W kodzie, używaną w tym temacie, nosi nazwę tabeli `TodoItem` i ma następujące kolumny: `Id`, `Text`, i `Complete`. Ta tabela jest tej samej tabeli utworzone po zakończeniu [szybkiego startu usługi Azure Mobile Apps][1].

Odpowiedni typ klienta wpisane w języku C# jest następującej klasy:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute] [ 6] służy do definiowania *PropertyName* mapowanie między polem klienta i pola w tabeli.

Informacje na temat tworzenia tabel w kodzie zaplecza funkcji Mobile Apps, zobacz [temat zestawu SDK serwera .NET] [ 7] lub [temat Node.js Server SDK][8]. Jeśli utworzono zaplecza aplikacji mobilnej w witrynie Azure portal przy użyciu opcji szybkiego startu umożliwia także **łatwych tabel** w [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Instrukcje: Zainstaluj pakiet zestawu SDK klienta zarządzanego
Użyj jednej z następujących metod instalacji pakietu SDK zarządzanego klienta usługi Mobile Apps z [NuGet][9]:

* **Program Visual Studio** kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj `Microsoft.Azure.Mobile.Client` pakietu, a następnie kliknij przycisk **zainstalować**.
* **Program Xamarin Studio** kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj** > **Dodaj pakiety NuGet**, wyszukaj `Microsoft.Azure.Mobile.Client` pakietu, a następnie kliknij przycisk **Dodaj pakiet** .

W pliku głównym działaniu, pamiętaj, aby dodać następujące **przy użyciu** instrukcji:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt systemu Android, muszą mieć tę samą wersję. Zestaw SDK ma `Xamarin.Android.Support.CustomTabs` zależność dla platformy systemu Android, więc jeśli projekt używa nowszej obsługi pakietów można konieczne zainstalowanie tego pakietu przy użyciu wymaganej wersji bezpośrednio w celu uniknięcia konfliktów.

### <a name="symbolsource"></a>Jak: Praca z symbole debugowania w programie Visual Studio
Symbole dla przestrzeni nazw Microsoft.Azure.Mobile są dostępne na [SymbolSource][10].  Zapoznaj się [instrukcje SymbolSource] [ 11] zintegrować SymbolSource z programem Visual Studio.

## <a name="create-client"></a>Tworzenie klienta funkcji Mobile Apps
Poniższy kod tworzy [MobileServiceClient] [ 12] obiekt, który umożliwia dostęp do zaplecza aplikacji mobilnej.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

W poprzednim kodzie Zastąp `MOBILE_APP_URL` z adresem URL zaplecza aplikacji mobilnej, który znajduje się w bloku zaplecza aplikacji mobilnej w [Azure Portal]. Obiekt MobileServiceClient powinien być klasą pojedynczą.

## <a name="work-with-tables"></a>Praca z tabelami
W poniższej sekcji przedstawiono sposób wyszukiwania i pobierania rekordów i modyfikować dane w tabeli.  Omówiono następujące tematy:

* [Tworzenie odwołania do tabeli](#instantiating)
* [Wykonywanie zapytań dotyczących danych](#querying)
* [Filtruj dane zwrotne](#filtering)
* [Sortuj zwrócone dane](#sorting)
* [Zwróć dane na stronach](#paging)
* [Wybrać określone kolumny](#selecting)
* [Wyszukaj według identyfikatora](#lookingup)
* [Obsługa zapytań bez typu](#untypedqueries)
* [Wstawianie danych](#inserting)
* Aktualizowanie danych
* [Usuwanie danych](#deleting)
* [Rozwiązywanie konfliktów i optymistycznej współbieżności](#optimisticconcurrency)
* [Powiązanie z interfejsu użytkownika Windows](#binding)
* [Zmiana rozmiaru strony](#pagesize)

### <a name="instantiating"></a>Jak: Tworzenie odwołania do tabeli
Cały kod, który uzyskuje dostęp do lub modyfikuje dane w tabeli wewnętrznej bazy danych wywołuje funkcje na `MobileServiceTable` obiektu. Uzyskaj odwołanie do tabeli przez wywołanie metody [GetTable] metody w następujący sposób:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Zwrócony obiekt wykorzystuje model wpisane serializacji. Obsługiwane jest również model danych serializacji. Poniższy przykład [tworzy odwołanie do tabeli bez typu]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

W zapytaniach bez typu należy określić podstawowy ciągu zapytania OData.

### <a name="querying"></a>Jak: Wykonywanie zapytań dotyczących danych z aplikacji mobilnej
W tej sekcji opisano sposób wysyłania zapytań do zaplecza aplikacji mobilnej, która obejmuje następujące funkcje:

* [Filtruj dane zwrotne](#filtering)
* [Sortuj zwrócone dane](#sorting)
* [Zwróć dane na stronach](#paging)
* [Wybrać określone kolumny](#selecting)
* [Wyszukiwać dane według Identyfikatora](#lookingup)

> [!NOTE]
> Rozmiaru strony opartych na serwerze jest wymuszana, aby uniemożliwić wszystkie wiersze są zwracane.  Stronicowanie przechowuje żądania domyślnej dla dużych zestawów danych z niekorzystnego wpływu na usługi.  Aby zwrócić więcej niż 50 wierszy, użyj `Skip` i `Take` metodę, zgodnie z opisem w [zwracania danych na stronach](#paging).

### <a name="filtering"></a>Jak: Filtruj dane zwrotne
Poniższy kod ilustruje sposób filtrowania danych, umieszczając `Where` klauzuli w zapytaniu. Zwraca wszystkie elementy z `todoTable` którego `Complete` właściwości jest równa `false`. [Where] funkcja ma zastosowanie wiersz filtrowanie predykatu do wykonywania zapytań w odniesieniu do tabeli.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Możesz wyświetlić identyfikator URI żądania wysłanego do zaplecza za pomocą oprogramowania do kontroli wiadomości, takich jak narzędzia deweloperskie przeglądarki lub [Fiddler]. W przypadku identyfikatora URI żądania, zwróć uwagę, zmodyfikowania ciągu kwerendy:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

To żądanie OData przetłumaczyć kwerendę SQL przez zestaw SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkcja, która jest przekazywana do `Where` metoda może mieć dowolną liczbę warunków.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

W tym przykładzie będzie można przetłumaczyć w kwerendzie SQL przez zestaw SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

To zapytanie również może zostać podzielony na wiele klauzul:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Te dwie metody są równoważne i mogą być używane zamiennie.  Opcja wcześniejsze&mdash;z łączenia wielu predykaty w jednym zapytaniu&mdash;jest bardziej zwarty i zalecane.

`Where` Klauzuli obsługuje operacje, które można przetłumaczyć podzbiór protokołu OData. Operacje obejmują:

* Operatory relacyjne (==,! =, <, < =, >, > =),
* Operatory arytmetyczne (+, -, /, *, %),
* Liczba precyzji (Math.Floor —, Math.Ceiling)
* Funkcje ciągów (długość podciągu, Zastąp, IndexOf, StartsWith, EndsWith),
* Właściwości daty (rok, miesiąc, dzień, godzina, minuta, sekunda),
* Uzyskiwanie dostępu do właściwości obiektu, a
* Wyrażeń, łącząc każdej z tych operacji.

Rozważając Server SDK obsługuje, można rozważyć [Dokumentacja usługi OData v3].

### <a name="sorting"></a>Jak: Sortuj zwrócone dane
Poniższy kod ilustruje sposób sortowania danych, umieszczając [OrderBy] lub [OrderByDescending] funkcji w zapytaniu. Zwraca elementy z `todoTable` posortowane rosnąco `Text` pola.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Jak: Zwróć dane na stronach
Domyślnie zaplecze zwraca tylko 50 pierwszych wierszy. Możesz zwiększyć liczbę wierszy zwróconych przez wywołanie metody [Take] metody. Użyj `Take` wraz z [Skip] metodę, aby żądać określonych "page" całkowita zestawu danych zwróconych przez zapytanie. Następujące zapytanie po wykonaniu zwraca pierwszych trzech elementów w tabeli.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Następujące zapytanie poprawione pomija pierwszych trzech wyników i zwraca wyniki następne trzy. To zapytanie tworzy drugi "page" dane, których rozmiar strony jest trzy elementy.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] metoda żąda łączną liczbę dla *wszystkich* rekordy, które mogłyby zostały zwrócone, ignorowanie wszelkich klauzulę stronicowania/limitu:

```csharp
query = query.IncludeTotalCount();
```

W przypadku aplikacji rzeczywistych służy zapytania, podobnie jak w poprzednim przykładzie za pomocą kontroli pagera lub porównywalny interfejsu użytkownika do przechodzenia między stronami.

> [!NOTE]
> Aby zastąpić limit 50 wierszy w zaplecza aplikacji mobilnej, również należy zastosować [EnableQueryAttribute] publicznie metoda GET i określić zachowanie stronicowania. Po zastosowaniu do metody, poniżej maksymalny ustawiony zwrócone wiersze do 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Jak: Wybrać określone kolumny
Można określić, który zestaw właściwości do uwzględnienia w wynikach, dodając [wybierz] klauzuli do zapytania. Na przykład poniższy kod przedstawia, jak wybrać tylko jedno pole, a także jak należy wybierać i formatowanie wielu pól:

```csharp
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

Wszystkie funkcje opisane w do tej pory są addytywne, dzięki czemu firma Microsoft może zachować łańcucha je. Każde wywołanie łańcuchowych ma wpływ na jeden z zapytania. Przykładem więcej:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Jak: Wyszukiwać dane według Identyfikatora
[LookupAsync] funkcja może służyć do wyszukiwania obiektów z bazy danych za pomocą określonego identyfikatora.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Jak: Wykonywanie zapytań bez typu
Podczas wykonywania zapytania za pomocą obiektu tabeli bez typu, należy jawnie określić parametry zapytania OData, wywołując [ReadAsync], jak w poniższym przykładzie:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Możesz odzyskać wartości JSON, które można używać jak zbiór właściwości. Aby uzyskać więcej informacji na temat JToken i Newtonsoft Json.NET, zobacz [Json.NET] lokacji.

### <a name="inserting"></a>Jak: Wstawianie danych do zaplecza aplikacji mobilnej
Wszystkie typy klientów musi zawierać element o nazwie **identyfikator**, która jest domyślnie ciągu. To **identyfikator** jest wymagane do wykonywania operacji CRUD i synchronizacji w trybie offline. Poniższy kod ilustruje sposób używania [InsertAsync] metodę, aby wstawić nowe wiersze do tabeli. Parametr zawiera dane, które ma zostać wstawiony jako obiekt .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Jeśli nie są objęte niestandardowe wartość unikatowego Identyfikatora `todoItem` podczas wstawiania, identyfikator GUID jest generowany przez serwer.
Możesz pobrać wygenerowany identyfikator, sprawdzając obiektu po wywołaniu zwraca.

Aby wstawić dane bez typu, mogą korzystać ze struktury Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Oto przykład przy użyciu adresu e-mail jako identyfikator unikatowy ciąg:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Praca z wartościami Identyfikatora
Mobile Apps obsługuje wartości unikatowego ciągu niestandardowych w tabeli **identyfikator** kolumny. Wartość ciągu umożliwia aplikacjom używanie wartości niestandardowe, takie jak adresy e-mail lub nazwy użytkownika dla identyfikatora.  Identyfikatory ciągu zapewniają następujące korzyści:

* Identyfikatory są generowane, bez konieczności szukania komunikacji dwustronnej w bazie danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości identyfikatorów można zintegrować lepiej za pomocą aplikacji logiki.

Gdy wartość Identyfikatora ciągu nie jest ustawiona na wstawionego rekordu, zaplecza aplikacji mobilnej generuje unikatową wartość dla identyfikatora. Możesz użyć [Guid.NewGuid] metodę w celu wygenerowania własne wartości Identyfikatora klienta lub w wewnętrznej bazie danych.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Jak: Modyfikowanie danych w zaplecza aplikacji mobilnej
Poniższy kod ilustruje sposób używania [UpdateAsync] metodę, aby zaktualizować istniejący rekord o tym samym identyfikatorze o nowe informacje. Parametr zawiera dane, które mają zostać zaktualizowane w obiekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Do aktualizowania danych bez typu, użytkownik może skorzystać z [Json.NET] w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

`id` Należy określić pole, podczas wprowadzania aktualizacji. Używa wewnętrznej bazy danych `id` pola, aby zidentyfikować, które wiersza do zaktualizowania. `id` Pola można uzyskać z wynikiem `InsertAsync` wywołania. `ArgumentException` Jest wywoływane, jeśli próbujesz zaktualizować element bez podawania `id` wartość.

### <a name="deleting"></a>Jak: Usuń dane z zaplecza aplikacji mobilnej
Poniższy kod ilustruje sposób używania [DeleteAsync] metodę, aby usunąć istniejącego wystąpienia. Wystąpienie jest identyfikowany przez `id` zestawie pól na `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Usuwanie danych bez typu, mogą korzystać ze struktury Json.NET w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Po wprowadzeniu żądanie usunięcia, należy określić identyfikator. Inne właściwości nie zostaną przekazane do usługi lub są ignorowane na usługę. Wynik `DeleteAsync` wywołanie jest zwykle `null`. Identyfikator Aby przekazać można uzyskać z wynikiem `InsertAsync` wywołania. A `MobileServiceInvalidOperationException` jest zgłaszany, gdy spróbujesz usunąć element bez określania `id` pola.

### <a name="optimisticconcurrency"></a>Jak: Używaj optymistycznej współbieżności na potrzeby rozwiązywania konfliktów
Co najmniej dwóch klientów mogą zapisywać zmian do tego samego elementu w tym samym czasie. Bez wykrywanie konfliktów zapisana jako ostatnia spowodowałoby zastąpienie wszelkich poprzednich aktualizacji. **Mechanizmu kontroli optymistycznej współbieżności** zakłada, że każda transakcja zatwierdzić i dlatego nie korzysta z żadnych blokowanie zasobów.  Przed zatwierdzeniem transakcji, mechanizmu kontroli optymistycznej współbieżności sprawdza, czy nie inne transakcje nie zmienione dane. Jeśli dane zostały zmodyfikowane, przekazywanie transakcja zostanie wycofana.

Mobile Apps obsługuje mechanizmu kontroli optymistycznej współbieżności, śledzenie zmian przy użyciu każdego elementu `version` kolumny właściwości systemu, która jest zdefiniowana dla każdej tabeli w zaplecza aplikacji mobilnej. Przy każdym rekord zostanie zaktualizowany, aplikacje mobilne ustawia `version` właściwości dla tego rekordu na nową wartość. Podczas każdego żądania aktualizacji `version` właściwości rekordu uwzględnione w żądaniu jest porównywany z tej samej właściwości dla rekordu na serwerze. Jeśli wersja przekazany z żądania jest niezgodna z wewnętrznej bazy danych, a następnie generuje biblioteki klienckiej `MobileServicePreconditionFailedException<T>` wyjątku. Typ dołączone do tego wyjątku jest rekord z wewnętrznej bazy danych, zawierającą serwery wersję rekordu. Aplikacja może użycie tych informacji do określenia, czy można wykonać żądania aktualizacji ponownie z prawidłowymi `version` wartość z wewnętrznej bazy danych, aby zatwierdzić zmiany.

Definiowanie kolumny na klasie tabel dla `version` właściwości systemu, aby umożliwić optymistycznej współbieżności. Na przykład:

```csharp
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

Aplikacje przy użyciu tabel bez typu Włącz optymistycznej współbieżności, ustawiając `Version` flagą `SystemProperties` tabeli w następujący sposób.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Oprócz włączenia optymistycznej współbieżności, należy również przechwytywać `MobileServicePreconditionFailedException<T>` wyjątków w kodzie podczas wywoływania [UpdateAsync].  Rozwiąż konflikt, stosując poprawny `version` zaktualizowanym rekordem i wywołania [UpdateAsync] z rekordem rozwiązane. Poniższy kod przedstawia sposób rozwiązania, gdy wykryto konflikt podczas zapisywania:

```csharp
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

### <a name="binding"></a>Jak: Powiąż dane funkcji Mobile Apps do interfejsu użytkownika Windows
W tej sekcji przedstawiono sposób wyświetlania obiektów zwróconych danych za pomocą elementów interfejsu użytkownika w aplikacji Windows.  Poniższy przykład kodu tworzy powiązanie z źródła listy przy użyciu zapytania niezakończonych elementów. [MobileServiceCollection] tworzy kolekcję mobilnych aplikacji obsługującej powiązanie.

```csharp
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

Niektóre formanty w zarządzanym środowisku uruchomieniowym obsługują interfejs o nazwie [ISupportIncrementalLoading]. Ten interfejs umożliwia formantów zażądać dodatkowych danych, gdy użytkownik przewija widok. Brak wbudowanej obsługi dla tego interfejsu dla uniwersalnych aplikacji dla Windows za pośrednictwem [MobileServiceIncrementalLoadingCollection], który automatycznie obsługuje wywołania z kontrolki. Użyj `MobileServiceIncrementalLoadingCollection` w aplikacjach Windows w następujący sposób:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Aby użyć nowej kolekcji dla aplikacji Windows Phone 8 i "Silverlight", użyj `ToCollection` metod rozszerzenia `IMobileServiceTableQuery<T>` i `IMobileServiceTable<T>`. Aby załadować dane, należy wywołać `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Gdy używasz kolekcji utworzonej przez wywołanie metody `ToCollectionAsync` lub `ToCollection`, Pobierz kolekcję, która może być powiązana z kontrolek interfejsu użytkownika.  Ta kolekcja jest obsługujących stronicowania.  Ponieważ kolekcja jest ładowanie danych z sieci, podczas ładowania czasami kończy się niepowodzeniem. Aby obsłużyć takie błędy, należy zastąpić `OnException` metody `MobileServiceIncrementalLoadingCollection` do obsługi wyjątków, wynikające z wywołania `LoadMoreItemsAsync`.

Należy wziąć pod uwagę, jeśli tabela ma wiele pól, ale tylko mają być wyświetlane niektóre z nich w kontrolce. Można użyć wskazówki zawarte w poprzedniej sekcji "[wybrać określone kolumny](#selecting)" Aby wybrać określone kolumny do wyświetlenia w interfejsie użytkownika.

### <a name="pagesize"></a>Zmień rozmiar strony
Domyślnie usługa Azure Mobile Apps zwraca maksymalnie 50 elementów na żądanie.  Możesz zmienić rozmiar stronicowania przez zwiększenie maksymalnego rozmiaru strony na kliencie i serwerze.  Aby zwiększyć rozmiar żądanej strony, należy określić `PullOptions` przy użyciu `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Zakładając, że wprowadzono `PageSize` równy lub większy niż 100 na serwerze, żądanie zwraca elementy do 100.

## <a name="#offlinesync"></a>Praca z tabelami w trybie Offline
Tabele w trybie offline za pomocą danych lokalnych do magazynu do przechowywania bazy danych SQLite Aby używać w trybie offline.  Tabela wszystkie operacje są wykonywane na lokalnym serwerze bazy danych SQLite przechowywać zamiast magazynu zdalnego serwera.  Aby utworzyć tabelę w trybie offline, należy najpierw przygotować projektu:

1. W programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet dla rozwiązania...** , a następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakiet NuGet dla wszystkich projektów w rozwiązaniu.
2. (Opcjonalnie) Do obsługi urządzeń Windows, należy zainstalować jedną z następujących pakietów dla środowiska uruchomieniowego bazy danych SQLite:

   * **Środowisko wykonawcze Windows 8.1:** Zainstaluj [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** Zainstaluj [SQLite dla Windows Phone 8.1][4].
   * **Platforma Universal Windows** zainstalować [SQLite for Universal Windows][5].
3. (Opcjonalnie). W przypadku urządzeń Windows, kliknij przycisk **odwołania** > **Dodaj odwołanie...** , rozwiń węzeł **Windows** folder > **rozszerzenia**, następnie włączyć odpowiednie **SQLite dla Windows** zestawu SDK oraz **2013 środowiska uruchomieniowego visual C++ dla Windows** zestawu SDK.
    Nazwy zestawu SDK bazy danych SQLite różnią się nieco z każdej z platform Windows.

Przed utworzeniem odwołania do tabeli, należy przygotować Magazyn lokalny:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicjowanie Store odbywa się normalnie, natychmiast, po utworzeniu klienta.  **OfflineDbPath** powinna być nazwa pliku odpowiedni do użytku na wszystkich platformach, które obsługujesz.  Jeśli ścieżka jest w pełni kwalifikowana (czyli zaczyna się ukośnikiem), zostanie użyta w tej ścieżce.  Jeśli ścieżka nie jest w pełni kwalifikowana, plik zostanie umieszczony w lokalizacji określonej platformy.

* Dla systemów iOS i urządzenia z systemem Android ścieżka domyślna to folder "Pliki osobiste".
* W przypadku urządzeń Windows ścieżka domyślna to folder "AppData" specyficzne dla aplikacji.

Odwołania do tabeli można uzyskać za pomocą `GetSyncTable<>` metody:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Nie musisz uwierzytelnić się w trybie offline tabeli.  Wystarczy do uwierzytelniania, gdy komunikują się z usługą zaplecza.

### <a name="syncoffline"></a>Synchronizacja Offline tabeli
Domyślnie tabele w trybie offline nie są zsynchronizowane z wewnętrznej bazy danych.  Synchronizacja jest podzielona na dwie części.  Wypchnij zmiany oddzielnie pobierania nowych elementów.  Poniżej przedstawiono metody typowego synchronizacji:

```csharp
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

Jeśli pierwszy argument `PullAsync` ma wartość null, a następnie synchronizacja przyrostowa nie jest używany.  Każda operacja synchronizacji umożliwia pobranie wszystkich rekordów.

Zestaw SDK wykonuje niejawne `PushAsync()` przed rekordów.

Obsługa konfliktów ma miejsce na `PullAsync()` metody.  Poradzenie sobie z konfliktami w taki sam sposób jak tabele w trybie online.  Konflikt jest generowany podczas `PullAsync()` jest wywoływana zamiast podczas insert, update lub delete. Jeśli wystąpi konflikt wielu, są one połączone w MobileServicePushFailedException jednego.  Obsługuj osobno każdy błąd.

## <a name="#customapi"></a>Praca z niestandardowego interfejsu API
Niestandardowy interfejs API umożliwia zdefiniowanie niestandardowe punkty końcowe, które udostępniają funkcje serwera, które nie mapowanie do wstawiania, aktualizowania, usuwania lub operacja odczytu. Korzystając z niestandardowego interfejsu API, może mieć większą kontrolę nad wiadomości, w tym odczytywanie i ustawiania nagłówków wiadomości HTTP oraz definiowania format treści wiadomości, innej niż JSON.

Wywoływanie niestandardowego interfejsu API, wywołując jedną z [InvokeApiAsync] metody na kliencie. Na przykład następujący wiersz kodu wysyła żądanie POST do **completeAll** interfejsu API zaplecza:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ta forma jest wywołaniem wpisanej metody i wymaga **MarkAllResult** zwracać typ jest zdefiniowany. Typizowane i nietypizowane metody są obsługiwane.

Metoda InvokeApiAsync() dołącza "/ api /" interfejsu API, który chcesz wywołać, chyba że interfejsu API, który rozpoczyna się od "/".
Na przykład:

* `InvokeApiAsync("completeAll",...)` wywołuje /api/completeAll do wewnętrznej bazy danych
* `InvokeApiAsync("/.auth/me",...)` wywołuje /.auth/me do wewnętrznej bazy danych

InvokeApiAsync umożliwia wywołanie dowolnego WebAPI, łącznie z tymi WebAPIs, które nie zostały zdefiniowane przy użyciu usługi Azure Mobile Apps.  Gdy używasz InvokeApiAsync(), odpowiednie nagłówki, włącznie z nagłówkami uwierzytelniania są wysyłane z żądaniem.

## <a name="authentication"></a>Uwierzytelnianie użytkowników
Mobile Apps obsługuje uwierzytelnianie i autoryzowanie użytkowników aplikacji za pomocą różnych dostawców tożsamości zewnętrznych: Facebook, Google, konta Microsoft, Twitter i Azure Active Directory. Możesz ustawić uprawnienia w tabelach ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników. Tożsamość uwierzytelnionych użytkowników umożliwia również zaimplementować reguły autoryzacji w skryptów serwera. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Dodawanie uwierzytelniania do aplikacji].

Obsługiwane są dwa przepływy uwierzytelniania: *zarządzanych przez klienta* i *serwer zarządzany* przepływu. Serwer zarządzany przepływu dają najprostszym proces uwierzytelniania opiera się na interfejs uwierzytelniania sieci web dostawcy. Przepływ zarządzanych przez klienta umożliwia lepszą integrację z funkcjami specyficznych dla urządzenia jak opiera się na zestawy SDK specyficzne dla urządzenia specyficzny dla dostawcy.

> [!NOTE]
> Zalecamy używanie przepływu zarządzanych przez klienta w aplikacjach produkcyjnych.

Aby skonfigurować uwierzytelnianie, należy zarejestrować aplikację w usłudze przynajmniej jednego dostawcy tożsamości.  Dostawca tożsamości generuje identyfikator klienta i klucz tajny klienta aplikacji.  Te wartości są ustawiane następnie w kodzie zaplecza, aby umożliwić usłudze Azure App Service uwierzytelniania/autoryzacji.  Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami szczegółowe samouczka [Dodawanie uwierzytelniania do aplikacji].

W tej sekcji omówione są następujące tematy:

* [Klient zarządzany uwierzytelniania](#clientflow)
* [Serwer zarządzany uwierzytelniania](#serverflow)
* [Buforowanie tokenu uwierzytelniania](#caching)

### <a name="clientflow"></a>Klient zarządzany uwierzytelniania
Aplikację można niezależnie skontaktować się z dostawcą tożsamości i następnie udostępnić zwrócony token podczas logowania z wewnętrzną bazą danych. Ten przepływ klienta pozwala zapewnić funkcji logowania jednokrotnego dla użytkowników bądź pobrać dodatkowe dane użytkownika od dostawcy tożsamości. Uwierzytelnianie przepływu klienta jest preferowany dla wiadomości przy użyciu przepływu serwera dostawcy tożsamości zestawu SDK zapewnia bardziej natywnego działania środowiska użytkownika i umożliwia dodatkowych dostosowań.

Przykłady znajdują się następujące wzorce uwierzytelniania przepływ klienta:

* [Biblioteki uwierzytelniania usługi Active Directory](#adal)
* [Facebook lub Google](#client-facebook)

#### <a name="adal"></a>Uwierzytelnianie użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory
Active Directory Authentication Library (ADAL) można użyć do uwierzytelnienia użytkownika inicjowania z klienta przy użyciu uwierzytelniania usługi Azure Active Directory.

1. Skonfiguruj zaplecza aplikacji mobilnej dla logowania jednokrotnego usługi AAD, wykonując [Jak skonfigurować usługi App Service dla logowania usługi Active Directory] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowanie natywnej aplikacji klienckiej.
2. W programie Visual Studio lub Xamarin Studio, otwórz projekt i Dodaj odwołanie do `Microsoft.IdentityModel.Clients.ActiveDirectory` pakietu NuGet. Podczas wyszukiwania, obejmują wersje wstępne.
3. Dodaj następujący kod do aplikacji, zależnie od platformy, którego używasz. W poszczególnych usług wprowadź następujące elementy zastępcze:

   * Zastąp **INSERT-urzędu-tutaj** nazwą dzierżawy, w której aprowizowano aplikacji. Powinna być w formacie https://login.microsoftonline.com/contoso.onmicrosoft.com. Ta wartość może zostać skopiowany z karcie domeny w usłudze Azure Active Directory w [Azure Portal].
   * Zastąp **INSERT zasobów — identyfikator — tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Można uzyskać identyfikator klienta z **zaawansowane** karcie **ustawienia usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT klienta-ID — tutaj** z Identyfikatorem klienta został skopiowany z aplikacja kliencka macierzystego.
   * Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, przy użyciu schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*.

     Następujący kod wymagane dla każdej platformy:

     **Windows:**

     ```csharp
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

     ```csharp
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

     ```csharp
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

#### <a name="client-facebook"></a>Logowanie jednokrotne przy użyciu tokenu z usług Facebook lub Google
Korzystać z tego przepływu klienta, jak pokazano w tym fragmencie kodu dla usługi Facebook lub Google.

```csharp
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

### <a name="serverflow"></a>Serwer zarządzany uwierzytelniania
Po zarejestrowaniu dostawcy tożsamości Wywołaj [LoginAsync] metody [MobileServiceClient] za pomocą [MobileServiceAuthenticationProvider] wartość dostawcy. Na przykład poniższy kod powoduje zainicjowanie logowania przepływ serwera za pomocą serwisu Facebook.

```csharp
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

Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, zmień wartość [MobileServiceAuthenticationProvider] wartości dla dostawcy.

W przepływie serwera usługi Azure App Service zarządza przepływem uwierzytelniania OAuth, wyświetlając stronę logowania wybranego dostawcy.  Po powrocie dostawcy tożsamości usługi Azure App Service generuje token uwierzytelniania usługi App Service. [LoginAsync] metoda zwraca [MobileServiceUser], który zawiera oba [UserId] uwierzytelnionego użytkownika i [MobileServiceAuthenticationToken], jako tokenu web JSON (JWT). Ten token można zapisać w pamięci podręcznej i ponownie go używać, dopóki nie wygaśnie. Aby uzyskać więcej informacji, zobacz [buforowania tokenu uwierzytelniania](#caching).

### <a name="caching"></a>Buforowanie tokenu uwierzytelniania
W niektórych przypadkach można uniknąć wywołania metody logowania po pierwszym pomyślnym uwierzytelnieniu przez zapisanie tokenu uwierzytelniania od dostawcy.  Aplikacje Microsoft Store i platformy uniwersalnej systemu Windows mogą używać [PasswordVault] w pamięci podręcznej bieżącego tokenu uwierzytelniania po pomyślnym zalogowaniu, w następujący sposób:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Wartość Nazwa użytkownika jest przechowywana jako poświadczenie nazwy użytkownika i token jest przechowywany jako hasło. Na kolejnych rozruchów, możesz sprawdzić **PasswordVault** dla buforowanych poświadczeń. W poniższym przykładzie użyto buforowanych poświadczeń, gdy zostaną znalezione, a w przeciwnym razie próbuje uwierzytelnić się ponownie przy użyciu wewnętrznej bazy danych:

```csharp
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

Po zalogowaniu się użytkownika, musisz również usunąć przechowywanych poświadczeń w następujący sposób:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Użyj aplikacji Xamarin [Xamarin.Auth] interfejsów API, aby bezpiecznie przechowywać poświadczenia w **konta** obiektu. Aby uzyskać przykład użycia tych interfejsów API, zobacz [AuthStore.cs] pliku z kodem w [próbki do udostępniania zdjęć ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Gdy używasz uwierzytelniania zarządzanych przez klienta, można buforować tokenu dostępu za pośrednictwem swojego usługodawcy, takich jak Facebook lub Twitter. Ten token może dostarczyć żądanie nowego tokenu uwierzytelniania z wewnętrznej bazy danych, w następujący sposób:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Powiadomienia wypychane
W poniższych tematach znajdziesz powiadomień wypychanych:

* [Rejestrowanie na potrzeby powiadomień wypychanych](#register-for-push)
* [Uzyskaj identyfikator SID pakietu Microsoft Store](#package-sid)
* [Zarejestruj za pomocą szablonów dla wielu platform](#register-xplat)

### <a name="register-for-push"></a>Jak: Rejestrowanie na potrzeby powiadomień wypychanych
Klienckie aplikacje mobilne można zarejestrować odbieranie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs. Podczas rejestrowania, można uzyskać dojścia uzyskany z określonych platform wypychania powiadomień usługi platformy (PNS,). Po utworzeniu rejestracji, następnie podaj tę wartość, wraz z żadnych znaczników. Poniższy kod rejestruje aplikację Windows dla powiadomień wypychanych przy użyciu usługi powiadomień Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Jeśli wypychasz do usługi WNS, a następnie należy [uzyskać identyfikator SID pakietu Microsoft Store](#package-sid).  Aby uzyskać więcej informacji na aplikacje systemu Windows, w tym jak zarejestrować do rejestracji szablonów, zobacz [Dodawanie powiadomień wypychanych do aplikacji].

Żądanie tagi z klienta nie jest obsługiwane.  Tag żądań dyskretnie są usuwane z rejestracji.
Jeśli chcesz zarejestrować urządzenie przy użyciu tagów, należy utworzyć niestandardowy interfejs API, który używa interfejsu API centrów powiadomień w celu przeprowadzenia rejestracji w Twoim imieniu.  Wywoływanie niestandardowego interfejsu API, zamiast `RegisterNativeAsync()` metody.

### <a name="package-sid"></a>Jak: Uzyskaj identyfikator SID pakietu Microsoft Store
Identyfikator SID pakietu jest wymagany przez włączenie powiadomień wypychanych w aplikacji Microsoft Store.  Aby otrzymać identyfikator SID pakietu, należy zarejestrować aplikację w usłudze Microsoft Store.

Aby uzyskać tę wartość:

1. W Eksploratorze rozwiązań w usłudze Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji Microsoft Store, kliknij przycisk **Store** > **Skojarz aplikację ze Store...** .
2. W kreatorze kliknij pozycję **dalej**, zaloguj się przy użyciu swojego konta Microsoft, wpisz nazwę swojej aplikacji w **Zarezerwuj nazwę nowej aplikacji**, następnie kliknij przycisk **rezerwy**.
3. Po pomyślnym utworzeniu rejestracji aplikacji, wybierz nazwę aplikacji, kliknij przycisk **dalej**, a następnie kliknij przycisk **skojarzyć**.
4. Zaloguj się do [Centrum deweloperów Windows] przy użyciu Account firmy Microsoft. W obszarze **Moje aplikacje**, kliknij przycisk z rejestracji aplikacji utworzony.
5. Kliknij przycisk **Zarządzanie aplikacjami** > **tożsamości aplikacji**, a następnie przewiń w dół, aby znaleźć Twoje **identyfikator SID pakietu**.

Wiele zastosowań identyfikator SID pakietu jej traktowała jako identyfikator URI, w którym to przypadku należy użyć *ms-app: / /* schemat. Zanotuj wersji pakietu utworzone przez złączenie tej wartości jako prefiks identyfikatora SID.

Aplikacje platformy Xamarin wymagają dodatkowy kod, aby można było zarejestrować aplikacji uruchomionej na platformy iOS lub Android. Aby uzyskać więcej informacji zobacz temat dla danej platformy:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Jak: Rejestrowanie szablonów wypychania do wysyłania powiadomień dla wielu platform
Aby zarejestrować szablony, użyj `RegisterAsync()` metody przy użyciu szablonów, w następujący sposób:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Szablony powinny być `JObject` typów i może zawierać wiele szablonów w następującym formacie JSON:

```csharp
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

Metoda **RegisterAsync()** akceptuje także dodatkowej Kafelki:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Wszystkie tagi są natychmiast usunięte podczas rejestracji dla zabezpieczeń. Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz [Praca z zestawem SDK serwera zaplecza platformy .NET usługi Azure Mobile Apps].

Aby wysyłać powiadomienia przy użyciu zarejestrowanych przy użyciu tych szablonów, zobacz [Interfejsy API centrów powiadomień].

## <a name="misc"></a>Tematy dodatkowe
### <a name="errors"></a>Jak: Obsługa błędów
Po wystąpieniu błędu w wewnętrznej bazie danych, klient SDK zgłasza `MobileServiceInvalidOperationException`.  Poniższy przykład pokazuje, jak obsłużyć wyjątek, który jest zwracany przez zaplecze:

```csharp
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

Innym przykładem zajmowanie się warunki błędów można znaleźć w [Przykładowe pliki aplikacji mobilnych]. [LoggingHandler] przykładzie przedstawiono procedury obsługi delegata rejestrowania do rejestrowania żądań wysyłanych do zaplecza.

### <a name="headers"></a>Jak: Dostosowywanie nagłówków żądania
Do obsługi danego scenariusza specyficzne dla aplikacji, może być konieczne dostosowanie komunikacji przy użyciu zaplecza aplikacji mobilnej. Na przykład można dodać niestandardowy nagłówek, aby każde wychodzące żądanie lub nawet zmienić kodów stanu odpowiedzi. Można używać niestandardowego [DelegatingHandler], jak w poniższym przykładzie:

```csharp
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
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Dodawanie uwierzytelniania do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Dodawanie powiadomień wypychanych do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Jak skonfigurować usługi App Service dla logowania usługi Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[tworzy odwołanie do tabeli bez typu]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
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
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum deweloperów Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Interfejsy API centrów powiadomień]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Przykładowe pliki aplikacji mobilnych]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Dokumentacja usługi OData v3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
