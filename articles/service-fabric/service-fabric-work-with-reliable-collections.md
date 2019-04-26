---
title: Praca z elementami Reliable Collections | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące pracy z elementami Reliable Collections.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2019
ms.author: aljo
ms.openlocfilehash: bb99e5984f91edb0cf40f3bdc485624b9ec59833
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506743"
---
# <a name="working-with-reliable-collections"></a>Praca z elementami Reliable Collections
Usługa Service Fabric oferuje stanowych model programowania dostępnych dla deweloperów platformy .NET za pomocą elementów Reliable Collections. W szczególności usługa Service Fabric udostępnia klasy niezawodna kolejka i niezawodnego słownika. Korzystając z tych klas, stan programu jest podzielona na partycje (w przypadku skalowalności), replikowane (w przypadku dostępności) i odbywających się w partycji (w przypadku semantyki ACID). Możemy przyjrzeć się typowego użycia obiektu niezawodnego słownika i zobacz, co faktycznie wykonywanie operacji.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Wszystkie operacje na obiektach niezawodnego słownika (z wyjątkiem ClearAsync, który jest nieodwracalna), wymagają ITransaction obiektu. Ten obiekt skojarzył z nim dowolny, a wszystkie zmiany, które próbujesz wprowadzać do dowolnego niezawodnego słownika i/lub niezawodne kolejki obiektów w ramach jednej partycji. Uzyskiwanie ITransaction obiektu przez wywołanie metody partycji przez metodę CreateTransaction StateManager firmy.

W powyższym kodzie obiekt ITransaction jest przekazywany do metody AddAsync niezawodnego słownika. Wewnętrznie metody słownika, które akceptują klucz przyjmują czytnika/blokadę skojarzoną z kluczem. Jeśli metoda Modyfikuje wartości klucza, ta metoda przyjmuje blokadę zapisu klucza, a jeśli metoda odczytuje tylko od wartości klucza, blokady odczytu jest następnie przekierowywany w kluczu. Ponieważ AddAsync Modyfikuje wartość klucza do nowego, przekazana wartość, blokady zapisu klucza jest zajęta. Tak Jeśli wątków 2 (lub więcej) spróbuje dodać wartości z tym samym kluczem, w tym samym czasie, jeden wątek uzyska dostęp do blokady zapisu i zablokuje przez inne wątki. Domyślnie w bloku metody do 4 sekund na uzyskanie blokady; po 4 sekundach metody zgłaszać TimeoutException. Przeciążenia metody istnieje, co pozwala przekazać wartość limitu czasu jawne, jeśli chcesz użyć.

Zwykle piszesz kod, aby reagować na TimeoutException, przechwytywania go i ponawianie próby całej operacji (jak pokazano w powyższym kodzie). W mojej prostego kodu wywoływany tylko Task.Delay przekazywanie każdorazowo 100 milisekund. Jednak w rzeczywistości może być lepsze rozwiązanie przy użyciu pewnego rodzaju opóźnień wykładniczych wycofań zamiast tego.

Gdy jest blokada AddAsync dodaje odwołania do obiektów kluczy i wartości do wewnętrznego słownika tymczasowe skojarzone z obiektem ITransaction. W ten sposób zapewniają semantykę odczytu swoich własnych zapisów. Oznacza to, że po wywołaniu metody AddAsync nowszych wywołanie TryGetValueAsync (przy użyciu tego samego obiektu ITransaction) zwróci wartość, nawet wtedy, gdy transakcja nie zostały jeszcze zatwierdzone. Następnie AddAsync serializuje klucz i wartość obiekty do tablic bajtowych i dołącza te tablice bajtów w pliku dziennika w węźle lokalnym. Na koniec AddAsync wysyła tablice typu byte do wszystkich replik pomocniczych, dzięki czemu mają te same informacje klucz/wartość. Mimo że informacji klucza i wartości została zapisana w pliku dziennika, informacje nie uznaje się za części, słownika przed przekazaniem transakcji, które są skojarzone.

W powyższym kodzie wywołanie CommitAsync zatwierdza wszystkie operacje transakcji. W szczególności dołącza informacje o zatwierdzeniu do pliku dziennika w węźle lokalnym i wysyła również rekordem zatwierdzania do replik pomocniczych. Po użytkownik kworum (większość) repliki, wszystkie zmiany danych są traktowane jako trwałe i zwolnienia blokad, wszelkie skojarzone z kluczami, które były zmieniane via ITransaction obiektu, dzięki czemu inne wątki/transakcji można manipulować tych samych kluczy i ich wartości.

