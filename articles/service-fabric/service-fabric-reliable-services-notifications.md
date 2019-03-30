---
title: Niezawodne usługi powiadomień | Dokumentacja firmy Microsoft
description: Dokumentacja koncepcyjna powiadomień usług usługi Service Fabric Reliable Services
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662673"
---
# <a name="reliable-services-notifications"></a>Niezawodne usługi powiadomień
Dzięki powiadomieniom klientów śledzić zmiany wprowadzone do obiektu, który interesują Cię. Dwa typy obiektów obsługuje powiadomień: *Reliable State Manager* i *niezawodnego słownika*.

Za pomocą powiadomień typowe przyczyny są następujące:

* Tworzenia zmaterializowanych widoków, takich jak indeksy pomocnicze lub agregowane filtrowane widoki stanu repliki. Przykładem jest posortowany indeks wszystkie klucze w niezawodnym słowniku.
* Wysyłanie danych monitorowania, takich jak liczba użytkowników dodanych w ciągu ostatniej godziny.

Powiadomienia są uruchamiane w ramach zastosowania operacji. Z tego powodu powiadomienia powinny być traktowane tak szybko, jak to możliwe i synchronicznych zdarzeń nie powinna zawierać żadnych kosztownych operacji.

## <a name="reliable-state-manager-notifications"></a>Elementy Reliable State Manager powiadomienia
Elementy Reliable State Manager udostępnia powiadomienia dotyczące następujących zdarzeń:

* Transakcja
  * Zatwierdzenie
* Menedżer stanu
  * Ponowne kompilowanie
  * Dodawanie niezawodnych stanu
  * Usuwanie stanu niezawodne

Elementy Reliable State Manager śledzi bieżące transakcje porządkowych. Jedyna zmiana w stanie transakcji, który powoduje, że powiadomienie do uruchomienia jest zatwierdzanie transakcji.

Elementy Reliable State Manager przechowuje kolekcję stanów niezawodne, takich jak niezawodnego słownika i niezawodna kolejka. Elementy Reliable State Manager generowane powiadomienia, gdy zmieni się tej kolekcji: niezawodne stanu jest dodawany lub usuwany lub całą kolekcję zostanie ponownie skompilowany.
Kolekcja Reliable State Manager zostanie ponownie skompilowany w trzech przypadkach:

* Odzyskiwanie: Po uruchomieniu repliki przywraca poprzedni stan z dysku. Po zakończeniu odzyskiwania używa **NotifyStateManagerChangedEventArgs** wyzwolenie zdarzenia, które zawiera zestaw odzyskane stany niezawodne.
* Pełna kopia: Przed repliki można dołączyć zestaw konfiguracji, musi zostać utworzony. Czasami wymaga to pełna kopia stanie niezawodne Menedżer stanów z repliki podstawowej, które mają być stosowane do repliki pomocniczej bezczynności. Elementy Reliable State Manager w replice pomocniczej używa **NotifyStateManagerChangedEventArgs** wywołać zdarzenie, który zawiera zestaw niezawodnych stanów, które go uzyskanych z repliką podstawową.
* Przywróć: W przypadku scenariuszy odzyskiwania po awarii, można przywrócić z kopii zapasowej za pośrednictwem stan repliki **RestoreAsync**. W takich przypadkach używa Reliable State Manager w replice podstawowej **NotifyStateManagerChangedEventArgs** wywołać zdarzenie, który zawiera zestaw niezawodnych stanów, które go przywrócić z kopii zapasowej.

Aby zarejestrować się na powiadomienia o transakcji i/lub powiadomienia o stanie menedżera, należy zarejestrować za pomocą **TransactionChanged** lub **StateManagerChanged** zdarzenia Reliable State Manager. Wspólnym miejscu, aby zarejestrować za pomocą tych programów obsługi zdarzeń jest konstruktor usługi stanowej. Po zarejestrowaniu się w konstruktorze, przegapi żadnych powiadomienie, które jest spowodowany przez zmianę w okresie istnienia **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** korzysta z programu obsługi zdarzeń **NotifyTransactionChangedEventArgs** dostarczanie szczegółowych informacji o zdarzeniu. Zawiera on właściwość akcji (na przykład **NotifyTransactionChangedAction.Commit**), który określa typ zmiany. Zawiera także właściwości transakcji, który zawiera odwołanie do transakcji, która się zmieniła.

> [!NOTE]
> Obecnie **TransactionChanged** zdarzenia są wywoływane tylko wtedy, gdy transakcja została zatwierdzona. Akcja będzie równa **NotifyTransactionChangedAction.Commit**. Ale w przyszłości zdarzeń może zostać wywołane dla innych typów zmiany stanu transakcji. Firma Microsoft zaleca sprawdzenie akcji i przetwarzania zdarzeń, tylko wtedy, gdy jest to jeden, których oczekujesz.
> 
> 

