---
title: Niezawodne usługi powiadomień | Dokumentacja firmy Microsoft
description: Dokumentacja koncepcyjna powiadomień usługi sieć szkieletowa niezawodne usługi
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 4455b259ef2159f9e1ec4991fc533f1843899682
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-notifications"></a>Niezawodne usługi powiadomień
Powiadomienia Zezwalaj klientom na śledzić zmiany wprowadzone do obiektu, który interesują Cię. Powiadomienia obsługuje dwa typy obiektów: *niezawodnej Menedżer stanu* i *niezawodnej słownika*.

Najczęstsze przyczyny przy użyciu powiadomienia są:

* Budynek zmaterializowany widokach, takich jak indeksów pomocniczych lub agregowana filtrowane widoki stanu repliki. Przykładem jest posortowana indeks wszystkie klucze w słowniku niezawodne.
* Wysyłanie danych monitorowania, takie jak liczba użytkowników dodanych w ciągu ostatniej godziny.

Powiadomienia są uruchamiane w ramach stosowania operacji. Z tego powodu powiadomienia powinien zostać obsłużony tak szybko, jak zdarzenia możliwe i synchroniczne nie powinna zawierać żadnych operacji kosztowne.

## <a name="reliable-state-manager-notifications"></a>Stan niezawodny Menedżera powiadomień
Menedżer stanu niezawodny zapewnia powiadomienia o następujących zdarzeń:

* Transakcji
  * Zatwierdzenie
* Menedżer stanu
  * Ponowne kompilowanie
  * Dodanie niezawodnej stanu
  * Usunięcie stanu niezawodnej

Menedżer stanu niezawodny śledzi bieżącej transakcji porządkowych. Zmiana tylko w stanie transakcji, który powoduje, że powiadomienia wyzwalane jest Trwa zatwierdzanie transakcji.

Menedżer stanu niezawodny przechowuje kolekcję stanów niezawodna jak słownik niezawodnych i niezawodne kolejki. Menedżer stanu niezawodny generowane powiadomienia, gdy zmienia się tej kolekcji: dodano lub usunięto niezawodnej stanu lub odbudowaniu całą kolekcję.
Kolekcja niezawodnej Menedżer stanu zostanie odtworzony w trzech przypadkach:

* Odzyskiwanie: Po uruchomieniu repliki odzyskuje poprzedniego stanu z dysku. Po zakończeniu odzyskiwania używa **NotifyStateManagerChangedEventArgs** zdarzenia zawierający zestaw odzyskane niezawodnej stanów.
* Pełna kopia: przed repliki może dołączyć do zestawu konfiguracji, ma ma zostać utworzony. Czasami wymaga pełną kopię stanu niezawodnej Menedżer stanu z repliki podstawowej, która ma zostać zastosowany do repliki pomocniczej bezczynności. Menedżer stanu niezawodny w replice pomocniczej używa **NotifyStateManagerChangedEventArgs** zdarzenia zawierający zestaw niezawodnej stanów, które uzyskał z repliki podstawowej.
* Przywracanie: W przypadku scenariuszy odzyskiwania po awarii repliki stan można przywrócić z kopii zapasowej za pomocą **RestoreAsync**. W takich przypadkach używa niezawodnej Menedżer stanu w replice podstawowej **NotifyStateManagerChangedEventArgs** zdarzenia zawierający zestaw niezawodnej stanów, które go przywrócić z kopii zapasowej.

Do zarejestrowania dla transakcji powiadomienia i/lub powiadomienia menedżera stanu, należy zarejestrować z **TransactionChanged** lub **StateManagerChanged** zdarzeń w niezawodnej Menedżer stanów. Spójne rejestrowania z tych programów obsługi zdarzeń jest konstruktor stanowe usługi. Gdy zarejestrujesz się w konstruktorze, nie będzie pominięcia wszystkich powiadomień, który jest spowodowany przez zmianę w czasie trwania **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** korzysta z programu obsługi zdarzeń **NotifyTransactionChangedEventArgs** podać szczegóły dotyczące zdarzenia. Zawiera właściwość action (na przykład **NotifyTransactionChangedAction.Commit**), który określa typ zmiany. Zawiera także właściwości transakcji, zapewniający odniesienie transakcji zmienione.

> [!NOTE]
> Obecnie **TransactionChanged** zdarzenia są generowane tylko wtedy, gdy transakcja została przekazana. Akcja będzie równa **NotifyTransactionChangedAction.Commit**. Jednak w przyszłości zdarzeń może zostać wywołane dla innych typów zmian stanu transakcji. Firma Microsoft zaleca sprawdzenie akcji i przetwarzania zdarzenia tylko wtedy, gdy jest to jeden, który będzie.
> 
> 

Poniżej przedstawiono przykładowy **TransactionChanged** obsługi zdarzeń.

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

