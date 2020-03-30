---
title: Wprowadzenie do magazynu kolejek przy użyciu programu Visual Studio (projekty webjob)
description: Jak rozpocząć korzystanie z magazynu kolejki platformy Azure w projekcie WebJob po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298748"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Wprowadzenie do usługi Azure Queue storage i usług połączonych z programem Visual Studio (projekty webjob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak rozpocząć korzystanie z magazynu kolejki platformy Azure w projekcie usługi Visual Studio Azure WebJob po utworzeniu konta magazynu platformy Azure lub do którego do niego nawiązano, korzystając z okna dialogowego **Dodaj połączone usługi** programu Visual Studio. Po dodaniu konta magazynu do projektu WebJob przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio są instalowane odpowiednie pakiety Azure Storage NuGet, odpowiednie odwołania .NET są dodawane do projektu, a parametry połączenia dla konta magazynu są aktualizowane w pliku App.config.  

Ten artykuł zawiera przykłady kodu języka C#, które pokazują, jak używać narzędzia Azure WebJobs SDK w wersji 1.x z usługą Azure Queue storage.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Aby uzyskać więcej [informacji, zobacz Wprowadzenie do usługi Azure Queue Storage przy użyciu platformy .NET.](../storage/queues/storage-dotnet-how-to-use-queues.md) Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak wyzwolić funkcję po odebraniu wiadomości kolejki
Aby napisać funkcję, którą sdk WebJobs wywołuje po odebraniu wiadomości kolejki, użyj atrybutu **QueueTrigger.** Konstruktor atrybutów przyjmuje parametr string, który określa nazwę kolejki do sondowania. Aby zobaczyć, jak dynamicznie ustawić nazwę kolejki, zapoznaj się z [instrukcjami ustawiania opcji konfiguracji](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Komunikaty kolejki ciągów
W poniższym przykładzie kolejka zawiera komunikat ciągu, więc **QueueTrigger** jest stosowany do parametru ciągu o nazwie **logMessage,** który zawiera zawartość wiadomości kolejki. Funkcja [zapisuje komunikat dziennika na pulpicie nawigacyjnym](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Oprócz **ciągu**parametr może być tablicą bajtów, obiektem **CloudQueueMessage** lub poco, który definiujesz.

### <a name="poco-plain-old-clr-object-queue-messages"></a>Komunikaty kolejki POCO [(Zwykły stary obiekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
W poniższym przykładzie komunikat kolejki zawiera JSON dla **obiektu Informacji o obiektach blobinformation,** który zawiera właściwość **BlobName.** SDK automatycznie deserializes obiektu.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Zestawu SDK używa [pakietu Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji wiadomości. Jeśli tworzysz wiadomości kolejki w programie, który nie używa webjobs SDK, można napisać kod, jak w poniższym przykładzie, aby utworzyć komunikat kolejki POCO, który można przeanalizować.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funkcje asynchronizowe
Następująca funkcja asynchronii [zapisuje dziennik na pulpicie nawigacyjnym](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Funkcje asynchronizowe mogą przyjmować [token anulowania,](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)jak pokazano w poniższym przykładzie, który kopiuje obiekt blob. (Aby uzyskać wyjaśnienie symbolu zastępczego **queueTrigger,** zobacz sekcję [Obiektów Blobs).](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy atrybutu QueueTrigger, z które współpracuje
Funkcji **QueueTrigger** można używać z następującymi typami:

* **Ciąg**
* Typ POCO serializowany jako JSON
* **bajt[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algorytm sondowania
Zestaw SDK implementuje algorytm losowego wykładniczego wycofywania, aby zmniejszyć wpływ sondowania w kolejkach bezczynności na koszty transakcji magazynu.  Po znalezieniu wiadomości, SDK czeka dwie sekundy, a następnie sprawdza, czy inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka około czterech sekund przed ponowną próbą. Po kolejnych nieudanych próbach uzyskania wiadomości kolejki czas oczekiwania nadal wzrasta, aż osiągnie maksymalny czas oczekiwania, który jest domyślnie wyświetlany jedną minutę. [Maksymalny czas oczekiwania jest konfigurowalny](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Wiele wystąpień
Jeśli aplikacja sieci web działa w wielu wystąpieniach, ciągłe WebJobs działa na każdym komputerze, a każdy komputer będzie czekać na wyzwalacze i próby uruchomienia funkcji. W niektórych scenariuszach może to prowadzić do niektórych funkcji przetwarzania tych samych danych dwa razy, więc funkcje powinny być idempotentne (napisane tak, że wywoływanie ich wielokrotnie z tych samych danych wejściowych nie daje zduplikowanych wyników).  

## <a name="parallel-execution"></a>Równoległego
Jeśli masz wiele funkcji nasłuchiwania w różnych kolejkach, zestaw SDK wywoła je równolegle, gdy wiadomości są odbierane jednocześnie.

To samo dotyczy wielu wiadomości są odbierane dla jednej kolejki. Domyślnie SDK pobiera partii 16 komunikatów kolejki naraz i wykonuje funkcję, która przetwarza je równolegle. [Rozmiar partii można konfigurować](#how-to-set-configuration-options). Gdy liczba przetwarzana pobiera w dół do połowy rozmiaru partii, SDK pobiera inną partię i rozpoczyna przetwarzanie tych komunikatów. W związku z tym maksymalna liczba równoczesnych komunikatów przetwarzanych na funkcję jest półtora razy rozmiar partii. Ten limit ma zastosowanie oddzielnie do każdej funkcji, która ma atrybut **QueueTrigger.** Jeśli nie chcesz równoległego wykonywania wiadomości odebranych w jednej kolejce, ustaw rozmiar partii na 1.

## <a name="get-queue-or-queue-message-metadata"></a>Pobierz metadane wiadomości kolejki lub kolejki
Można uzyskać następujące właściwości komunikatu, dodając parametry do podpisu metody:

* **DataCzas** wygaśnięcia zestawu dat
* **Wstawianie datetimeoffsettime**
* **DateTimeOffset** następnyVisibleTime
* **string** queueTrigger (zawiera tekst wiadomości)
* identyfikator **ciągu**
* **pop** stringReceipt
* int dequeueCount **(int** dequeueCount)

Jeśli chcesz pracować bezpośrednio z interfejsem API magazynu platformy Azure, możesz również dodać parametr **CloudStorageAccount.**

Poniższy przykład zapisuje wszystkie te metadane w dzienniku aplikacji INFO. W tym przykładzie zarówno logMessage i queueTrigger zawierają zawartość komunikatu kolejki.

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

Oto przykładowy dziennik napisany przez przykładowy kod:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Wdzięczne zamknięcie
Funkcja uruchamiana w ciągłym trybie WebJob może zaakceptować parametr **CancellationToken,** który umożliwia systemowi operacyjnemu powiadamianie funkcji, gdy praca w sieci Web ma zostać zakończona. Tego powiadomienia można użyć, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który pozostawia dane w niespójnym stanie.

W poniższym przykładzie pokazano, jak sprawdzić zbliżające się zakończenie webjob w funkcji.

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

**Uwaga:** Pulpit nawigacyjny może nie poprawnie wyświetlić stan i dane wyjściowe funkcji, które zostały zamknięte.

Aby uzyskać więcej informacji, zobacz [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Jak utworzyć wiadomość kolejki podczas przetwarzania wiadomości kolejki
Aby napisać funkcję, która tworzy nową wiadomość kolejki, użyj atrybutu **Kolejka.** Podobnie jak **funkcja QueueTrigger**, przekazujesz w nazwie kolejki jako ciąg lub można [dynamicznie ustawić nazwę kolejki.](#how-to-set-configuration-options)

### <a name="string-queue-messages"></a>Komunikaty kolejki ciągów
Poniższy przykład kodu nieaskronikowego tworzy nowy komunikat kolejki w kolejce o nazwie "outputqueue" z taką samą zawartością, jak komunikat kolejki odebrany w kolejce o nazwie "inputqueue". (Dla funkcji asynchronicznych należy użyć **\<IAsyncCollector T>** jak pokazano w dalszej części tej sekcji.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Komunikaty kolejki POCO [(Zwykły stary obiekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Aby utworzyć komunikat kolejki, który zawiera POCO, a nie ciąg, przekazać typ POCO jako parametr wyjściowy do konstruktora **atrybutu queue.**

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

SDK automatycznie serializuje obiekt do JSON. Komunikat kolejki jest zawsze tworzony, nawet jeśli obiekt ma wartość null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Tworzenie wielu wiadomości lub funkcji asynchronizycznych
Aby utworzyć wiele komunikatów, należy utworzyć typ parametru dla kolejki wyjściowej **ICollector\<T>** lub **IAsyncCollector\<T>**, jak pokazano w poniższym przykładzie.

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

Każdy komunikat kolejki jest tworzony natychmiast **po** add metoda jest wywoływana.

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, z którymi działa atrybut Kolejka
Atrybut **Queue** można użyć w następujących typach parametrów:

* **out string** (tworzy komunikat kolejki, jeśli wartość parametru jest niena null po zakończeniu funkcji)
* **out byte[]** (działa jak **ciąg)**
* **się CloudQueueMessage** (działa jak **ciąg)**
* **out POCO** (typ serializowalny, tworzy komunikat z obiektem null, jeśli parametr ma wartość null po zakończeniu funkcji)
* **ICollector**
* **IAsyncKollector**
* **CloudQueue** (do ręcznego tworzenia wiadomości przy użyciu interfejsu API usługi Azure Storage bezpośrednio)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Używanie atrybutów SDK webjobs w treści funkcji
Jeśli przed użyciem atrybutu SDK WebJobs, takiego jak **Kolejka,** **Obiekt Blob**lub **Tabela,** należy wykonać pewną pracę w ramach funkcji, można użyć interfejsu **IBinder.**

W poniższym przykładzie przyjmuje komunikat kolejki wejściowej i tworzy nową wiadomość z tej samej zawartości w kolejce wyjściowej. Nazwa kolejki wyjściowej jest ustawiana przez kod w treści funkcji.

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

Interfejs **IBinder** może być również używany z atrybutami **Tabela** i **Obiekt blob.**

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Jak odczytywać i zapisywać obiekty BLOB i tabele podczas przetwarzania wiadomości kolejki
Atrybuty **obiektów blob** i **table** umożliwiają odczytywanie i zapisywanie obiektów blob i tabel. Przykłady w tej sekcji dotyczą obiektów blob. Aby uzyskać przykłady kodu, które pokazują, jak wyzwalać procesy podczas tworzenia lub aktualizowanie obiektów BLOB, zobacz [Jak używać magazynu obiektów blob platformy Azure z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Komunikaty kolejki ciągów wyzwalające operacje obiektów blob
Dla komunikatu kolejki, który zawiera ciąg, **queueTrigger** jest symbolem zastępczym, którego można użyć w parametrze blobPath atrybutu **blobPath,** który zawiera zawartość wiadomości. **Blob**

W poniższym przykładzie użyto **Stream** obiektów do odczytu i zapisu obiektów blob. Komunikat kolejki to nazwa obiektu blob znajdującego się w kontenerze textblobs. Kopia obiektu blob z "-new" dołączone do nazwy jest tworzony w tym samym kontenerze.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Konstruktor **atrybutów obiektów Blob** przyjmuje parametr **blobPath,** który określa nazwę kontenera i obiektu blob. Aby uzyskać więcej informacji na temat tego symbolu [zastępczego, zobacz Jak używać magazynu obiektów blob platformy Azure z zestawem SDK webjobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Gdy atrybut ozdabia **Stream** obiektu, inny parametr konstruktora określa **tryb FileAccess** jako odczyt, zapis lub odczyt/zapis.

W poniższym przykładzie użyto **CloudBlockBlob** obiektu do usunięcia obiektu blob. Komunikat kolejki jest nazwą obiektu blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Komunikaty kolejki POCO [(Zwykły stary obiekt CLR)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
W przypadku poco przechowywane jako JSON w komunikacie kolejki, można użyć symboli zastępczych, które nazwy właściwości obiektu w parametrze **blobPath** **atrybutu queue.** Można również użyć nazw właściwości metadanych kolejki jako symboli zastępczych. Zobacz: [Pobierz metadane wiadomości kolejki lub kolejki](#get-queue-or-queue-message-metadata).

Poniższy przykład kopiuje obiekt blob do nowego obiektu blob z innym rozszerzeniem. Komunikat kolejki jest obiektem **informacji o obiektach blobInformation,** który zawiera właściwości **BlobName** i **BlobNameWithoutExtension.** Nazwy właściwości są używane jako symbole zastępcze w ścieżce obiektu blob dla atrybutów **obiektu Blob.**

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Zestawu SDK używa [pakietu Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji wiadomości. Jeśli tworzysz wiadomości kolejki w programie, który nie używa webjobs SDK, można napisać kod, jak w poniższym przykładzie, aby utworzyć komunikat kolejki POCO, który można przeanalizować.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Jeśli musisz wykonać jakąś pracę w ramach funkcji przed powiększeniem obiektu blob z obiektem, można użyć atrybutu w treści funkcji, jak pokazano w [użyj atrybutów SDK WebJobs w treści funkcji](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, których można używać atrybutu Blob z
Atrybut **obiektu Blob** może być używany z następującymi typami:

* **Strumień** (odczyt lub zapis, określony przy użyciu parametru konstruktora FileAccess)
* **Textreader**
* **TextWriter**
* **ciąg (odczyt)**
* **out string** (zapis; tworzy obiekt blob tylko wtedy, gdy parametr ciągu jest nie-null, gdy funkcja zwraca)
* POCO (czytaj)
* out POCO (zapis; zawsze tworzy obiekt blob, tworzy jako obiekt null, jeśli parametr POCO ma wartość null, gdy funkcja zwraca)
* **CloudBlobStream** (zapis)
* **ICloudBlob** (odczyt lub zapis)
* **CloudBlockBlob** (odczyt lub zapis)
* **CloudPageBlob** (odczyt lub zapis)

## <a name="how-to-handle-poison-messages"></a>Jak radzić sobie z trującymi wiadomościami
Wiadomości, których zawartość powoduje niepowodzenie funkcji są nazywane *trującymi wiadomościami*. Gdy funkcja nie powiedzie się, komunikat kolejki nie jest usuwany i ostatecznie jest pobierany ponownie, co powoduje powtórzenie cyklu. SDK może automatycznie przerwać cykl po ograniczonej liczbie iteracji lub można to zrobić ręcznie.

### <a name="automatic-poison-message-handling"></a>Automatyczna obsługa wiadomości o trucinie
SDK wywoła funkcję do 5 razy, aby przetworzyć komunikat kolejki. Jeśli piąta próba nie powiedzie się, wiadomość zostanie przeniesiona do kolejki trujących. Możesz zobaczyć, jak skonfigurować maksymalną liczbę ponownych prób w [jak ustawić opcje konfiguracji](#how-to-set-configuration-options).

Kolejka trucizn nosi nazwę *{originalqueuename}*-poison. Można napisać funkcję przetwarzania wiadomości z kolejki trucizny, rejestrując je lub wysyłając powiadomienie, że wymagana jest ręczna uwaga.

W poniższym przykładzie **CopyBlob** funkcja zakończy się niepowodzeniem, gdy komunikat kolejki zawiera nazwę obiektu blob, który nie istnieje. W takim przypadku wiadomość jest przenoszona z kolejki copyblobqueue do kolejki copyblobqueue-poison. **ProcessPoisonMessage** następnie rejestruje trujący komunikat.

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

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli z tych funkcji podczas przetwarzania trującego komunikatu.

![Wyjście konsoli do obsługi trujących komunikatów](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Ręczna obsługa wiadomości o trucinie
Można uzyskać liczbę razy wiadomość została pobrana do przetwarzania przez dodanie **int** parametr o nazwie **dequeueCount** do funkcji. Następnie można sprawdzić liczbę dequeue w kodzie funkcji i wykonać własną obsługę trujących wiadomości, gdy liczba przekracza próg, jak pokazano w poniższym przykładzie.

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
Za pomocą typu **JobHostConfiguration** można ustawić następujące opcje konfiguracji:

* Ustaw parametry połączenia zestawu SDK w kodzie.
* Skonfiguruj ustawienia **funkcji QueueTrigger,** takie jak maksymalna liczba dekań.
* Pobierz nazwy kolejek z konfiguracji.

### <a name="set-sdk-connection-strings-in-code"></a>Ustawianie ciągów połączeń zestawu SDK w kodzie
Ustawienie ciągów połączeń SDK w kodzie umożliwia używanie własnych nazw ciągów połączeń w plikach konfiguracyjnych lub zmiennych środowiskowych, jak pokazano w poniższym przykładzie.

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

### <a name="configure-queuetrigger--settings"></a>Konfigurowanie ustawień programu QueueTrigger
Można skonfigurować następujące ustawienia, które mają zastosowanie do przetwarzania wiadomości kolejki:

* Maksymalna liczba komunikatów kolejki, które są pobierane jednocześnie do wykonania równolegle (domyślnie jest 16).
* Maksymalna liczba ponownych prób przed wysłaniem wiadomości kolejki do kolejki zacięcia (domyślnie jest to 5).
* Maksymalny czas oczekiwania przed ponownym sondowaniem, gdy kolejka jest pusta (domyślnie jest to 1 minuta).

W poniższym przykładzie pokazano, jak skonfigurować te ustawienia:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ustawianie wartości parametrów konstruktora zestawu SDK webjobs w kodzie
Czasami chcesz określić nazwę kolejki, nazwę obiektu blob lub kontenera lub nazwę tabeli w kodzie, a nie kod twardy. Na przykład można określić nazwę kolejki dla **queueTrigger** w pliku konfiguracji lub zmiennej środowiskowej.

Można to zrobić, przekazując w **NameResolver** obiektu do **JobHostConfiguration** typu. Dołączasz specjalne symbole zastępcze otoczone procentem (%) znaki w parametrach konstruktora atrybutów SDK WebJobs, a kod **NameResolver** określa rzeczywiste wartości, które mają być używane zamiast tych symboli zastępczych.

Załóżmy na przykład, że chcesz użyć kolejki o nazwie logqueuetest w środowisku testowym i jeden o nazwie logqueueprod w środowisku produkcyjnym. Zamiast nazwy kolejki zakodowane, chcesz określić nazwę wpisu w **appSettings** kolekcji, która będzie miała rzeczywistą nazwę kolejki. Jeśli **appSettings** klucz jest logqueue, funkcja może wyglądać jak w poniższym przykładzie.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Twoja **klasa NameResolver** może następnie uzyskać nazwę kolejki z **appSettings,** jak pokazano w poniższym przykładzie:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Klasa **NameResolver** należy przekazać do obiektu **JobHost,** jak pokazano w poniższym przykładzie.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Uwaga:** Nazwy kolejek, tabel i obiektów blob są rozpoznawane za każdym razem, gdy wywoływana jest funkcja, ale nazwy kontenerów obiektów blob są rozpoznawane tylko po uruchomieniu aplikacji. Nie można zmienić nazwy kontenera obiektów blob, gdy zadanie jest uruchomione.

## <a name="how-to-trigger-a-function-manually"></a>Jak ręcznie wyzwolić funkcję
Aby wyzwolić funkcję ręcznie, należy użyć **Call** lub **CallAsync** metody na **JobHost** obiektu i **NoAutomaticTrigger** atrybut na funkcji, jak pokazano w poniższym przykładzie.

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

## <a name="how-to-write-logs"></a>Jak pisać dzienniki
Pulpit nawigacyjny pokazuje dzienniki w dwóch miejscach: stronę webjob i stronę dla określonego wywołania webjob.

![Loguje się na stronie WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Strona wywołania funkcji loguje się](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Dane wyjściowe z metody konsoli, które można wywołać w funkcji lub w **Main()** metoda pojawia się na pulpicie nawigacyjnym strony dla WebJob, a nie na stronie dla wywołania określonej metody. Dane wyjściowe z TextWriter obiektu, który można uzyskać z parametru w podpisie metody pojawia się na stronie pulpitu nawigacyjnego dla wywołania metody.

Dane wyjściowe konsoli nie mogą być połączone z wywołaniem określonej metody, ponieważ konsola jest jednowątkowa, podczas gdy wiele funkcji zadań może być uruchomionych w tym samym czasie. Dlatego SDK zapewnia każde wywołanie funkcji z własnym unikatowym obiektem modułu zapisującego dzienniki.

Aby napisać [dzienniki śledzenia aplikacji,](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)użyj **Console.Out** (tworzy dzienniki oznaczone jako INFO) i **Console.Error** (tworzy dzienniki oznaczone jako ERROR). Alternatywą jest użycie [śledzenia lub tracesource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), który zapewnia pełne, ostrzeżenie i krytyczne poziomy oprócz informacji i błędów. Dzienniki śledzenia aplikacji są wyświetlane w plikach dziennika aplikacji sieci Web, tabelach platformy Azure lub obiektach blob platformy Azure w zależności od sposobu konfigurowania aplikacji sieci Web platformy Azure. Podobnie jak w przypadku wszystkich danych wyjściowych konsoli, najnowsze dzienniki aplikacji 100 są również wyświetlane na stronie pulpitu nawigacyjnego dla webjob, a nie na stronie wywołania funkcji.

Dane wyjściowe konsoli są wyświetlane na pulpicie nawigacyjnym tylko wtedy, gdy program jest uruchomiony w programie Azure WebJob, a nie wtedy, gdy program jest uruchomiony lokalnie lub w innym środowisku.

Rejestrowanie można wyłączyć, ustawiając ciąg połączenia pulpitu nawigacyjnego na wartość null. Aby uzyskać więcej informacji, zobacz [Jak ustawić opcje konfiguracji](#how-to-set-configuration-options).

W poniższym przykładzie przedstawiono kilka sposobów pisania dzienników:

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

Na pulpicie nawigacyjnym SDK webjobs dane wyjściowe z obiektu **TextWriter** są wyświetlane po naciśnięciu strony dla określonego wywołania funkcji i wybraniu **opcji Przełącz dane wyjściowe:**

![Łącze wywołania](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Strona wywołania funkcji loguje się](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na pulpicie nawigacyjnym SDK webjobs najnowsze 100 wierszy danych wyjściowych konsoli pojawia się po przełączeniu do strony dla webjob (nie dla wywołania funkcji) i wybierz pozycję **Przełącz dane wyjściowe**.

![Przełączanie danych wyjściowych](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

W ciągłym webjobecharze pojawiają się w /data/jobs/continuous/*{webjobname}*/job_log.txt w systemie plików aplikacji sieci web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

W obiekcie blob platformy Azure dzienniki aplikacji wyglądają następująco: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Błąd,contosoadsnew,491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Informacje,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

W tabeli platformy Azure **dzienniki Console.Out** i **Console.Error** wyglądają następująco:

![Tabela logowania informacji](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Logowanie błędów w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z kolejkami platformy Azure. Aby uzyskać więcej informacji na temat korzystania z usługi Azure WebJobs i webjobs SDK, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