Jeśli CommitAsync nie jest wywoływany (zazwyczaj z powodu rzuceniem wyjątku), obiekt ITransaction pobiera usunięty. Podczas usuwania obiektu ITransaction niezatwierdzone usługi Service Fabric dołącza przerwania informacji do pliku dziennika węźle lokalnym i nic nie musi być wysyłana do wszystkich replik pomocniczych. A następnie zwolnienia blokad, wszelkie skojarzone z kluczami, które były zmieniane za pomocą transakcji.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Typowych pułapek oraz sposoby unikania ich
Teraz, gdy rozumiesz, jak elementy reliable collections działają wewnętrznie, Spójrzmy na niektóre typowe wpływające. Zobacz poniższy kod:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Podczas pracy z regularnych słownik platformy .NET, należy dodać klucz/wartość do słownika, a następnie zmień wartość właściwości (na przykład LastLogin). Jednak ten kod nie będą działać poprawnie przy użyciu niezawodnego słownika. Należy pamiętać wcześniej dyskusji wywołanie AddAsync serializuje obiekty klucz/wartość do tablic bajtowych i następnie zapisuje tablic w pliku lokalnym i wysyła je do replik pomocniczych. W przypadku późniejszej zmiany właściwości, spowoduje to zmianę wartości właściwości w pamięci. nie ma ona wpływu lokalnego pliku lub dane wysyłane do replik. Jeśli proces ulegnie awarii, co znajduje się w pamięci jest wyrzucać. Po uruchomieniu nowego procesu lub innej repliki staje się podstawowym, stara wartość właściwości jest, co jest dostępne.

Nie można podkreślają, wystarczająco jak łatwo jest zapewnienie rodzaj błędu przedstawionych powyżej. Ponadto tylko poznasz o pomyłkę Jeśli/gdy ulegnie awarii procesu. Poprawny sposób napisać kod jest po prostu odwrócić dwa wiersze:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Oto inny przykład przedstawiający powszechnym:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Ponownie za pomocą regularnego słowniki .NET, powyższy kod działa prawidłowo i jest to typowy wzorzec: programistka używa klucza do wyszukania wartość. Jeśli wartość istnieje, deweloper zmienia wartość właściwości. Jednak z elementami reliable collections to kod wykazuje ten sam problem, jaki już omówione: **nie wolno modyfikować obiektu, gdy przyznano niezawodnych kolekcji.**

Poprawny sposób zaktualizować wartości w niezawodnej kolekcji jest odwołać się do istniejącej wartości i należy wziąć pod uwagę obiekt odwołuje się do tego odwołania, które są niezmienne. Następnie utwórz nowy obiekt, który jest dokładna kopia oryginalnego obiektu. Teraz można modyfikować stanu tego nowy obiekt i Zapisz nowy obiekt w kolekcji tak, aby go jest serializowana z tablic bajtowych dołączany do pliku lokalnego i przesyłane do replik. Po zatwierdzeniu zmian, obiekty w pamięci, lokalnego pliku i wszystkie repliki mają ten sam stan. Wszystko jest w porządku!

Poniższy kod pokazuje poprawne sposób aktualizowania wartości w niezawodnej kolekcji:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiowanie typów niezmienialnymi danymi, aby uniknąć błędów programisty
W idealnym przypadku prosimy o poświęcenie kompilator, aby włączyć raportowanie błędów podczas przypadkowo tworzenia kodu, która mutuje stan obiektu, który jest powinien wziąć pod uwagę niezmienne. Jednak C# kompilator nie ma możliwości, aby to zrobić. Tak, aby uniknąć potencjalnych błędów programisty, zdecydowanie zalecamy czy zdefiniować typy użycia z elementami reliable collections być typami niezmienne. W szczególności oznacza to, że używany podstawowych typów wartości (na przykład numery [Int32, UInt64, itp.,], daty/godziny, identyfikator Guid, TimeSpan i podobne). Można również użyć ciągu. Najlepiej unikać właściwości kolekcji serializacji i deserializacji je często może obniżyć wydajność. Jednak jeśli chcesz korzystać z właściwości kolekcji, zdecydowanie zalecamy użycie. Biblioteka kolekcje niezmienialne NET firmy ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ta biblioteka jest dostępny do pobrania https://nuget.org. Zalecamy również pieczętowanie Twoich zajęciach i oznaczanie pól tylko do odczytu, jeśli to możliwe.