Poniżej znajduje się przykład **TransactionChanged** programu obsługi zdarzeń.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged** korzysta z programu obsługi zdarzeń **NotifyStateManagerChangedEventArgs** dostarczanie szczegółowych informacji o zdarzeniu.
**NotifyStateManagerChangedEventArgs** has two subclasses: **NotifyStateManagerRebuildEventArgs** and **NotifyStateManagerSingleEntityChangedEventArgs**.
Użyj właściwości akcji w **NotifyStateManagerChangedEventArgs** rzutowanie **NotifyStateManagerChangedEventArgs** do podklasy poprawny:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** i **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Poniżej znajduje się przykład **StateManagerChanged** obsługi powiadomień.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Niezawodny słownik powiadomienia
Niezawodnego słownika zawiera powiadomienia o następujących zdarzeń:

* Ponowna kompilacja: Wywoływane, gdy **ReliableDictionary** odzyskał jego stan z kopii zapasowej odzyskana lub skopiowany stanu lokalnego lub.
* Usuń zaznaczenie: Wywoływane, gdy stan **ReliableDictionary** został wyczyszczony za pośrednictwem **ClearAsync** metody.
* Dodaj: Wywoływane, gdy element został dodany do **ReliableDictionary**.
* Aktualizacja: Wywoływane, gdy element **IReliableDictionary** został zaktualizowany.
* Usuń: Wywoływane, gdy element **IReliableDictionary** został usunięty.

Aby otrzymywać powiadomienia w niezawodnym słowniku, należy zarejestrować za pomocą **DictionaryChanged** programu obsługi zdarzeń na **IReliableDictionary**. Typowe miejscem do rejestrowania przy użyciu tych programów obsługi zdarzeń jest **ReliableStateManager.StateManagerChanged** Dodaj powiadomienie.
Podczas rejestrowania **IReliableDictionary** jest dodawany do **IReliableStateManager** gwarantuje, że przegapi żadnych powiadomień.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** jest przykładowa metoda, poprzedni **OnStateManagerChangedHandler** przykład wywołuje.
> 
> 

Poprzedni zestawów kodu **IReliableNotificationAsyncCallback** interfejsu, wraz z **DictionaryChanged**. Ponieważ **NotifyDictionaryRebuildEventArgs** zawiera **IAsyncEnumerable** interfejsu — która musi zostać wyliczone asynchronicznie — powiadomienia ponownej kompilacji są uruchamiane przy użyciu  **RebuildNotificationAsyncCallback** zamiast **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> W poprzednim kodzie, w ramach przetwarzania powiadomienia o ponownej kompilacji, najpierw utrzymywane w dobrym stanie zagregowany stan jest usuwany. Ponieważ niezawodnej kolekcji jest kompilowane przy użyciu nowy stan, wszystkie poprzednie powiadomienia są nieistotne.
> 
> 

**DictionaryChanged** korzysta z programu obsługi zdarzeń **NotifyDictionaryChangedEventArgs** dostarczanie szczegółowych informacji o zdarzeniu.
**NotifyDictionaryChangedEventArgs** has five subclasses. Użyj właściwości akcji w **NotifyDictionaryChangedEventArgs** rzutowanie **NotifyDictionaryChangedEventArgs** do podklasy poprawny:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** i **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Zalecenia
* *Czy* ukończenia zdarzenia powiadomień tak szybko, jak to możliwe.
* *Nie* wykonaj wszelkie kosztownych operacji (na przykład operacje We/Wy) jako część zdarzenia synchroniczne.
* *Czy* Sprawdź typ akcji, przed rozpoczęciem przetwarzania zdarzenia. Nowe typy akcji mogą być dodane w przyszłości.

Oto kilka rzeczy, które należy uwzględnić:

* Powiadomienia są uruchamiane w ramach wykonywania operacji. Na przykład restore powiadomienie jest uruchamiany jako ostatni krok operacji przywracania. Przywracanie nie zostanie zakończony, dopóki nie jest przetwarzany zdarzenie powiadomienia.
* Ponieważ powiadomienia są uruchamiane jako część operacji stosowania, klientów, zobacz tylko powiadomienia dla operacji lokalnie zatwierdzone. A ponieważ gwarancję operacji tylko do można zatwierdzić lokalnie (innymi słowy, rejestrowane), mogą być lub może być cofnięte w przyszłości.
* W ścieżce ponawiania pojedyncze zgłoszenie jest generowane dla każdej operacji zastosowane. Oznacza to, że jeśli transakcja T1 zawiera Create(X) Delete(X) i Create(X), otrzymasz jedno powiadomienie do tworzenia X, jeden do usunięcia i jeden do tworzenia ponownie, w tej kolejności.
* W przypadku transakcji, które zawierają wiele operacji operacji są stosowane w kolejności, w której zostały odebrane w replice podstawowej przez użytkownika.
* Jako część false postęp przetwarzania niektóre operacje może być cofnięte. Powiadomienia są wywoływane w przypadku takich operacji cofania wycofania stan repliki do punktu stabilne. Jedną istotną różnicą powiadomienia cofania jest, że zdarzenia, które mają zduplikowane klucze są agregowane. Na przykład jeśli jest cofnięcie transakcji T1, zobaczysz pojedyncze zgłoszenie do Delete(X).

## <a name="next-steps"></a>Kolejne kroki
* [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services — szybki start](service-fabric-reliable-services-quick-start.md)
* [Niezawodne usługi Kopia zapasowa i przywracanie (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

