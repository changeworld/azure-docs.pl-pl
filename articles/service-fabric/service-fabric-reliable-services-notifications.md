---
title: Powiadomienia o niezawodnych usługach
description: Dokumentacja koncepcyjna dla powiadomień o niezawodnych usługach sieci szkieletowej dla menedżera stanu niezawodnego i niezawodnego słownika
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639568"
---
# <a name="reliable-services-notifications"></a>Powiadomienia o niezawodnych usługach
Powiadomienia umożliwiają klientom śledzenie zmian wprowadzonych w interesującym im obiekcie. Dwa typy obiektów obsługują powiadomienia: *Reliable State Manager* i Reliable *Dictionary*.

Typowe powody używania powiadomień to:

* Tworzenie zmaterializowanych widoków, takich jak indeksy pomocnicze lub zagregowane filtrowane widoki stanu repliki. Przykładem jest posortowany indeks wszystkich kluczy w słowniku niezawodnym.
* Wysyłanie danych monitorowania, takich jak liczba użytkowników dodanych w ciągu ostatniej godziny.

Powiadomienia są uruchamiane w ramach stosowania operacji. Z tego powodu powiadomienia powinny być obsługiwane tak szybko, jak to możliwe, a zdarzenia synchroniczne nie powinny zawierać żadnych kosztownych operacji.

## <a name="reliable-state-manager-notifications"></a>Powiadomienia menedżera stanu niezawodnego
Reliable State Manager udostępnia powiadomienia dla następujących zdarzeń:

* Transakcja
  * Zatwierdzenie
* Kierownik państwa
  * Ponowne kompilowanie
  * Dodanie niezawodnego stanu
  * Usunięcie niezawodnego stanu

Menedżer stanu niezawodne śledzi bieżące transakcje pokładowe. Jedyną zmianą w stanie transakcji, która powoduje, że powiadomienie ma zostać odpalone, jest transakcja zatwierdzona.

Menedżer stanu niezawodne utrzymuje kolekcję niezawodnych stanów, takich jak niezawodny słownik i niezawodne kolejki. Menedżer stanu niezawodne uruchamia powiadomienia, gdy ta kolekcja zmienia: stan niezawodny jest dodawany lub usuwany lub cała kolekcja jest przebudowywana.
Kolekcja Programu Reliable State Manager jest przebudowywana w trzech przypadkach:

* Odzyskiwanie: Po uruchomieniu repliki odzyskuje swój poprzedni stan z dysku. Na koniec odzyskiwania używa **NotifyStateManagerChangedEventArgs** do wypalania zdarzenia, które zawiera zestaw odzyskane stany niezawodne.
* Pełna kopia: Zanim replika może dołączyć do zestawu konfiguracyjnego, musi zostać zbudowana. Czasami wymaga to pełnej kopii stanu Menedżera stanu niezawodnego z repliki podstawowej, która ma zostać zastosowana do bezczynnej repliki pomocniczej. Menedżer stanu niezawodne w replice pomocniczej używa **NotifyStateManagerChangedEventArgs** do wypalania zdarzenia, które zawiera zestaw wiarygodnych stanów, które nabył z repliki podstawowej.
* Przywracanie: W scenariuszach odzyskiwania po awarii stan repliki można przywrócić z kopii zapasowej za pośrednictwem **restoreasync**. W takich przypadkach Menedżer stanu niezawodnego w replice podstawowej używa **NotifyStateManagerChangedEventArgs** do wypalania zdarzenia, które zawiera zestaw niezawodnych stanów, które zostały przywrócone z kopii zapasowej.

Aby zarejestrować powiadomienia o transakcjach i/lub powiadomienia menedżera stanu, należy zarejestrować się w **transactionchanged** lub **StateManagerZmienia zdarzenia** w Menedżerze stanu niezawodne. Wspólne miejsce do rejestrowania się przy tych programach obsługi zdarzeń jest konstruktor usługi stanowej. Podczas rejestracji w konstruktorze, nie przegapisz żadnego powiadomienia, które jest spowodowane przez zmianę w okresie istnienia **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** obsługi zdarzeń używa **NotifyTransactionChangedEventArgs,** aby podać szczegółowe informacje o zdarzeniu. Zawiera właściwość akcji (na przykład **NotifyTransactionChangedAction.Commit**), która określa typ zmiany. Zawiera również właściwość transakcji, która zapewnia odwołanie do transakcji, która uległa zmianie.

> [!NOTE]
> Dzisiaj **TransactionChanged** zdarzenia są wywoływane tylko wtedy, gdy transakcja jest zatwierdzona. Akcja jest wtedy równa **NotifyTransactionChangedAction.Commit**. Ale w przyszłości zdarzenia mogą być wywoływane dla innych typów zmian stanu transakcji. Zalecamy sprawdzenie akcji i przetworzenie zdarzenia tylko wtedy, gdy jest to działanie, którego oczekujesz.
> 
> 

Poniżej przedstawiono przykład **TransactionChanged** obsługi zdarzeń.

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

Program obsługi zdarzeń **StateManagerChanged** używa **NotifyStateManagerChangedEventArgs,** aby podać szczegółowe informacje o zdarzeniu.
**NotifyStateManagerChangedEventArgs** ma dwie podklasy: **NotifyStateManagerRebuildEventArgs** i **NotifyStateManagerSingleEntityChangedEventArgs**.
Właściwości akcji w **NotifyStateManagerChangedEventArgs** można rzutować **NotifyStateManagerChangedEventArgs** do poprawnej podklasy:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** and **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Poniżej przedstawiono **przykładu StateManagerZmieniony** program obsługi powiadomień.

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