Typ informacji o użytkowniku, poniżej pokazano, jak zdefiniować typ niezmienne, korzystając z zalet wyżej zalecenia.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
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
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Wpisz identyfikator elementu jest również typem niezmienne, jak pokazano poniżej:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Przechowywanie wersji schematu (uaktualnienia)
Wewnętrznie elementów Reliable Collections serializacji obiektów przy użyciu. DataContractSerializer NET firmy. Serializacji obiektów jest zachowywany na dysku lokalnym repliką podstawową, a także są przekazywane do replik pomocniczych. Jak dojrzewa usługi, prawdopodobnie należy zmienić typ danych (schemat) wymaga Twoja usługa. Podejście do danych za pomocą szczególną uwagę na zarządzanie ich wersjami. Najpierw należy zawsze można rozszeregować starych danych. W szczególności oznacza to, że kod deserializacji musi być zgodny z poprzednimi nieskończenie: Wersja 333 kodu usługi musi mieć możliwość operować na danych umieszczone w reliable collection w wersji 1 Kod usługi 5 lat temu.

Ponadto kod usługi jest uaktualniony jedną domenę uaktualnienia w danym momencie. Dlatego podczas uaktualniania, masz dwie różne wersje kodu usługi uruchomione jednocześnie. Należy unikać, o nową wersję kodu usługi zastosowania nowego schematu starsze wersje kodu usługi mogą nie być w stanie obsłużyć nowy schemat. Jeśli to możliwe, należy projektować w każdej wersji usługi jako zgodny wprzód według jedną wersję. W szczególności oznacza to, że V1 kodu usługi powinien móc Zignoruj wszelkie elementy schematu, które jawnie nie obsługuje. Jednak musi być w stanie zapisać wszystkie dane, które jawnie nie wiedzieć o i zapisu, wycofywanie podczas aktualizowania klucz ze słownika lub wartość.

> [!WARNING]
> Chociaż można zmodyfikować schematu klucza, upewnij się, kod skrótu klucza usługi i algorytmy equals są stabilne. Jeśli zmienisz sposób działania albo te algorytmy, nie można wyszukać klucz w niezawodnym słowniku kiedykolwiek ponownie.
> Ciągi .NET może służyć jako klucz, użyj ciągu jako klucz — nie należy używać wynik String.GetHashCode jako klucz.

Alternatywnie można wykonywać, co jest zwykle nazywany dwóch uaktualnienia. Uaktualnianie dwufazowe uaktualnieniu usługi z V1 na V2: W wersji 2 zawiera kod, który wie, jak radzić sobie z nowej zmiany schematu, ale ten kod nie jest wykonywany. Gdy kod V2 odczytuje dane V1, działa na nim i zapisuje dane w wersji 1. Następnie po ukończeniu uaktualniania w domenach uaktualnienia wszystkich użytkownik może jakiś sposób sygnału uruchomionych wystąpień w wersji 2 zakończeniu uaktualniania. (Jeden ze sposobów sygnału to wdrażanie uaktualnienia konfiguracji; jest to, co sprawia, że to dwufazowe uaktualniania). Teraz wystąpień w wersji 2 można odczytać danych w wersji 1, przekonwertować danych w wersji 2, wykonywać na nich operacje i zapisz je jako dane w wersji 2. Jeśli inne wystąpienia do odczytu danych w wersji 2, nie ma potrzeby przekonwertować go, po prostu działają na niej, a także zapisać danych w wersji 2.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tworzeniu kontrakty danych zgodne do przodu, zobacz [kontrakty danych zgodne](https://msdn.microsoft.com/library/ms731083.aspx)

Aby poznać najlepsze rozwiązania na przechowywanie wersji kontraktów danych, zobacz [przechowywanie wersji kontraktów danych](https://msdn.microsoft.com/library/ms731138.aspx)

Aby dowiedzieć się, jak zaimplementować kontraktów na uszkodzenia danych w wersji, zobacz [wywołania zwrotne serializacji z tolerancją dla wersji](https://msdn.microsoft.com/library/ms733734.aspx)

Aby dowiedzieć się, jak zapewnić struktura danych, która może współpracować w wielu wersjach, zobacz [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)