---
title: ReliableConcurrentQueue w sieci szkieletowej usług Azure
description: ReliableConcurrentQueue to kolejka o wysokiej przepływie, która umożliwia równoległe kolejki i kolejki.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462727"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Wprowadzenie do reliableconcurrentQueue w sieci szkieletowej usług Azure
Niezawodna kolejka współbieżna jest kolejką asynchronizalną, transakcyjną i replikowaną, która oferuje wysoką współbieżność operacji enqueue i dequeue. Został on zaprojektowany, aby zapewnić wysoką przepustowość i małe opóźnienia przez rozluźnienie ścisłego zamawiania FIFO przez [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) i zamiast tego zapewnia najlepsze zamówienie.

## <a name="apis"></a>Interfejsy API

|Kolejka współbieżna                |Niezawodna kolejka współbieżna                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | EnqueueAsync zadania(ITransaction tx, T item)                       |
| bool TryDequeue (wynik T)  | Zadanie< Wartość warunkowa < T > > TryDequeueAsync(ITransaction tx)  |
| int Liczba()                    | długi Count()                                                     |

## <a name="comparison-with-reliable-queue"></a>Porównanie z [niezawodną kolejką](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Niezawodna kolejka współbieżna jest oferowana jako alternatywa dla [niezawodnej kolejki.](https://msdn.microsoft.com/library/azure/dn971527.aspx) Powinien być stosowany w przypadkach, gdy ścisłe zamawianie FIFO nie jest wymagane, ponieważ zagwarantowanie FIFO wymaga kompromisu z współbieżnością.  [Niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx) używa blokad do wymuszania zamawiania FIFO, z co najwyżej jedną transakcją dozwoloną do umieszczania w kolejce i co najwyżej jedną transakcją dozwoloną do usuwania kolejki naraz. W porównaniu z reliable concurrent queue rozluźnia ograniczenie zamawiania i umożliwia dowolną liczbę równoczesnych transakcji przeplatać ich enqueue i dequeue operacji. Kolejność najlepszych starań jest dostępna, jednak względna kolejność dwóch wartości w niezawodnej kolejce współbieżnej nigdy nie może być zagwarantowana.

