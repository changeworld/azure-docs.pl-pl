---
title: Praca z kolekcjami niezawodnej | Dokumentacja firmy Microsoft
description: Dowiedz się, najlepsze rozwiązania dotyczące pracy z kolekcjami niezawodne.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="working-with-reliable-collections"></a>Praca z elementami Reliable Collections
Sieć szkieletowa usług oferuje stanowe model programowania dostępne dla deweloperów platformy .NET przy użyciu niezawodnych kolekcje. W szczególności usługa Service Fabric realizuje klasy kolejka niezawodnych i niezawodne słownika. Korzystając z tych klas, swój stan jest podzielona na partycje (w przypadku skalowalności), replikowane (dostępność) i nietransakcyjnego partycji (w przypadku semantyki ACID). Załóżmy Spójrz na typowy sposób obiekcie dictionary niezawodnych i zobacz, jakie jego faktycznego czynności.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Wszystkie operacje na obiektach niezawodnej słownika (z wyjątkiem ClearAsync, która jest nieodwracalna), wymagają ITransaction obiektu. Ten obiekt jest skojarzony z nim żadnego i wszystkie zmiany, które umożliwią do dowolnego słownika niezawodnych i/lub niezawodnej kolejka obiektów w jednej partycji. W przypadku uzyskania ITransaction obiektu przez wywołanie metody partycji na zabezpiecza CreateTransaction metody.

W powyższym kodzie obiekt ITransaction jest przekazywany do metody AddAsync niezawodnej słownika. Wewnętrznie słownika metody, które akceptuje klucz zająć czytnika/blokadę skojarzoną z kluczem. Jeśli metoda Modyfikuje wartość tego klucza, metoda pobiera blokady zapisu w kluczu i jeśli metoda odczytuje jedynie z wartości klucza, następnie blokady odczytu są wykonywane na kluczu. Ponieważ AddAsync Modyfikuje wartość klucza do nowego, przekazany w wartości, blokady zapisu klucza jest zajęta. Tak 2 (lub więcej) wątków podejmie próbę dodania wartości z tym samym kluczem, w tym samym czasie, jeden wątek będzie uzyskanie blokady zapisu i inne wątki będzie blokować. Domyślnie bloku metody dla maksymalnie 4 sekundy na uzyskanie blokady; po 4 sekundy metody throw TimeoutException. Przeciążenia metody istnieje, umożliwiając przekazać wartość limitu czasu jawne, jeśli chcesz użyć.

Na ogół pisania swój kod, aby zareagować na TimeoutException przez Przechwytywanie go i wykonania całej operacji (jak pokazano w powyższym kodzie). W kodzie proste wywoływany tylko Task.Delay przekazywanie każdorazowo 100 milisekund. Ale w rzeczywistości może być lepiej zamiast określonego rodzaju wykładniczego wycofywania opóźnienia.

Po blokady są uzyskiwane, AddAsync dodaje odwołania do obiektów kluczy i wartości do tymczasowego słownik wewnętrzny skojarzony z obiektem ITransaction. Jest to realizowane dostarczają semantyki odczytu your właścicielem zapisu. Oznacza to po wywołaniu metody AddAsync nowsze wywołanie TryGetValueAsync (przy użyciu tego samego obiektu ITransaction) zwróci wartość, nawet jeśli nie zostały jeszcze przekazane transakcji. Następnie AddAsync serializuje klucz i wartość obiekty do tablic bajtowych i dołącza te tablice typu byte do pliku dziennika w węźle lokalnym. Na koniec AddAsync wysyła tablice typu byte w replikach pomocniczych, więc te same informacje klucza i wartości. Mimo że informacje klucza i wartości została zapisana w pliku dziennika, informacje jest uważana za część słownik przed przekazaniem transakcji, które są skojarzone.

W powyższym kodzie wywołanie CommitAsync zatwierdza wszystkich operacji transakcji. W szczególności dołącza przekazywania informacji do pliku dziennika w węźle lokalnym i wysyła również rekordu zatwierdzania w replikach pomocniczych. Po odpowiedział kworum (większość) repliki, wszystkie zmiany danych są traktowane jako trwałe i wszystkie skojarzone z kluczy, które zostały manipulować za pośrednictwem obiektu ITransaction blokady są wydawane tak innych wątków/transakcji można manipulować tych samych kluczy i ich wartości.

