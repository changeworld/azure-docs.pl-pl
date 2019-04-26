---
title: Wprowadzenie do usługi queue storage i Visual Studio podłączone usługi (projekty, zadania WebJob) | Dokumentacja firmy Microsoft
description: Jak rozpocząć korzystanie z usługi Azure Queue storage projektu zadania WebJob, po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f6f1a3a7f0a406e1dbb40f4bfc6a358da7ac68fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391237"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Wprowadzenie do usługi Azure Queue storage i Visual Studio podłączone usługi (projekty, zadania WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpocząć korzystanie z usługi Azure Queue storage w projekcie programu Visual Studio zadań WebJob platformy Azure, po użytkownik utworzył, lub odwołanie do konta usługi Azure storage za pomocą programu Visual Studio **Dodaj usługi połączone** okno dialogowe. Po dodaniu konta magazynu do projektu zadania WebJob przy użyciu programu Visual Studio **Dodaj usługi połączone** okno dialogowe, są zainstalowane odpowiednie pakiety NuGet usługi Azure Storage, odpowiednie odwołania .NET są dodawane do projektu, i Parametry połączenia dla konta magazynu są aktualizowane w pliku App.config.  

Ten artykuł zawiera C# przykłady kodu, które pokazują, jak używać zestawu Azure WebJobs SDK w wersji 1.x za pomocą usługi Azure Queue storage.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Zobacz [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) Aby uzyskać więcej informacji. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak wyzwolić funkcję po otrzymaniu komunikatu w kolejce
Aby napisać funkcja wywołująca przez zestaw SDK zadań Webjob, po odebraniu komunikatu w kolejce, użyj **QueueTrigger** atrybutu. Konstruktor atrybutu ma parametr ciągu, który określa nazwę kolejki do sondowania. Aby zobaczyć, jak można ustawić nazwy kolejki dynamicznie, zapoznaj się z [jak ustawić opcje konfiguracji](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Ciąg wiadomości w kolejce
W poniższym przykładzie kolejka zawiera komunikat w formacie ciągu, więc **QueueTrigger** jest stosowany do parametru ciągu o nazwie **logmessage —** zawierający zawartość komunikatu w kolejce. Funkcja [zapisuje komunikat w dzienniku do pulpitu nawigacyjnego](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Oprócz **ciąg**, parametr może być tablicą bajtów **CloudQueueMessage** obiektu lub POCO, który zdefiniujesz.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykłe stare obiektu CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) kolejki komunikatów
W poniższym przykładzie komunikat w kolejce zawiera plik JSON do **BlobInformation** obiektu, który zawiera **BlobName** właściwości. Zestaw SDK automatycznie deserializuje obiekt.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Zestaw SDK używa [pakiet Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji komunikatów. Jeśli tworzysz komunikatów w kolejce w programie, który nie korzysta z zestawem SDK usługi WebJobs można napisać kod, jak w poniższym przykładzie do utworzenia komunikatu w kolejce POCO, zestaw SDK może przeanalizować.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funkcje asynchroniczne
Poniższa funkcja async [zapisuje dziennik do pulpitu nawigacyjnego](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Funkcje asynchroniczne może potrwać [token anulowania](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak pokazano w poniższym przykładzie, który kopiuje obiekt blob. (Objaśnienia dotyczące **queueTrigger** symbolu zastępczego, zobacz [obiektów blob](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) sekcji.)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy atrybutu QueueTrigger współpracuje z
Możesz użyć **QueueTrigger** z następujących typów:

* **ciąg**
* Typ POCO zserializowanym w formacie JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algorytm sondowania
Zestaw SDK implementuje losowe wykładniczego wycofywania algorytmu zmniejszają efekt bezczynności kolejki sondowania kosztów transakcji magazynu.  Gdy komunikat zostanie znaleziony, zestaw SDK czeka dwóch sekund, a następnie sprawdza, czy inny komunikat; gdy zostanie znaleziony żaden komunikat czeka około czterech sekund przed ponowną próbą. Po kolejnych nieudanych prób można pobrać komunikatu w kolejce czas oczekiwania stale rośnie, dopóki nie osiągnie maksymalny czas oczekiwania, która domyślnie na jedną minutę. [Maksymalny czas oczekiwania jest konfigurowane](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Wiele wystąpień
Jeśli aplikacja sieci web działa w wielu wystąpieniach, ciągłych zadań Webjob jest uruchamiane na każdej maszynie, a każda maszyna będzie poczekaj, aż wyzwalacze i próba uruchomienia funkcji. W niektórych scenariuszach, które może to prowadzić do niektórych funkcji przetwarzania te same dane dwa razy dlatego funkcje powinny być idempotentne, (napisane tak, aby je wielokrotnie wywołuje z tymi samymi danymi wejściowymi nie generuje wyniki duplikatów).  

## <a name="parallel-execution"></a>Wykonywanie równoległe
Jeśli masz wiele funkcji nasłuchuje na różnych kolejek, zestaw SDK wywoła ich równolegle po odebraniu wiadomości jednocześnie.

Dotyczy to także po odebraniu wiadomości wielu dla jednej kolejki. Domyślnie zestaw SDK pobiera partii 16 komunikatów w kolejce w danym momencie i wykonuje funkcję, która przetwarza je w sposób równoległy. [Rozmiar partii jest konfigurowalne](#how-to-set-configuration-options). Gdy liczba przetwarzanych pobiera w dół do połowy rozmiar partii, zestaw SDK pobiera inna partia i rozpoczyna przetwarzanie tych komunikatów. W związku z tym maksymalną liczbę równoczesnych komunikatów przetwarzanych dla każdej funkcji jest jedną połowę czasu i rozmiar partii. Ten limit dotyczy oddzielnie każdej funkcji, która ma **QueueTrigger** atrybutu. Jeśli nie chcesz, aby dla wiadomości otrzymanych w jednej kolejki przetwarzania równoległego, należy ustawić rozmiar partii 1.

## <a name="get-queue-or-queue-message-metadata"></a>Pobierz kolejki lub kolejki komunikatów metadanych
Następujące właściwości komunikatu można uzyskać, dodając parametry do sygnatury metody:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **ciąg** queueTrigger (zawiera tekst komunikatu)
* **ciąg** identyfikator
* **ciąg** elementu popReceipt
* **int** dequeueCount

Jeśli chcesz współpracować bezpośrednio z interfejsu API usługi Azure storage, możesz również dodać **CloudStorageAccount** parametru.

Poniższy przykład zapisuje wszystkie te metadane w dzienniku aplikacji informacje. W tym przykładzie logmessage — i queueTrigger zawierać zawartość komunikatu w kolejce.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Poniżej przedstawiono przykładowy dziennik napisane przez przykładowy kod:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Łagodne zamykanie
Funkcja, która działa w ciągłe zadanie WebJob może akceptować **CancellationToken** parametr, który umożliwia systemowi operacyjnemu powiadamianie funkcji, które ma zostać zakończone zadania WebJob. Skorzystaj z tego powiadomienia, aby upewnić się, że funkcja nie nieoczekiwanego zakończenia działania w sposób powodujący, że dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić, czy zbliżającego się zakończenie zadania WebJob w funkcji.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Uwaga:** Pulpit nawigacyjny nie może poprawnie wyświetlać stan i dane wyjściowe funkcji, które została zamknięta.

Aby uzyskać więcej informacji, zobacz [łagodne zamykanie usługi WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Jak utworzyć komunikatu w kolejce podczas przetwarzania komunikatu w kolejce
Aby napisać funkcję, która tworzy nowy komunikat w kolejce, użyj **kolejki** atrybutu. Podobnie jak **QueueTrigger**, przekazać nazwę kolejki w postaci ciągu, lub możesz [dynamiczne Ustawianie nazwy kolejki](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Ciąg wiadomości w kolejce
Poniższy przykładowy kod async nie tworzy nowego komunikatu w kolejce w kolejce o nazwie "outputqueue" przy użyciu tej samej zawartości jako odebrana w kolejce o nazwie "inputqueue" komunikatu w kolejce. (Funkcje asynchroniczne używają **IAsyncCollector<T>**  jak pokazano w dalszej części w tej sekcji.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykłe stare obiektu CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) kolejki komunikatów
Do utworzenia komunikatu w kolejce, zawierający POCO, a nie w ciągu, należy przekazać typ, POCO jako parametr wyjściowy do **kolejki** atrybut konstruktora.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Zestaw SDK automatycznie serializuje obiekt do formatu JSON. Komunikatu w kolejce zawsze jest tworzony, nawet jeśli obiekt ma wartość null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Utwórz wiele wiadomości lub funkcje asynchroniczne
Aby utworzyć wiele wiadomości, należy typu parametru dla kolejki wyjściowej **ICollector<T>**  lub **IAsyncCollector<T>**, jak pokazano w poniższym przykładzie.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Każdy komunikat w kolejce zostanie utworzony natychmiast, gdy **Dodaj** metoda jest wywoływana.

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, które atrybut kolejki współpracuje z
Możesz użyć **kolejki** atrybutu następujące typy parametrów:

* **limit ciągu** (tworzy komunikat z kolejki, jeśli wartość parametru jest inna niż null, gdy funkcja skończy działanie)
* **limit byte []** (działa jak **ciąg**)
* **limit CloudQueueMessage** (działa jak **ciąg**)
* **limit POCO** (typ możliwy do serializacji, tworzy komunikat z obiektem null Jeśli parametr ma wartość null, gdy funkcja skończy działanie)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (w przypadku tworzenia wiadomości, ręcznie bezpośrednio przy użyciu interfejsu API usługi Azure Storage)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Używanie atrybutów zestawu SDK usługi WebJobs w treści funkcji
Jeśli potrzeba wykonania dodatkowych czynności w funkcji przed przy użyciu atrybutu zestawu SDK usługi WebJobs, takich jak **kolejki**, **Blob**, lub **tabeli**, możesz użyć **IBinder**interfejsu.

Poniższy przykład pobiera komunikat kolejki danych wejściowych i tworzy nowy komunikat o tej samej zawartości w kolejce danych wyjściowych. Nazwa kolejki danych wyjściowych jest ustawiany przez kod w treści funkcji.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

**IBinder** interfejs może również służyć za pomocą **tabeli** i **Blob** atrybutów.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Jak odczytywanie i zapisywanie obiektów blob i tabel podczas przetwarzania komunikatu w kolejce
**Blob** i **tabeli** atrybutów umożliwiają odczytywanie i zapisywanie obiektów blob i tabel. Przykłady w tej sekcji mają zastosowanie do obiektów blob. Aby uzyskać przykłady kodu, które pokazują, jak wyzwolić procesów, podczas tworzenia lub aktualizowania obiektów blob, zobacz [jak używać usługi Azure blob storage z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Ciąg wiadomości w kolejce wyzwalania operacje obiektów blob
Dla komunikatu w kolejce, który zawiera ciąg **queueTrigger** jest symbolem zastępczym, można użyć w **Blob** atrybutu **blobPath** parametr, który znajduje się zawartość Komunikat.

W poniższym przykładzie użyto **Stream** obiektów na odczytywanie i zapisywanie obiektów blob. Komunikat w kolejce to nazwa obiektu blob znajduje się w kontenerze textblobs. Kopiowania obiektu blob za pomocą "-nowe" dołączany do nazwy jest tworzony w tym samym kontenerze.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

**Blob** atrybutu Konstruktor przyjmuje **blobPath** parametr określający nazwę kontenera i obiektów blob. Aby uzyskać więcej informacji na temat tego symbolu zastępczego, zobacz [jak używać usługi Azure blob storage z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Gdy atrybut zdobi **Stream** obiektu innego parametru Konstruktor Określa **FileAccess** trybie odczytu, zapisu lub odczytu i zapisu.

W poniższym przykładzie użyto **CloudBlockBlob** obiektu do usunięcia obiektu blob. Komunikat w kolejce to nazwa obiektu blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykłe stare obiektu CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) kolejki komunikatów
Dla POCO, zapisane w formacie JSON w komunikacie w kolejce, można użyć symboli zastępczych dostępnych nazwy właściwości obiektu w **kolejki** atrybutu **blobPath** parametru. Umożliwia także nazwy właściwości metadanych kolejki jako symbole zastępcze. Zobacz [kolejki lub kolejki komunikatów metadanych](#get-queue-or-queue-message-metadata).

Poniższy przykład kopiuje obiekt blob do nowego obiektu blob z innym rozszerzeniem. Komunikat w kolejce to **BlobInformation** obiektu, który zawiera **BlobName** i **BlobNameWithoutExtension** właściwości. Nazwy właściwości są używane jako symbole zastępcze w ścieżce obiektu blob dla **Blob** atrybutów.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Zestaw SDK używa [pakiet Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji komunikatów. Jeśli tworzysz komunikatów w kolejce w programie, który nie korzysta z zestawem SDK usługi WebJobs można napisać kod, jak w poniższym przykładzie do utworzenia komunikatu w kolejce POCO, zestaw SDK może przeanalizować.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Jeśli zachodzi potrzeba wykonania dodatkowych czynności w funkcji przed powiązania obiektu blob do obiektu, możesz użyć atrybutu w treści funkcji, jak pokazano na [Użyj zestawu SDK usługi WebJobs atrybutów w treści funkcji](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, których można użyć atrybutu obiektu Blob przy użyciu
**Blob** atrybut może być używany z następujących typów:

* **Stream** (Odczyt lub zapis, określone za pomocą FileAccess parametr konstruktora)
* **TextReader**
* **TextWriter**
* **ciąg** (odczyt)
* **limit ciągu** (zapis; utworzy obiekt blob, tylko wtedy, gdy parametr ciągu jest różna od null, gdy funkcja zwraca)
* POCO (odczyt)
* limit POCO (zapis; zawsze tworzy obiekt blob, tworzy jako obiekt o wartości null, jeśli parametr POCO ma wartość null w przypadku, gdy funkcja zwraca)
* **CloudBlobStream** (zapis)
* **ICloudBlob** (Odczyt lub zapis)
* **CloudBlockBlob** (Odczyt lub zapis)
* **CloudPageBlob** (Odczyt lub zapis)

## <a name="how-to-handle-poison-messages"></a>Sposób obsługi wiadomości
Komunikaty, w których zawartość powoduje, że funkcja nie powiedzie się, są nazywane *skażonymi komunikatami*. Jeśli funkcja zawiedzie, komunikat w kolejce nie zostanie usunięta i ostatecznie zostaje pobrana ponownie, powodując cykl jest powtarzany. Zestaw SDK automatycznie można przerwać cykl po ograniczoną liczbę iteracji, lub możesz to zrobić ręcznie.

### <a name="automatic-poison-message-handling"></a>Obsługa automatycznego, zarządzanie skażonymi komunikatami
Zestaw SDK wywoła funkcję maksymalnie 5 razy do przetwarzania komunikatu w kolejce. W przypadku awarii spróbuj piąty wiadomość zostanie przeniesiona do kolejki skażone. Możesz dowiedzieć się, jak skonfigurować maksymalną liczbę ponownych prób w [jak ustawić opcje konfiguracji](#how-to-set-configuration-options).

Nosi nazwę kolejki skażone *{originalqueuename}*-skażone. Można napisać, że funkcja przetwarzania komunikatów z kolejki zanieczyszczone przez rejestrowania ich lub wysyłania powiadomienia, że ręczne uwagi jest wymagana.

W poniższym przykładzie **CopyBlob** funkcja zakończy się niepowodzeniem, gdy komunikatu w kolejce zawiera nazwę obiektu blob, który nie istnieje. Jeśli tak się stanie, wiadomość zostanie przeniesiona do kolejki copyblobqueue poison z kolejki copyblobqueue. **ProcessPoisonMessage** następnie logują się zarządzanie skażonymi komunikatami.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Poniższa ilustracja przedstawia dane wyjściowe konsoli z tych funkcji, po przetworzeniu Zarządzanie skażonymi komunikatami.

![Dane wyjściowe konsoli Zarządzanie skażonymi komunikatami obsługi](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Obsługa ręczne zarządzanie skażonymi komunikatami
Ile razy zostały odebrane wiadomości do przetwarzania można uzyskać, dodając **int** parametr o nazwie **dequeueCount** do funkcji. Można sprawdzić liczbę usuwania z kolejki w kodzie funkcji i wykonać swoje własne Zarządzanie skażonymi komunikatami obsługi gdy ich liczba przekracza próg, jak pokazano w poniższym przykładzie.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Jak ustawić opcje konfiguracji
Możesz użyć **JobHostConfiguration** typu można ustawić następujące opcje konfiguracji:

* Ustaw parametry połączenia SDK w kodzie.
* Konfigurowanie **QueueTrigger** ustawienia, takie jak maksymalna liczba usuwanych z kolejki.
* Uzyskaj nazwy kolejek z konfiguracji.

### <a name="set-sdk-connection-strings-in-code"></a>Ustawianie parametrów połączenia SDK w kodzie
Ustawianie parametrów połączenia SDK w kodzie można użyć własnych nazw parametrów połączenia w plikach konfiguracji lub zmiennych środowiskowych, jak pokazano w poniższym przykładzie.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Konfigurowanie ustawień QueueTrigger
Można skonfigurować następujące ustawienia, które są stosowane do przetwarzania komunikatów w kolejce:

* Maksymalna liczba komunikatów w kolejce, które są pobierane jednocześnie wykonywanej równolegle (wartość domyślna to 16).
* Maksymalna liczba ponownych prób przed wysłaniem komunikatu w kolejce do skażone kolejki (wartość domyślna to 5).
* Maksymalny czas oczekiwania przed sondowaniem ponownie, gdy kolejka jest pusta (wartość domyślna to 1 minuta).

Poniższy przykład pokazuje, jak skonfigurować te ustawienia:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ustawianie wartości dla zestawu SDK usługi WebJobs parametry konstruktora w kodzie
Czasami trzeba określić nazwę kolejki, nazwa obiektu blob lub kontenera lub tabeli w kodzie zamiast trwale kodować nadaj mu nazwę. Na przykład możesz chcieć określić nazwę kolejki **QueueTrigger** w zmiennej środowisku lub pliku konfiguracji.

Możesz to zrobić, przekazując **NameResolver** obiekt **JobHostConfiguration** typu. Zawierają specjalne elementy zastępcze otoczony procentu (%) loguje się parametry konstruktora atrybutu zestaw SDK zadań Webjob i **NameResolver** kod określa rzeczywiste wartości, które będą używane zamiast te symbole zastępcze.

Na przykład załóżmy, że chcesz użyć kolejkę o nazwie logqueuetest w środowisku testowym i jedną o nazwie logqueueprod w środowisku produkcyjnym. Zamiast nazwy zakodowane kolejki, aby określić nazwę wpisu w **appSettings** kolekcji, która będzie mieć nazwę kolejki rzeczywistych. Jeśli **appSettings** logqueue jest klucz, funkcja może wyglądać jak w poniższym przykładzie.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Twoje **NameResolver** klasy można następnie uzyskać nazwę kolejki z **appSettings** jak pokazano w poniższym przykładzie:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Możesz przekazać **NameResolver** klasy w celu **JobHost** obiektu, jak pokazano w poniższym przykładzie.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Uwaga:** Nazwy obiektów blob, tabel i kolejki są rozwiązywane w każdym wywołaniu funkcji, ale nazwy kontenera obiektów blob są rozpoznawane tylko podczas uruchamiania aplikacji. Nie można zmienić nazwy kontenera obiektów blob, gdy zadanie jest uruchomione.

## <a name="how-to-trigger-a-function-manually"></a>Ręczne wyzwalanie funkcji
Aby ręcznie wyzwolić funkcję, należy użyć **wywołania** lub **CallAsync** metody **JobHost** obiektu i **NoAutomaticTrigger** atrybut dla funkcji, jak pokazano w poniższym przykładzie.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Jak napisać dzienników
Na pulpicie nawigacyjnym prezentowana dzienniki w dwóch miejscach: na stronie dla zadania WebJob i na stronie dla poszczególnych wywołań zadania WebJob.

![Dzienniki na stronie zadań WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Loguje się Strona wywołania funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Dane wyjściowe z konsoli metody wywołania funkcji lub w **Main()** metoda pojawia się na stronie pulpitu nawigacyjnego dla zadania WebJob, a nie w stronie wywołania określonej metody. Dane wyjściowe obiektu TextWriter, który można pobrać z parametrem w podpisie metody zostanie wyświetlony na stronie pulpitu nawigacyjnego dla wywołania metody.

Nie można połączyć dane wyjściowe konsoli z wywołania określonej metody, ponieważ konsola jest jednowątkowym, gdy wiele funkcji zadania mogą być uruchomione w tym samym czasie. Dlatego zestaw SDK udostępnia każdego wywołania funkcji wraz z obiektem dziennika Unikatowy składnik zapisywania.

Aby zapisać [dzienniki śledzenia aplikacji](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), użyj **Console.Out** (tworzy Dzienniki oznaczone jako INFO) i **Console.Error** (tworzy Dzienniki oznaczone jako błąd). Alternatywą jest użycie [ślad lub TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), który zawiera pełne informacje, ostrzeżenie, i krytyczne poziomy oprócz informacje i błąd. Dzienniki śledzenia aplikacji są wyświetlane w plikach dziennika aplikacji sieci web, tabele platformy Azure lub obiektów blob platformy Azure, w zależności od tego, jak skonfigurować aplikację internetową platformy Azure. Podobnie jak wszystkie dane wyjściowe konsoli, najnowsze Dzienniki aplikacji 100 również zostać wyświetlony na stronie pulpitu nawigacyjnego dla zadania WebJob nie strony dla wywołania funkcji.

Dane wyjściowe konsoli zostanie wyświetlony pulpit nawigacyjny tylko wtedy, gdy program jest uruchomiony w zadaniu Azure WebJob, nie wtedy, gdy program działa lokalnie lub innego środowiska.

Aby wyłączyć rejestrowanie, należy ustawiania ciągu połączenia z pulpitu nawigacyjnego na wartość null. Aby uzyskać więcej informacji, zobacz [jak ustawić opcje konfiguracji](#how-to-set-configuration-options).

W poniższym przykładzie pokazano kilka sposobów na zapisywanie dzienników:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

Na pulpicie nawigacyjnym zestawu SDK usługi WebJobs, dane wyjściowe z **TextWriter** pokazuje się po przejściu do strony dla określonego wywołania funkcji i wybierz obiektów **Przełącz dane wyjściowe**:

![Link wywołania](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Loguje się Strona wywołania funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na pulpicie nawigacyjnym zestawu SDK usługi WebJobs najbardziej aktualne 100 wierszy z konsoli dane wyjściowe show się przejść na stronę zadania webjob (a nie dla wywołania funkcji) i wybierz **Przełącz dane wyjściowe**.

![Przełącz dane wyjściowe](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Ciągłe zadanie WebJob Dzienniki aplikacji widoczne w/data/zadania/ciągłe/*{webjobname}*/job_log.txt w systemie plików aplikacji sieci web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Na platformie Azure blob wygląd Dzienniki aplikacji następująco: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write — Witaj świecie!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error — Witaj świecie!, 2014-09-26T21 : 01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out — Witaj świecie!,

W tabeli platformy Azure **Console.Out** i **Console.Error** dzienniki wyglądać następująco:

![Informacje dziennika w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Dziennik błędów w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule udostępnił przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze dotyczące pracy z kolejek systemu Azure. Aby uzyskać więcej informacji o sposobie używania usługi Azure WebJobs i zestaw SDK zadań Webjob, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