## <a name="reliable-dictionary-notifications"></a>Powiadomienia o niezawodnym słowniku
Niezawodny słownik udostępnia powiadomienia dla następujących zdarzeń:

* Odbuduj: Wywoływane, gdy **ReliableDictionary** odzyskał swój stan z odzyskanego lub skopiowanego stanu lokalnego lub kopii zapasowej.
* Clear: Wywoływane, gdy stan **ReliableDictionary** został wyczyszczony za pomocą **ClearAsync** metody.
* Dodaj: Wywoływane po dodaniu elementu do **ReliableDictionary**.
* Aktualizacja: Wywoływana, gdy element w **IReliableDictionary** został zaktualizowany.
* Usuń: Wywoływane po usunięciu elementu w **IReliableDictionary.**

Aby uzyskać powiadomienia reliable dictionary, należy zarejestrować się w **dictionarychanged** obsługi zdarzeń na **IReliableDictionary**. Wspólne miejsce, aby zarejestrować się w tych programach obsługi zdarzeń jest w **ReliableStateManager.StateManagerZmienił** dodać powiadomienie.
Rejestrowanie, gdy **IReliableDictionary** jest dodawany do **IReliableStateManager** zapewnia, że nie przegapisz żadnych powiadomień.

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
> **ProcessStateManagerSingleEntityNotification** jest metoda przykładowa, że poprzedni **OnStateManagerChangedHandler** przykład wywołania.
> 
> 

Poprzedni kod ustawia interfejs **IReliableNotificationAsyncCallback** wraz ze **słownikiemzmienił**. Ponieważ **NotifyDictionaryRebuildEventArgs** zawiera **IAsyncEnumerable** interfejs - który musi być wyliczony asynchronicznie - odbudować powiadomienia są uruchamiane przez **RebuildNotificationAsyncCallback** zamiast **OnDictionaryChangedHandler**.

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
> W poprzednim kodzie, w ramach przetwarzania powiadomień o przebudowie, najpierw zachowany stan zagregowany jest czyszczony. Ponieważ niezawodna kolekcja jest przebudowywana z nowym stanem, wszystkie poprzednie powiadomienia są bez znaczenia.
> 
> 

Program obsługi zdarzeń **DictionaryChanged** używa **NotifyDictionaryChangedEventArgs,** aby podać szczegółowe informacje o zdarzeniu.
**NotifyDictionaryChangedEventArgs** ma pięć podklas. Użyj właściwości akcji w **NotifyDictionaryChangedEventArgs** do rzutowania **NotifyDictionaryChangedEventArgs** do poprawnej podklasy:

* NotifyDictionaryChangedAction.Rebuild : **NotifyDictionaryRebuildEventArgs** **NotifyDictionionaryChangedAction.Rebuild**: NotifyDictionaryRebuildEventArgs NotifyDictionionary
* NotifyDictionaryChangedAction.Clear : NotifyDictionaryClearEventArgs **NotifyDictionionaryChangedaction.Clear**: **NotifyDictionaryClearEventArgs NotifyDictionionaryChangargs NotifyDictionionaryClearEventArgs NotifyDiction**
* **NotifyDictionaryChangedaction.Add**: NotifyDictionaryItemaddedEventArgs NotifyDictionaryChangedaction.Add : **NotifyDictionaryItemedeventArgs NotifyDictionary**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedaction.Remove**: **NotifyDictionaryItemRemovedEventArgs** NotifyDictionaryChangedaction.Remove : NotifyDictionaryItemRemovedEventArgs NotifyDictionary

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
* *Wykonaj* zdarzenia powiadomień tak szybko, jak to możliwe.
* *Nie* należy wykonywać żadnych kosztownych operacji (na przykład operacji we/wy) w ramach zdarzeń synchronicznych.
* *Sprawdź* typ akcji przed przetworzeniem zdarzenia. Nowe typy akcji mogą zostać dodane w przyszłości.

Oto kilka rzeczy, o których należy pamiętać:

* Powiadomienia są uruchamiane w ramach wykonywania operacji. Na przykład powiadomienie przywracania jest uruchamiany jako ostatni krok operacji przywracania. Przywracanie nie zakończy się, dopóki zdarzenie powiadomienia jest przetwarzane.
* Ponieważ powiadomienia są uruchamiane jako część operacji stosowania, klienci widzą tylko powiadomienia dla operacji zatwierdzonych lokalnie. A ponieważ operacje są gwarantowane tylko do lokalnego zatwierdzania (innymi słowy, rejestrowane), mogą lub nie mogą być cofnięte w przyszłości.
* Na ścieżce ponawianie pojedyncze powiadomienie jest uruchamiane dla każdej zastosowanej operacji. Oznacza to, że jeśli transakcja T1 zawiera Create(X), Delete(X) i Create(X), otrzymasz jedno powiadomienie o utworzeniu X, jedno dla usunięcia i jedno dla ponownego utworzenia w tej kolejności.
* W przypadku transakcji, które zawierają wiele operacji, operacje są stosowane w kolejności, w jakiej zostały odebrane w replice podstawowej od użytkownika.
* W ramach przetwarzania postępu fałszywych, niektóre operacje mogą zostać cofnięte. Powiadomienia są wywoływane dla takich operacji cofania, toczenia stanu repliki z powrotem do punktu stabilnego. Jedną z ważnych różnic powiadomień cofania jest to, że zdarzenia, które mają zduplikowane klucze są agregowane. Na przykład jeśli transakcja T1 jest cofnięta, zobaczysz pojedyncze powiadomienie delete(X).

## <a name="next-steps"></a>Następne kroki
* [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
* [Tworzenie kopii zapasowych i przywracanie niezawodnych usług (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

