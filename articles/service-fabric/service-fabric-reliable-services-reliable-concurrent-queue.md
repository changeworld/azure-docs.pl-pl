---
title: ReliableConcurrentQueue w usłudze Azure Service Fabric
description: ReliableConcurrentQueue jest kolejką o wysokiej przepływności, co umożliwia równoległe umieszczeniu i dequeues.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: dbdfa4686c047fa7cf5d74cd9aca768447f9db93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774016"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Wprowadzenie do ReliableConcurrentQueue w usłudze Azure Service Fabric
Niezawodna kolejka współbieżna jest kolejką asynchronicznego, transakcji i replikowane concurrency które funkcje wysokiej umieścić w kolejce i pobierać operacji. Zaprojektowano w celu dostarczania wysokiej przepływności i małego opóźnienia, złagodzić ścisłe porządkowanie FIFO dostarczone przez [niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx) i przekazuje porządkowanie największej staranności.

## <a name="apis"></a>Interfejsy API

|Kolejka współbieżna                |Niezawodna kolejka współbieżna                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Zadanie EnqueueAsync (tx ITransaction elementu T)                       |
| wartość logiczna TryDequeue (out wynik T)  | Zadanie < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Porównanie z [niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Niezawodna kolejka współbieżna jest oferowany jako alternatywa [niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx). Należy jej używać w przypadku gdy ścisłe porządkowanie FIFO nie jest wymagane, jako gwarantujących FIFO wymaga to z kompromisem ze współbieżnością.  [Niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx) blokady są używane do wymuszania, FIFO kolejności, co najwyżej jedna transakcja może umieścić w kolejce i co najwyżej jedna transakcja, możliwość usuwania z kolejki w danym momencie. W odróżnieniu od niezawodna kolejka współbieżna zwalnia ograniczenie sortowania i umożliwia liczba jednoczesnych transakcji przeplot ich dodawania do kolejki operacji. Kolejność optymalnych zostanie podana, jednak względną kolejność dwóch wartości w niezawodna kolejka współbieżna może nigdy nie może zagwarantować.

Niezawodna kolejka współbieżna zapewnia wyższą przepływność i mniejsze opóźnienia niż [niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx) zawsze wtedy, gdy istnieje wielu jednoczesnych transakcji wykonywania umieszczeniu i/lub dequeues.

Przykład przypadek użycia jest ReliableConcurrentQueue [kolejki komunikatów](https://en.wikipedia.org/wiki/Message_queue) scenariusza. W tym scenariuszu jednego lub kilku producentów komunikat tworzyć i dodawać elementy do kolejki, a co najmniej jeden odbiorcami komunikatów ściągają komunikaty z kolejki i przetwarzać je. Wielu producentów i konsumentów może działać niezależnie, za pomocą równoczesnych transakcji, aby przetworzyć kolejkę.

## <a name="usage-guidelines"></a>Wytyczne dotyczące użycia
* Kolejka oczekuje, że elementów w kolejce mają okres przechowywania niski. Oznacza to, że elementy nie będzie pozostają w kolejce przez długi czas.
* Kolejka nie gwarantuje ścisłe porządkowanie FIFO.
* Kolejka nie odczytuje swój własny zapisy. Jeśli element jest dodawanych do kolejki w obrębie transakcji, nie będą widoczne na dequeuer w ramach tej samej transakcji.
* Dequeues nie są od siebie odizolowane. Jeśli element *A* jest usuwane z kolejki w transakcji *txnA*, nawet jeśli *txnA* nie zostanie zatwierdzony, element *A* nie będzie widoczny na równoczesne Transakcja *txnB*.  Jeśli *txnA* przerywa, *A* staną się widoczne dla *txnB* natychmiast.
* *TryPeekAsync* zachowanie można zaimplementować przy użyciu *TryDequeueAsync* i następnie Przerywanie transakcji. Na przykład można znaleźć w sekcji wzorce programowania.
* Liczba nie jest transakcyjna. Może służyć do poznać liczbę elementów w kolejce, ale reprezentuje punkt w czasie i nie można polegać.
* Nie ma zostać wykonane przetwarzanie kosztowne dequeued elementów, gdy transakcja jest aktywny, aby uniknąć długotrwałych transakcji, które mogą mieć wpływ na wydajność systemu.

## <a name="code-snippets"></a>Wstawki kodu
Przyjrzyjmy się kilku fragmenty kodu i ich oczekiwane dane wyjściowe. Obsługa wyjątków jest ignorowany w tej sekcji.

### <a name="enqueueasync"></a>EnqueueAsync
Poniżej przedstawiono kilka fragmenty kodu przy użyciu EnqueueAsync następuje ich oczekiwane dane wyjściowe.

- *Przypadek 1: Pojedyncze zadanie w kolejce*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zakończyło się pomyślnie i czy nie zostały wprowadzone żadne transakcje współbieżnych modyfikowanie kolejki. Użytkownika można oczekiwać, że kolejka zawiera elementów w jednym z następujących zamówień:

> 10, 20
> 
> 20, 10


- *Przypadek 2: Parallel Enqueue Task*

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

Przyjęto założenie, że zadań została ukończona pomyślnie, czy zadania uruchomione w sposób równoległy i czy nie było żadnych innych równoczesnych transakcji modyfikowanie kolejki. Wnioskowanie nie można wykonywać na temat kolejności elementów w kolejce. Dla tego fragmentu kodu elementy może występować w dowolnym z 4! możliwe porządkowania.  Kolejka będzie próbował zachować elementy w kolejności, oryginalnym (umieszczonych w kolejce), ale może być zmuszona, aby zmienić ich kolejność z powodu jednoczesnych operacji lub błędów.


### <a name="dequeueasync"></a>DequeueAsync
Poniżej przedstawiono kilka fragmenty kodu przy użyciu TryDequeueAsync następuje oczekiwane dane wyjściowe. Załóżmy, że jest już ona wypełniana przy użyciu następujących elementów w kolejce:
> 10, 20, 30, 40, 50, 60

- *Przypadek 1: Pojedynczej kolejki zadań*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zakończyło się pomyślnie i czy nie zostały wprowadzone żadne transakcje współbieżnych modyfikowanie kolejki. Ponieważ wnioskowania nie może się o kolejność elementów w kolejce, wszystkie trzy elementy mogą być usuwane z kolejki, w dowolnej kolejności. Kolejka będzie próbował zachować elementy w kolejności, oryginalnym (umieszczonych w kolejce), ale może być zmuszona, aby zmienić ich kolejność z powodu jednoczesnych operacji lub błędów.  

- *Przypadek 2: Równoległe kolejki zadań*

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

Przyjęto założenie, że zadań została ukończona pomyślnie, czy zadania uruchomione w sposób równoległy i czy nie było żadnych innych równoczesnych transakcji modyfikowanie kolejki. Ponieważ wnioskowania nie można wykonywać na temat kolejności elementów w kolejce, list *dequeue1* i *dequeue2* będzie każdy zawiera dwa elementy, w dowolnej kolejności.

Taki sam element będzie *nie* pojawiają się w obu list. W związku z tym jeśli ma dequeue1 *10*, *30*, a następnie miałby dequeue2 *20*, *40*.

- *Przypadek 3: Usuń z kolejki kolejności z przerwać transakcji*

Trwa przerywanie transakcji z śledząc dequeues umieszcza elementy z powrotem na główny kolejki. Kolejność, w której elementy są ponownie wprowadzane na główny kolejki nie jest gwarantowana. Przyjrzyjmy się następujący kod:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Załóżmy, że elementy były usuwane z kolejki w następującej kolejności:
> 10, 20

Gdy firma Microsoft przerwać transakcji, elementy były dodawane do głowy kolejki w jednym z następujących zamówień:
> 10, 20
> 
> 20, 10

To samo dotyczy wszystkich przypadków, gdy transakcja nie została pomyślnie *przydzielony*.

## <a name="programming-patterns"></a>Wzorce programowania
W tej sekcji, Przyjrzyjmy się kilku programowania wzorców, które mogą być pomocne przy użyciu ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dequeues usługi Batch
Zalecanym programowania wzorzec jest konsument zadania usługi batch jego dequeues zamiast przeprowadzania jednej kolejki w danym momencie. Użytkownik może wybrać ograniczać opóźnienia między każdej partii lub rozmiar partii. Poniższy fragment kodu przedstawia ten model programowania.  Należy pamiętać, że w tym przykładzie przetwarzanie odbywa się po transakcja została zatwierdzona, więc gdyby błąd występuje podczas przetwarzania nieprzetworzonych elementy zostaną utracone bez konieczności został przetworzony.  Alternatywnie przetwarzanie może odbywać się w zakresie transakcji, ale to może mieć negatywny wpływ na wydajność i wymaga obsługi elementów już przetworzone.

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

### <a name="best-effort-notification-based-processing"></a>Przetwarzanie oparte na powiadomienie optymalnych
Inny interesujący wzorzec programowania za pomocą interfejsu API licznik. W tym miejscu można zaimplementować największej staranności powiadomień na podstawie przetwarzania dla kolejki. Kolejka liczba może służyć do ograniczania umieścić w kolejce "lub" task usuwania z kolejki.  Należy pamiętać, że co w poprzednim przykładzie, ponieważ przetwarzanie zachodzi poza tą transakcją, nieprzetworzonych elementy mogą zostać utracone jeśli błąd wystąpi podczas przetwarzania.

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
Nie można zagwarantować opróżniania kolejki ze względu na charakter współbieżnych struktury danych.  Jest to możliwe, że nawet jeśli żadne operacje użytkownika w kolejce są aktywne, wywołania określonej TryDequeueAsync mogą nie zwracać element, który był wcześniej umieszczonych w kolejce i zatwierdzone.  Element umieszczonych w kolejce jest gwarantowane, *ostatecznie* stają się widoczne dla kolejki, jednak bez mechanizm komunikacji poza pasmem konsumenta niezależnych nie wiedzieć, że kolejki osiągnął stan nawet wtedy, gdy wszyscy producenci zostały zatrzymane, a nie nowy umieścić, dozwolone są operacje. W związku z tym Operacja opróżniania jest optymalny zaimplementowanego poniżej.

Użytkownik, należy zatrzymać dalsze producenta i konsumenta zadania i poczekaj, aż wszystkie aktywne transakcje zatwierdzenia lub przerwania przed podjęciem próby opróżnienia kolejki.  Jeśli użytkownik zna oczekiwanej liczby elementów w kolejce, można skonfigurować powiadomienia, które sygnalizują, że wszystkie elementy mają został usunięty z kolejki.

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
Nie obejmuje ReliableConcurrentQueue *TryPeekAsync* interfejsu api. Użytkownicy będą mogli uzyskać wartość szczytową semantycznych przy użyciu *TryDequeueAsync* i następnie Przerywanie transakcji. W tym przykładzie dequeues są przetwarzane tylko wtedy, gdy wartość elementu jest większa niż *10*.

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
* [Przewodnik Szybki Start usług Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
* [Niezawodne usługi Kopia zapasowa i przywracanie (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguracja Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Wprowadzenie do usług interfejsu API sieci Web w usłudze Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Zaawansowane zastosowania usług Reliable Services w modelu programowania](service-fabric-reliable-services-advanced-usage.md)
* [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
