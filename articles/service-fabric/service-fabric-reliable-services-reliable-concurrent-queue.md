---
title: ReliableConcurrentQueue w sieci szkieletowej usług Azure
description: ReliableConcurrentQueue jest kolejką wysokiej przepustowości, co umożliwia równoległe enqueues i dequeues.
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: e04123f7870921a2979564d0f6c68424d4d7711c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206581"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Wprowadzenie do ReliableConcurrentQueue w sieci szkieletowej usług Azure
Niezawodne równoczesnych kolejka jest asynchroniczne, transakcyjne i replikowane kolejki które concurrency wysokiej funkcji umieścić w kolejce i usuwania z kolejki operacji. Został zaprojektowany w celu dostarczenia wysokiej przepływności i małe opóźnienia za mniejsze strict kolejności FIFO dostarczonych przez [kolejka niezawodnych](https://msdn.microsoft.com/library/azure/dn971527.aspx) i przekazuje optymalnych porządkowania.

## <a name="apis"></a>Interfejsy API

|Współbieżne kolejki                |Niezawodna kolejka współbieżna                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Zadanie EnqueueAsync (tx ITransaction elementu T)                       |
| wartość logiczna TryDequeue (out wynik T)  | Zadanie < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | długie Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Porównanie z [niezawodnej kolejki](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Niezawodne równoczesnych kolejki jest oferowany jako alternatywę do [kolejka niezawodnych](https://msdn.microsoft.com/library/azure/dn971527.aspx). Tej opcji należy używać w przypadku, gdy strict kolejności FIFO nie jest wymagane, jako gwarantujących FIFO wymaga zależnościami z współbieżności.  [Kolejka niezawodnych](https://msdn.microsoft.com/library/azure/dn971527.aspx) używa blokad wymusić kolejności FIFO, co najwyżej jedna transakcja może umieścić w kolejce i możliwość usuwania z kolejki w czasie co najwyżej jedna transakcja. Z kolei niezawodnej kolejki równoczesnych zwalnia ograniczenie porządkowania i umożliwia liczba równoczesnych transakcji interleave ich umieścić w kolejce do usuwania z kolejki operacji. Porządkowanie optymalnych została podana, jednak względne uporządkowanie dwóch wartości w niezawodnej kolejki równoczesnych nigdy nie można zagwarantować.

Niezawodne kolejki równoczesnych zapewnia wyższą przepływność i mniejsze opóźnienia niż [kolejka niezawodnych](https://msdn.microsoft.com/library/azure/dn971527.aspx) zawsze, gdy istnieje wiele równoczesnych transakcji wykonywania enqueues i/lub dequeues.

Przypadek użycia próbki jest ReliableConcurrentQueue [kolejki komunikatów](https://en.wikipedia.org/wiki/Message_queue) scenariusza. W tym scenariuszu jeden lub więcej komunikatów producentów tworzyć i Dodaj elementy do kolejki, a co najmniej jednego odbiorcy wiadomości ściągać komunikaty z kolejki i przetwarzanie ich. Wiele producenci i konsumenci może pracować wielel osób, aby przetworzyć kolejki przy użyciu równoczesnych transakcji.

## <a name="usage-guidelines"></a>Wskazówki dotyczące użycia
* Kolejki oczekuje, że okres przechowywania mało elementów w kolejce. Oznacza to elementy nie może pozostać w kolejce przez długi czas.
* Kolejka nie gwarantuje kolejności FIFO strict.
* Kolejka nie odczytuje własną zapisy. Jeśli element jest dodawanych do kolejki w ramach transakcji, nie będą widoczne dla dequeuer w ramach tej samej transakcji.
* Dequeues nie są od siebie odizolowane. Jeśli element *A* jest usuniętej w transakcji *txnA*, nawet jeśli *txnA* nie została przekazana, element *A* nie będą widoczne dla równoczesne Transakcja *txnB*.  Jeśli *txnA* przerywa, *A* staną się widoczne dla *txnB* natychmiast.
* *TryPeekAsync* zachowanie można zaimplementować przy użyciu *TryDequeueAsync* i Trwa przerywanie transakcji. Na przykład można znaleźć w sekcji wzorce programowania.
* Liczba jest nietransakcyjnej. Może służyć do poznać liczbę elementów w kolejce, ale reprezentuje punkt w czasie i nie może opierać się.
* Nie ma zostać wykonane przetwarzanie kosztowne dequeued elementów, gdy transakcja jest aktywna, aby uniknąć długotrwałe transakcje, które mogą mieć wpływ na wydajność systemu.

## <a name="code-snippets"></a>Wstawki kodu
Daj nam przyjrzeć się kilka fragmentów kodu i ich oczekiwane dane wyjściowe. Obsługa wyjątków jest ignorowany w tej sekcji.

### <a name="enqueueasync"></a>EnqueueAsync
Poniżej przedstawiono kilka fragmenty kodu przy użyciu EnqueueAsync następuje ich oczekiwane dane wyjściowe.

- *Przypadek 1: Pojedynczy umieścić w kolejce zadań*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało pomyślnie zakończone i czy nie zostały żadne transakcje równoczesnych modyfikowanie kolejki. Użytkownika można oczekiwać w kolejce zawierać elementów w jednym z następujących zleceń:

> 10, 20

> 20, 10


- *Przypadek 2: Równoległe umieścić w kolejce zadań*

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

Załóżmy, czy zadania zakończyła się pomyślnie, że zadania uruchomione równolegle i że nie wystąpiły żadne inne transakcje równoczesnych modyfikowanie kolejki. Brak wnioskowania nie jest możliwe o kolejności elementów w kolejce. Na poniższym przykładzie elementów może występować w żadnym z 4! możliwe uporządkowania.  Kolejka będzie próbował zachować elementy w kolejności (umieszczonych w kolejce), ale można wymusić ponownie określić ich kolejność z powodu równoczesnych operacji lub błędów.


### <a name="dequeueasync"></a>DequeueAsync
Poniżej przedstawiono kilka fragmenty kodu przy użyciu TryDequeueAsync następuje oczekiwane dane wyjściowe. Załóżmy, że kolejka jest już wypełniony następujących elementów w kolejce:
> 10, 20, 30, 40, 50, 60

- *Przypadek 1: Jednym usuwania z kolejki zadań*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało pomyślnie zakończone i czy nie zostały żadne transakcje równoczesnych modyfikowanie kolejki. Ponieważ żadne wnioskowania nie jest możliwe o kolejności elementów w kolejce, wszystkie trzy elementy może być usunięty z kolejki, w dowolnej kolejności. Kolejka będzie próbował zachować elementy w kolejności (umieszczonych w kolejce), ale można wymusić ponownie określić ich kolejność z powodu równoczesnych operacji lub błędów.  

- *Przypadek 2: Dequeue równoległych zadań*

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

Załóżmy, czy zadania zakończyła się pomyślnie, że zadania uruchomione równolegle i że nie wystąpiły żadne inne transakcje równoczesnych modyfikowanie kolejki. Ponieważ żadne wnioskowania nie jest możliwe o kolejności elementów w kolejce listy *dequeue1* i *dequeue2* każdy będzie zawierać dwa elementy, w dowolnej kolejności.

Taki sam element zostanie *nie* są wyświetlane na listach obu. W związku z tym jeśli dequeue1 *10*, *30*, następnie byłyby dequeue2 *20*, *40*.

- *Przypadek 3: Usuwania z kolejki w kolejności z przerwać transakcji*

Przerywanie transakcji z locie dequeues naraża elementy z powrotem na head kolejki. Kolejność, w której elementy są odłożyć na head kolejki nie jest gwarantowana. Daj nam przyjrzeć się następującym kodem:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Załóżmy, że elementy zostały usuniętej w następującej kolejności:
> 10, 20

Gdy firma Microsoft przerwać transakcji, elementy zostanie dodany do head kolejki w jednym z następujących zleceń:
> 10, 20

> 20, 10

To samo dotyczy wszystkich przypadkach, gdy transakcja nie została pomyślnie *zatwierdzone*.

## <a name="programming-patterns"></a>Wzorce programowania
W tej sekcji Sprawdź Daj nam kilka programowania w języku wzorców, które mogą być przydatne przy użyciu ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dequeues partii
A zalecane jest wzorzec programowania dla konsumentów zadania wsadowego jego dequeues zamiast wykonaj jedną usuwania z kolejki w czasie. Użytkownik może wybrać ograniczać opóźnienia między każdej partii lub rozmiar partii. Poniższy fragment kodu przedstawia ten model programowania.  Należy pamiętać, że w tym przykładzie przetwarzanie odbywa się po transakcja została zatwierdzona, tak jakby był błąd występuje podczas przetwarzania, nieprzetworzone elementy zostaną utracone bez przetworzeniu.  Alternatywnie przetwarzanie może odbywać się w zakresie transakcji, jednak to może mieć negatywny wpływ na wydajność i wymaga obsługi elementów już przetworzone.

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

### <a name="best-effort-notification-based-processing"></a>Przetwarzanie powiadomień na podstawie optymalnych
Inny interesujące wzorzec programowania używa interfejsu API Count. W tym miejscu możemy wdrożyć optymalnych powiadomień na podstawie przetwarzania dla kolejki. Kolejki licznik może służyć do ograniczania umieścić w kolejce "lub" task kolejki.  Należy pamiętać, że co w poprzednim przykładzie, ponieważ przetwarzanie zachodzi poza transakcją, nieprzetworzone elementy mogą zostać utracone jeśli wystąpi błąd podczas przetwarzania.

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

### <a name="best-effort-drain"></a>Optymalny opróżniania
Nie można zagwarantować opróżniania kolejki z powodu równoczesnych charakter struktury danych.  Jest to możliwe, że nawet jeśli nie ma operacji użytkownika w kolejce znajdują się aktywny, określonego wywołania TryDequeueAsync nie może zwracać element, który został wcześniej umieszczonych w kolejce i zatwierdzone.  Jest gwarantowane elementu umieszczonych w kolejce *ostatecznie* stają się widoczne do usuwania z kolejki, bez mechanizm komunikacji poza pasmem konsumenta niezależne nie wiedzieć, że kolejka została osiągnięta nawet jeśli stan wszystkich producentów zostały zatrzymane, a nie nowy umieścić operacje są niedozwolone. W związku z tym Operacja opróżniania jest optymalnych zgodnie z implementacją poniżej.

Użytkownik należy zatrzymać wszystkie dalsze producentów i zadania odbiorców i poczekaj, aż locie transakcji do zatwierdzenia lub przerwania, przed podjęciem próby opróżnienia kolejki.  Jeśli użytkownik zna oczekiwana liczba elementów w kolejce, można skonfigurować powiadomienia, która sygnalizuje, że wszystkie elementy zostały usuniętej.

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
Nie ma ReliableConcurrentQueue *TryPeekAsync* interfejsu api. Użytkownicy mogą uzyskać wglądu semantycznych przy użyciu *TryDequeueAsync* i Trwa przerywanie transakcji. W tym przykładzie dequeues są przetwarzane tylko wtedy, gdy wartość elementu jest większa niż *10*.

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

## <a name="must-read"></a>Należy odczytać
* [Niezawodne usługi Szybki Start](service-fabric-reliable-services-quick-start.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
* [Niezawodne usługi tworzenia kopii zapasowej i przywracania (odzyskiwania po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Niezawodne stanu Menedżera konfiguracji](service-fabric-reliable-services-configuration.md)
* [Wprowadzenie do usług interfejsu API sieci Web sieci szkieletowej usług](service-fabric-reliable-services-communication-webapi.md)
* [Zaawansowanego wykorzystania niezawodnej Model programowania usług](service-fabric-reliable-services-advanced-usage.md)
* [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