Niezawodna kolejka współbieżna zapewnia większą przepływność i mniejsze opóźnienia niż [niezawodne kolejki,](https://msdn.microsoft.com/library/azure/dn971527.aspx) gdy istnieje wiele równoczesnych transakcji wykonujących enqueues i/lub dequeues.

Przykładowy przypadek użycia dla ReliableConcurrentQueue jest scenariusz [kolejki komunikatów.](https://en.wikipedia.org/wiki/Message_queue) W tym scenariuszu co najmniej jeden producent wiadomości utworzyć i dodać elementy do kolejki, a co najmniej jeden konsument wiadomości ściągać wiadomości z kolejki i przetwarzać je. Wielu producentów i konsumentów może pracować niezależnie, przy użyciu równoczesnych transakcji w celu przetworzenia kolejki.

## <a name="usage-guidelines"></a>Zalecenia dotyczące użytkowania
* Kolejka oczekuje, że elementy w kolejce mają niski okres przechowywania. Oznacza to, że elementy nie pozostaną w kolejce przez długi czas.
* Kolejka nie gwarantuje ścisłego zamawiania FIFO.
* Kolejka nie odczytuje własnych zapisów. Jeśli element jest w kolejce w ramach transakcji, nie będzie widoczny dla dequeuer w ramach tej samej transakcji.
* Dequeues nie są odizolowane od siebie. Jeśli towar *A* jest odsunięty w kolejce do transakcji *txnA*, nawet jeśli *txnA* nie jest zatwierdzony, element *A* nie będzie widoczny dla równoczesnych transakcji *txnB*.  Jeśli *txnA* zostanie przerwane, *A* stanie się natychmiast widoczne dla *txnB.*
* *Zachowanie TryPeekAsync* można zaimplementować przy użyciu *TryDequeueAsync,* a następnie przerywając transakcję. Przykład tego zachowania można znaleźć w sekcji Wzorce programowania.
* Liczba nietransakcyjna. Może służyć do zorientowania się, ile elementów w kolejce, ale reprezentuje punkt w czasie i nie można polegać.
* Kosztowne przetwarzanie towarów de-queued nie powinny być wykonywane, gdy transakcja jest aktywna, aby uniknąć długotrwałych transakcji, które mogą mieć wpływ na wydajność systemu.

## <a name="code-snippets"></a>Wstawki kodu
Przyjrzyjmy się kilku fragmentów kodu i ich oczekiwanych wyjść. Obsługa wyjątków jest ignorowana w tej sekcji.

### <a name="instantiation"></a>Tworzenie wystąpienia
Tworzenie wystąpienia niezawodnej kolejki współbieżnej jest podobny do innych niezawodnych kolekcji.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync (EnqueueAsync)
Oto kilka fragmentów kodu do korzystania z EnqueueAsync następuje ich oczekiwane dane wyjściowe.

- *Przypadek 1: Pojedyncze zadanie enqueue*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało ukończone pomyślnie i że nie było żadnych równoczesnych transakcji modyfikujących kolejkę. Użytkownik może oczekiwać, że kolejka będzie zawierać elementy w dowolnym z następujących zamówień:

> 10, 20
> 
> 20, 10


- *Przypadek 2: Równoległe zadanie enqueue*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadania zostały wykonane pomyślnie, że zadania działały równolegle i że nie było żadnych innych równoczesnych transakcji modyfikujących kolejkę. Nie można wnioskować o kolejności elementów w kolejce. Dla tego fragmentu kodu, elementy mogą pojawić się w dowolnym z 4! możliwych zamówień.  Kolejka spróbuje zachować elementy w oryginalnej (w kolejce) kolejności, ale może być zmuszony do ich ponownego zamówienia z powodu równoczesnych operacji lub błędów.


### <a name="dequeueasync"></a>DequeueAsync ( DequeueAsync )
Oto kilka fragmentów kodu do korzystania z TryDequeueAsync następuje oczekiwane dane wyjściowe. Załóżmy, że kolejka jest już wypełniona następującymi elementami w kolejce:
> 10, 20, 30, 40, 50, 60

- *Przypadek 1: Pojedyncze zadanie dequeue*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało ukończone pomyślnie i że nie było żadnych równoczesnych transakcji modyfikujących kolejkę. Ponieważ nie można wnioskować o kolejności elementów w kolejce, wszystkie trzy elementy mogą być usuwane z kolejki, w dowolnej kolejności. Kolejka spróbuje zachować elementy w oryginalnej (w kolejce) kolejności, ale może być zmuszony do ich ponownego zamówienia z powodu równoczesnych operacji lub błędów.  

- *Przypadek 2: Równoległe zadanie dequeue*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Załóżmy, że zadania zostały wykonane pomyślnie, że zadania działały równolegle i że nie było żadnych innych równoczesnych transakcji modyfikujących kolejkę. Ponieważ nie można wnioskować o kolejności elementów w *kolejce, listy dequeue1* i *dequeue2* będą zawierać wszystkie dwa elementy, w dowolnej kolejności.

Ten sam element *nie* pojawi się na obu listach. Stąd, jeśli dequeue1 ma *10*, *30*, a następnie dequeue2 będzie miał *20,* *40*.

- *Przypadek 3: Dequeue Zamawianie z przerwą w transakcji*

Przerwanie transakcji z dequeues w locie stawia elementy z powrotem na czele kolejki. Kolejność, w jakiej elementy są umieszczane z powrotem na głowie kolejki, nie jest gwarantowana. Przyjrzyjmy się następującej kodzie:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Załóżmy, że elementy zostały odsuniętye w następującej kolejności:
> 10, 20

Po przerwaniu transakcji, towary zostaną dodane z powrotem do głowy kolejki w dowolnym z następujących zamówień:
> 10, 20
> 
> 20, 10

To samo dotyczy wszystkich przypadków, w których transakcja nie została pomyślnie *zatwierdzona.*

## <a name="programming-patterns"></a>Wzorce programowania
W tej sekcji przyjrzyjmy się kilku wzorców programowania, które mogą być pomocne w użyciu ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dequeues partii
Zalecany wzorzec programowania jest dla zadania konsumenta do partii jego dequeues zamiast wykonywania jednej dequeue naraz. Użytkownik może wybrać, aby ograniczyć opóźnienia między każdej partii lub wielkości partii. Poniższy fragment kodu pokazuje ten model programowania. Należy pamiętać, w tym przykładzie przetwarzania odbywa się po transakcji jest zatwierdzona, więc jeśli błąd miał wystąpić podczas przetwarzania, nieprzetwochnione elementy zostaną utracone bez zostały przetworzone.  Alternatywnie przetwarzanie można wykonać w zakresie transakcji, jednak może mieć negatywny wpływ na wydajność i wymaga obsługi już przetworzonych elementów.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Przetwarzanie oparte na powiadomieniach o najlepszym wysiłku
Innym ciekawym wzorcem programowania używa interfejsu API count. W tym miejscu możemy zaimplementować przetwarzanie oparte na powiadomieniach o najlepszym wysiłku dla kolejki. Liczba kolejki może służyć do ograniczania enqueue lub zadania dequeue.  Należy zauważyć, że podobnie jak w poprzednim przykładzie, ponieważ przetwarzanie odbywa się poza transakcją, nieprzetworzene towary mogą zostać utracone, jeśli wystąpi błąd podczas przetwarzania.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Najlepszy wysiłek drenażu
Nie można zagwarantować drenażu kolejki ze względu na równoczesny charakter struktury danych.  Jest możliwe, że nawet jeśli żadne operacje użytkownika w kolejce są w locie, określone wywołanie TryDequeueAsync nie może zwrócić elementu, który został wcześniej w kolejce i zatwierdzone.  Ujmowany w kolejce element jest *gwarantowany,* aby ostatecznie stać się widocznym dla dequeue, jednak bez mechanizmu komunikacji poza pasmem niezależny konsument nie może wiedzieć, że kolejka osiągnęła stan równowagi, nawet jeśli wszyscy producenci zostali zatrzymani i nie są dozwolone żadne nowe operacje w kolejce. W związku z tym, operacji drenażu jest najlepszym wysiłkiem, jak zaimplementowano poniżej.

Użytkownik powinien zatrzymać wszystkie dalsze zadania producenta i konsumenta i czekać na wszelkie transakcje w locie, aby zatwierdzić lub przerwać, przed podjęciem próby opróżnienia kolejki.  Jeśli użytkownik zna oczekiwaną liczbę elementów w kolejce, może skonfigurować powiadomienie, które sygnalizuje, że wszystkie elementy zostały odsuniętym w kolejce.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Peek
ReliableConcurrentQueue nie zapewnia interfejsu API *TryPeekAsync.* Użytkownicy mogą uzyskać semantyczne wgląd przy użyciu *TryDequeueAsync,* a następnie przerywając transakcji. W tym przykładzie dequeues są przetwarzane tylko wtedy, gdy wartość elementu jest większa niż *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Lektura obowiązkowa
* [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Powiadomienia o niezawodnych usługach](service-fabric-reliable-services-notifications.md)
* [Niezawodne usługi kopii zapasowej i przywracania (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Niezawodna konfiguracja menedżera stanu](service-fabric-reliable-services-configuration.md)
* [Wprowadzenie do usług sieci Web sieci Web sieci Web usługi Sieci Web](service-fabric-reliable-services-communication-webapi.md)
* [Zaawansowane wykorzystanie modelu programowania niezawodnych usług](service-fabric-reliable-services-advanced-usage.md)
* [Informacje dla deweloperów dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