Jeśli CommitAsync nie jest wywoływany (zazwyczaj z powodu rzuceniem wyjątku), obiekt ITransaction pobiera usunięty. Podczas usuwania obiektu ITransaction niezatwierdzone sieci szkieletowej usług dołącza przerwania informacji w pliku dziennika w węźle lokalnym i nic nie trzeba wysłane do wszystkich replik pomocniczych. A następnie są wydawane wszystkie blokady związane z kluczy, które zostały manipulować za pomocą transakcji.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Typowych problemów oraz sposób ich uniknięcie
Po zapoznaniu się z wewnętrznie działania niezawodnej kolekcje, Spójrzmy na niektórych typowych wpływające. Zobacz poniższy kod:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Podczas pracy ze słownikiem regularne .NET, można dodać klucza i wartości do słownika, a następnie zmień wartość właściwości (na przykład LastLogin). Jednak ten kod nie będzie pracował prawidłowo niezawodnej słownika. Należy pamiętać wcześniejszych dyskusji wywołanie AddAsync serializuje obiektów klucza i wartości do tablic bajtowych tablic jest następnie zapisywana w pliku lokalnym i wysyła je w replikach pomocniczych. Jeśli później zmienić właściwość, spowoduje to zmianę wartości właściwości w pamięci. nie wpływa na plik lokalny lub dane wysłane do repliki. Jeśli proces ulegnie awarii, co znajduje się w pamięci jest odrzucone. Po uruchomieniu nowego procesu lub inny replika staje się podstawowym, stara wartość właściwości jest, co jest dostępne.

Nie można podkreślają, wystarczająco jak łatwo jest dokonanie rodzaju błędu przedstawionych powyżej. I tylko poznasz błąd jeśli się, że proces przestanie działać. Poprawne sposobem pisanie kodu jest po prostu wstecznego dwa wiersze:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Inny przykład przedstawiający powszechnym błędem jest następujący:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Ponownie z regularne .NET słowników, powyższy kod działa prawidłowo i jest wspólnym wzorcem: dewelopera jest stosowany klucz do odszukania wartości. Jeśli istnieje wartość, deweloper zmieni się wartość właściwości. Jednak z kolekcjami niezawodne, ten kod wykazuje ten sam problem opisano jak już: **nie należy zmodyfikować obiekt po nadana jej wiarygodnych kolekcji.**

Poprawne sposobem aktualizowania wartości w zbiorze niezawodnej jest odwołać się do istniejącej wartości i należy wziąć pod uwagę obiekt odwołuje się do tego odwołania niezmienialny. Następnie utwórz nowy obiekt, który jest dokładna kopia oryginalnego obiektu. Teraz można zmodyfikować stanu tego nowego obiektu i zapisu nowego obiektu do kolekcji, aby go serializowana na tablice typu byte, dołączane do pliku lokalnego i wysyłane do repliki. Po zatwierdzania zmianę(-y), obiektów w pamięci, lokalnego pliku i wszystkie repliki mają tego samego dokładnie taki stan. Wszystko to dobry!

Poniższy kod przedstawia poprawnego sposobu aktualizowania wartości w niezawodnej kolekcji:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiowanie typów danych niezmienne, aby uniknąć błędów programisty
Najlepiej, jeśli chcemy kompilator może raportować błędy podczas przypadkowo tworzy kod, który mutuje stanu obiektu, który ma należy wziąć pod uwagę niezmienialny. Jednak kompilatora C# nie ma możliwości w tym celu. Tak, aby uniknąć potencjalnych błędów programisty, zdecydowanie zaleca się czy Definiowanie typów używać z kolekcjami niezawodnej za niezmienialny typów. W szczególności oznacza to, że osadzania podstawowych typów wartości (na przykład numery [Int32, UInt64, itp.], DateTime, Guid, TimeSpan i podobne). A, można również użyć ciągu. Zaleca się unikać właściwości kolekcji jako serializacji i deserializacji je można często może pogarszać wydajność. Jednak jeśli chcesz użyć właściwości kolekcji, zalecamy użycie. Biblioteka obsługi kolekcji niezmienialnych w sieci ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ta biblioteka jest dostępny do pobrania http://nuget.org. Zalecamy również pieczętowania klas i oznaczanie pola tylko do odczytu, jeśli to możliwe.