**StateManagerChanged** korzysta z programu obsługi zdarzeń **NotifyStateManagerChangedEventArgs** podać szczegóły dotyczące zdarzenia.
**NotifyStateManagerChangedEventArgs** ma dwa podklas: **NotifyStateManagerRebuildEventArgs** i **NotifyStateManagerSingleEntityChangedEventArgs**.
Użyj właściwości akcji w **NotifyStateManagerChangedEventArgs** można rzutować **NotifyStateManagerChangedEventArgs** do podklasy poprawne:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** i **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Poniżej przedstawiono przykładowy **StateManagerChanged** obsługi powiadomień.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Niezawodne powiadomienia słownik
Słownik niezawodnej zapewnia powiadomienia o następujących zdarzeń:

* Rekompiluj: Wywoływane, gdy **ReliableDictionary** odzyskał z kopii zapasowej odzyskana lub skopiowane stanu lokalnego lub jego stan.
* Wyczyść: Wywoływane, gdy stan **ReliableDictionary** został wyczyszczony za pośrednictwem **ClearAsync** metody.
* Dodaj: Wywoływane, gdy element został dodany do **ReliableDictionary**.
* Aktualizacja: Wywoływane, gdy element **IReliableDictionary** została zaktualizowana.
* Usuń: Wywoływane, gdy element **IReliableDictionary** został usunięty.

Otrzymywać powiadomień niezawodnej słownika, musisz zarejestrować w usłudze **DictionaryChanged** obsługi zdarzeń na **IReliableDictionary**. Spójne rejestrowania z tych programów obsługi zdarzeń jest **ReliableStateManager.StateManagerChanged** dodać powiadomienia.
Podczas rejestrowania **IReliableDictionary** jest dodawany do **IReliableStateManager** gwarantuje, że nie utracić żadnych powiadomień.

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
> **ProcessStateManagerSingleEntityNotification** jest przykładowa metoda który poprzedniego **OnStateManagerChangedHandler** przykład wywołania.
> 
> 

Poprzedniego zestawu kodu **IReliableNotificationAsyncCallback** interfejsu, wraz z **DictionaryChanged**. Ponieważ **NotifyDictionaryRebuildEventArgs** zawiera **IAsyncEnumerable** interfejsu — które musi zostać wyliczone asynchronicznie — Odbuduj powiadomienia są uruchamiane przy użyciu  **RebuildNotificationAsyncCallback** zamiast **OnDictionaryChangedHandler**.

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
> W poprzednim kodzie podczas przetwarzania powiadomienia o odbudowie, najpierw utrzymywana zagregowane stan jest usuwany. Ponieważ niezawodnej kolekcji odbudowaniu trwa o nowy stan wszystkich powiadomienia nie mają znaczenia.
> 
> 

**DictionaryChanged** korzysta z programu obsługi zdarzeń **NotifyDictionaryChangedEventArgs** podać szczegóły dotyczące zdarzenia.
**NotifyDictionaryChangedEventArgs** ma pięć podklas. Użyj właściwości akcji w **NotifyDictionaryChangedEventArgs** można rzutować **NotifyDictionaryChangedEventArgs** do podklasy poprawne:

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
* *Czy* zakończenie zdarzenia powiadomień tak szybko jak to możliwe.
* *Nie* wykonać wszystkie operacje kosztowne (na przykład operacji We/Wy) jako część zdarzenia synchroniczne.
* *Czy* Sprawdź typ akcji, aby przetworzyć zdarzenie. Nowe typy akcji mogą być dodane w przyszłości.

Poniżej przedstawiono niektóre czynności, które należy wziąć pod uwagę:

* Powiadomienia są uruchamiane jako część wykonanie operacji. Na przykład powiadomienia przywracania jest uruchamiany jako ostatni krok operacji przywracania. Przywracanie nie zostanie zakończony, dopóki przetwarzania zdarzenia z powiadomieniem.
* Ponieważ powiadomienia są uruchamiane w ramach operacji stosowania, klientów, zobacz tylko powiadomień dla operacji lokalnie zatwierdzone. Ponieważ operacje są gwarancji tylko lokalnie zatwierdzone (innymi słowy, rejestrowane), mogą być lub może nie są zapisywane w przyszłości.
* W ścieżce Powtórz pojedyncze powiadomienia jest generowane dla każdej operacji zastosowane. Oznacza to, że jeśli transakcja T1 zawiera Create(X), Delete(X) i Create(X), otrzymasz jednego powiadomienia w celu utworzenia X, jeden do usunięcia, a drugi w celu utworzenia ponownie, w tej kolejności.
* Dla transakcji, które zawierają wiele operacji operacji są stosowane w kolejności, w jakiej zostały odebrane w replice podstawowej przez użytkownika.
* W ramach przetwarzania postępu false niektóre operacje mogą być cofnąć. Powiadomienia są zgłaszane dla takich operacji cofania, Przywracanie stanu repliki stabilna punktu. Jedną istotną różnicą powiadomienia cofania jest, że zdarzenia, które mają zduplikowane klucze są agregowane. Na przykład jeśli to Trwa wycofywanie transakcji T1, zobaczysz jednego powiadomienia Delete(X).

## <a name="next-steps"></a>Kolejne kroki
* [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
* [Niezawodne usługi Kopia zapasowa i przywracanie (odzyskiwania po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