Typ informacji o użytkowniku poniżej pokazano, jak zdefiniować niezmiennego typu, korzystając z wyżej wymienionych zalecenia.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Typ identyfikatora elementu jest również niezmiennego typu, jak pokazano poniżej:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Przechowywanie wersji schematu (uaktualnienia)
Wewnętrznie niezawodne kolekcje serializować obiektów przy użyciu. DataContractSerializer w sieci. Obiektów serializowanych jest zachowywany na dysku lokalnym repliką podstawową, a także są przekazywane w replikach pomocniczych. Miarę rozwoju usługi, prawdopodobne jest, należy zmienić typ danych (schemat) wymaga Twoja usługa. Przechowywanie wersji danych bardzo ostrożnie musi osiągają. Najpierw i wszystkim musi zawsze można deserializować stare dane. W szczególności, oznacza to, deserializacji kodu musi być nieograniczonej zapewniającej: wersja 333 kodu usługi musi być w stanie do działania na dane umieszczone w niezawodnej kolekcji w wersji 1 kodu usługi 5 lat temu.

Ponadto kodu usługi jest uaktualniony jedną domenę uaktualnienia naraz. Tak podczas uaktualniania, masz dwie różne wersje kodu usługi uruchomione jednocześnie. Należy unikać, o nową wersję kodu usługi zastosowania nowego schematu stare wersje kodu usługi może nie być w stanie obsłużyć nowego schematu. Jeśli to możliwe, należy projektować w każdej wersji usługi jako zgodne wprzód w wersji 1. W szczególności oznacza to, że V1 kodu usługi powinno być możliwe po prostu zignoruj wszelkie elementy schematu, które jawnie nie obsługuje. Jednak musi być w stanie zapisać wszystkie dane, które jawnie nie wiedzieć o i po prostu zapisu Wycofaj podczas aktualizowania klucza słownika lub wartość.

> [!WARNING]
> Gdy schemat klucza można modyfikować, należy się upewnić, skrótu swój klucz i algorytmy equals są stałe. Jeśli zmienisz sposób działania albo te algorytmy, nie można wyszukać klucz w słowniku niezawodnej kiedykolwiek ponownie.
>
>

Alternatywnie można wykonywać, co jest zwykle nazywany uaktualnienia 2-etapowego. Wraz z uaktualnieniem 2-etapowego uaktualnienia usługi z V1 do V2: V2 zawiera kod, który potrafi radzenia sobie z nowej zmiany schematu, ale ten kod nie jest wykonywana. Gdy kod V2 odczytuje dane V1, działa na nim i zapisuje dane w wersji 1. Następnie po zakończeniu uaktualnienia domen uaktualnienia wszystkich użytkownik może jakiś sposób sygnału uruchomionych wystąpień V2 ukończeniu uaktualniania. (Jednokierunkowej sygnału, to jest wprowadzana uaktualnienia konfiguracji; jest to, co sprawia, że to uaktualnienie Faza 2). Teraz wystąpień V2 można odczytać danych V1, przekonwertuj ją na typ danych V2 go i zapisz je jako dane V2. Jeśli inne wystąpienia odczytu danych V2, ich nie trzeba przekonwertować, tylko działają na nim, a także zapisać danych w wersji 2.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tworzeniu kontrakty danych zgodne do przodu, zobacz [kontrakty danych zgodne z nowszymi](https://msdn.microsoft.com/library/ms731083.aspx).

Aby uzyskać najlepsze rozwiązania na przechowywanie wersji kontraktów danych, zobacz [przechowywanie wersji kontraktów danych](https://msdn.microsoft.com/library/ms731138.aspx).

Aby uzyskać informacje o wersji kontraktów danych na uszkodzenia wdrożenia, zobacz [wywołania zwrotne serializacji z tolerancją dla wersji](https://msdn.microsoft.com/library/ms733734.aspx).

Aby dowiedzieć się, jak zapewnić struktura danych, która może współdziałać w różnych wersjach, zobacz [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
