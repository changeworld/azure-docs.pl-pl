---
title: Rozpoczynanie pracy z magazynem kolejek i Visual Studio połączone usługi (projekty zadania WebJob) | Dokumentacja firmy Microsoft
description: Jak rozpocząć korzystanie z magazynu kolejek Azure projektu zadania WebJob, po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi.
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 52c089d2657d61ee42730a00def060dec74439fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Rozpoczynanie pracy z magazynem kolejek Azure i programu Visual Studio połączone usługi (zadania WebJob projekty)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Przegląd
W tym artykule opisano sposób rozpoczęcie pracy z magazynem kolejek Azure w projekcie zadania programu Visual Studio Azure WebJob po utworzony lub odwołanie do konta magazynu platformy Azure przy użyciu programu Visual Studio **dodać usług połączonych** okno dialogowe. Podczas dodawania konta magazynu do projektu zadania WebJob za pomocą programu Visual Studio **dodać usług połączonych** okna dialogowego, są zainstalowane odpowiednie pakiety NuGet magazynu Azure, odpowiednie odwołania .NET są dodawane do projektu i parametry połączenia dla konta magazynu są aktualizowane w pliku App.config.  

Ten artykuł zawiera C# przykłady kodu, których pokazano, jak korzystać z wersji zestawu Azure WebJobs SDK 1.x z usługą magazyn kolejek platformy Azure.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Zobacz [Rozpoczynanie pracy z magazynem kolejek Azure przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) Aby uzyskać więcej informacji. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Sposób włączania funkcji po odebraniu komunikatu w kolejce
Aby napisać funkcję, która wywołuje zestaw SDK zadań Webjob po odebraniu komunikatu w kolejce, użyj **QueueTrigger** atrybutu. Konstruktor atrybutu ma parametr typu string, który określa nazwę kolejki do sondowania. Aby sprawdzić, jak można ustawić nazwy kolejki dynamicznie, wyewidencjonuj [sposobu ustawiania opcji konfiguracji](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Ciąg wiadomości w kolejce
W poniższym przykładzie kolejki zawiera komunikat, więc **QueueTrigger** jest stosowany do parametru ciągu o nazwie **logMessage** zawierający zawartość komunikatu w kolejce. Funkcja [zapisuje komunikat w dzienniku do pulpitu nawigacyjnego](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Oprócz **ciąg**, parametr może być tablicą bajtów **CloudQueueMessage** obiektu lub POCO, które należy zdefiniować.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykły stary obiekt CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) wiadomości w kolejce
W poniższym przykładzie komunikat z kolejki zawiera JSON dla **BlobInformation** obiektu, który zawiera **element BlobName** właściwości. Zestaw SDK automatycznie deserializuje obiekt.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Zestaw SDK używa [pakietu Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) do serializowania i deserializowania wiadomości. Jeśli tworzysz wiadomości w kolejce w programie, który nie korzysta z zestawu SDK zadań Webjob, można napisać kod, jak w następującym przykładzie, aby utworzyć POCO komunikatu w kolejce, które mogą przeanalizować zestawu SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Funkcje asynchroniczne
Następująca funkcja async [zapisuje dziennik w pulpicie nawigacyjnym](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Funkcje asynchroniczne może potrwać [token anulowania](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak pokazano w poniższym przykładzie, który kopiuje obiektu blob. (Aby uzyskać informacje o **queueTrigger** symbolu zastępczego, zobacz [obiekty BLOB](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) sekcji.)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy atrybutu QueueTrigger współpracuje z
Można użyć **QueueTrigger** z następujących typów:

* **Ciąg**
* Typ POCO zserializowanym w formacie JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algorytm sondowania
Zestaw SDK zaimplementowano losowe wykładniczej wycofywania algorytmu celu ograniczenia wpływu bezczynności kolejki sondowania kosztów transakcji magazynu.  Po znalezieniu komunikatu zestawu SDK oczekuje dwóch sekund i następnie sprawdza, czy kolejną wiadomość; gdy zostanie znaleziony żaden komunikat czeka około czterech sekund przed ponowną próbą. Po kolejnych nieudanych prób uzyskanie komunikatu w kolejce czas oczekiwania w dalszym ciągu zwiększyć, dopóki nie osiągnie maksymalny czas oczekiwania, domyślnie na jedną minutę. [Maksymalny czas oczekiwania jest konfigurowalne](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Wiele wystąpień
Jeśli aplikacja sieci web jest uruchamiana na wiele wystąpień, ciągłe zadania Webjob działa na każdym komputerze, a każda maszyna zostanie poczekaj, aż wyzwalaczy i próba uruchomienia funkcji. W niektórych scenariuszach, które może to prowadzić do niektórych funkcji dwukrotnie przetwarzania tych samych danych więc funkcji powinna być idempotentności (zapisany, aby je wielokrotnie wywołuje z tymi samymi danymi wejściowymi nie dostarczyło wyników duplikatów).  

## <a name="parallel-execution"></a>Wykonywanie równoległe
Jeśli masz wiele funkcji nasłuchiwanie w kolejkach innego zestawu SDK będą połączeń telefonicznych z nimi równolegle po odebraniu wiadomości jednocześnie.

Dotyczy to po odebraniu wiele komunikatów dla pojedynczej kolejki. Domyślnie zestaw SDK pobiera partii 16 wiadomości w kolejce w czasie i wykonuje funkcję, która przetwarza je równolegle. [Rozmiar partii to konfigurowalne](#how-to-set-configuration-options). Gdy liczba przetwarzanych pobiera w dół do połowy rozmiar partii, zestaw SDK pobiera inna partia i rozpoczyna przetwarzanie tych wiadomości. W związku z tym maksymalną liczbę równoczesnych komunikatów przetwarzanych dla każdej funkcji jest jeden i wielokrotności rozmiar partii. Ten limit dotyczy oddzielnie każdej funkcji, która ma **QueueTrigger** atrybutu. Jeśli nie chcesz wykonywanie równoległe dla wiadomości otrzymanych w jednej kolejki ustawioną 1 rozmiar partii.

## <a name="get-queue-or-queue-message-metadata"></a>Pobierz kolejki lub kolejka komunikatów metadanych
Następujące właściwości komunikatu można uzyskać, dodając parametry w podpisie metody:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **ciąg** queueTrigger (zawiera tekst wiadomości)
* **ciąg** id
* **ciąg** elementu popReceipt
* **int** dequeueCount

Jeśli chcesz pracować bezpośrednio za pomocą interfejsu API magazynu Azure, możesz także dodać **CloudStorageAccount** parametru.

Poniższy przykład zapisuje wszystkie te metadane w rejestrze informacji o aplikacji. W tym przykładzie zarówno logMessage, jak i queueTrigger zawierają zawartość komunikatu w kolejce.

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

Poniżej przedstawiono przykładowy dziennik napisane przez kod przykładowy:

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
Funkcja, która działa w ciągłego zadania WebJob może akceptować **CancellationToken** parametr, który umożliwia systemowi operacyjnemu powiadamianie funkcji, które ma zostać zakończone zadania WebJob. Skorzystaj z tego powiadomienia, aby upewnić się, że funkcja nie nieoczekiwane zakończenie w sposób powodujący, że dane w niespójnym stanie.

Poniższy przykład pokazuje, jak do sprawdzenia zbliżającym się zakończeniu zadania WebJob w funkcji.

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

**Uwaga:** pulpit nawigacyjny może nie poprawnie pokazać stan i dane wyjściowe funkcji, które została zamknięta.

Aby uzyskać więcej informacji, zobacz [łagodne zamykanie zadań Webjob](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Jak utworzyć komunikat z kolejki podczas przetwarzania komunikatu w kolejce
Aby napisać funkcję, która tworzy nowy komunikat kolejki, użyj **kolejki** atrybutu. Podobnie jak **QueueTrigger**, przekazać w nazwie kolejki w postaci ciągu lub możesz [Ustaw nazwę kolejki dynamicznie](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Ciąg wiadomości w kolejce
Poniższy przykładowy kod z systemem innym niż async tworzy nowego komunikatu w kolejce w kolejce o nazwie "outputqueue" o tej samej zawartości jako kolejki komunikat w kolejce o nazwie "inputqueue". (Asynchroniczne Użyj funkcji **IAsyncCollector<T>**  opisane dalej w tej sekcji.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykły stary obiekt CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) wiadomości w kolejce
Aby utworzyć komunikat z kolejki zawiera POCO zamiast ciągu, należy przekazać typ POCO jako parametr wyjściowy do **kolejki** atrybut konstruktora.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

Zestaw SDK automatycznie serializuje obiekt do formatu JSON. Komunikatu w kolejce zawsze jest tworzony, nawet jeśli obiekt ma wartość null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Tworzenie wielu wiadomości lub w funkcji asynchronicznych
Aby utworzyć wiele komunikatów, należy typu parametru dla kolejki wyjściowej **ICollector<T>**  lub **IAsyncCollector<T>**, jak pokazano w poniższym przykładzie.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Każdy komunikat kolejki jest tworzony natychmiast po **Dodaj** metoda jest wywoływana.

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, które atrybutu kolejki współpracuje z
Można użyć **kolejki** atrybutu na następujące typy parametrów:

* **limit ciąg** (tworzy komunikat z kolejki, jeśli wartość parametru jest różna od null, gdy kończy się funkcja)
* **limit byte []** (takich jak działa **ciąg**)
* **limit CloudQueueMessage** (takich jak działa **ciąg**)
* **limit POCO** (typ możliwy do serializacji, tworzy komunikat z obiektem null, jeśli parametru ma wartość null, gdy funkcja kończy się)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (do tworzenia komunikatów ręcznie bezpośrednio za pomocą interfejsu API magazynu Azure)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Użyj zestawu SDK zadań Webjob atrybutów w treści funkcji
Jeśli zachodzi konieczność wykonania dodatkowych czynności w funkcji przed przy użyciu atrybutu zestaw SDK zadań Webjob, takich jak **kolejki**, **obiektu Blob**, lub **tabeli**, można użyć **IBinder** interfejsu.

Poniższy przykład pobiera wiadomość z kolejki wejściowej i tworzy nowy komunikat o tej samej zawartości w kolejki wyjściowej. Nazwa kolejki danych wyjściowych jest ustawiana przez kod w treści funkcji.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

**IBinder** interfejsu można również używać razem **tabeli** i **obiektu Blob** atrybutów.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Jak do odczytywania i zapisywania obiektów blob i tabelach podczas przetwarzania komunikatu w kolejce
**Obiektu Blob** i **tabeli** atrybuty pozwalają na odczytywanie i zapisywanie obiektów blob i tabele. Przykłady w tej sekcji dotyczą obiektów blob. Aby uzyskać przykłady kodu, które pokazują, jak wyzwolić procesów, podczas tworzenia lub aktualizowania obiektów blob, zobacz [jak używać magazynu obiektów blob platformy Azure przy użyciu zestawu SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Ciąg wiadomości w kolejce wyzwalania operacje obiektów blob
Dla komunikatu w kolejce, który zawiera ciąg **queueTrigger** to symbol zastępczy, można użyć w **obiektu Blob** atrybutu **blobPath** parametr, który zawiera treść komunikatu.

W poniższym przykładzie użyto **strumienia** obiekty do odczytywania i zapisywania obiektów blob. Komunikat z kolejki jest nazwą obiektu blob znajdujące się w kontenerze textblobs. Kopiowania obiektu blob z "-new" dołączany do nazwy jest tworzony w tym samym kontenerze.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

**Obiektu Blob** atrybutu ma konstruktora **blobPath** parametr określający nazwę kontenera i obiektów blob. Aby uzyskać więcej informacji dotyczących tego symbolu zastępczego, zobacz [jak używać magazynu obiektów blob platformy Azure przy użyciu zestawu SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).

Gdy atrybut decorates **strumienia** obiektu, określa parametr innego konstruktora **FileAccess** trybie odczytu, zapisu lub odczytu/zapisu.

W poniższym przykładzie użyto **CloudBlockBlob** obiektu do usunięcia obiektu blob. Komunikat z kolejki jest nazwą obiektu blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(zwykły stary obiekt CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) wiadomości w kolejce
W przypadku POCO, zapisane w formacie JSON w komunikacie kolejki, można użyć zastępcze nazw właściwości obiektu w **kolejki** atrybutu **blobPath** parametru. Nazwy właściwości metadanych kolejki służy również jako symbole zastępcze. Zobacz [uzyskać kolejki lub kolejka komunikatów metadanych](#get-queue-or-queue-message-metadata).

Poniższy przykładowy kod kopiuje obiektu blob do nowego obiektu blob z innym rozszerzeniem. Komunikat z kolejki jest **BlobInformation** obiekt, który zawiera **element BlobName** i **BlobNameWithoutExtension** właściwości. Nazwy właściwości są używane jako symbole zastępcze w ścieżce obiektu blob dla **obiektu Blob** atrybutów.

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Zestaw SDK używa [pakietu Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) do serializowania i deserializowania wiadomości. Jeśli tworzysz wiadomości w kolejce w programie, który nie korzysta z zestawu SDK zadań Webjob, można napisać kod, jak w następującym przykładzie, aby utworzyć POCO komunikatu w kolejce, które mogą przeanalizować zestawu SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Jeśli potrzebujesz wykonania dodatkowych czynności w funkcji przed powiązania obiektu blob do obiektu, można użyć atrybutu w treści funkcji, jak pokazano w [Użyj zestawu SDK zadań Webjob atrybutów w treści funkcji](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, których można użyć atrybutu obiektu Blob o
**Obiektu Blob** atrybut może być używany z następujących typów:

* **Strumień** (Odczyt lub zapis, określony za pomocą parametru konstruktora FileAccess)
* **TextReader**
* **TextWriter**
* **ciąg** (odczyt)
* **limit ciąg** (zapisu; tworzy obiektu blob tylko wtedy, gdy parametr ciągu jest różna od null, gdy funkcja zwraca)
* POCO (odczyt)
* limit POCO (zapisu; zawsze tworzy obiektu blob, tworzy jako obiekt null, jeśli parametr POCO ma wartość null, gdy funkcja zwraca)
* **CloudBlobStream** (write)
* **ICloudBlob** (odczytu i zapisu)
* **CloudBlockBlob** (odczytu i zapisu)
* **CloudPageBlob** (odczytu i zapisu)

## <a name="how-to-handle-poison-messages"></a>Sposób obsługi wiadomości
Komunikaty, których zawartość powoduje, że funkcja niepowodzenie są nazywane *zanieczyszczonych komunikatów*. Gdy funkcja nie powiedzie się, komunikat z kolejki nie zostanie usunięta i ostatecznie zostaje pobrana ponownie, powoduje cykl do powtarzania. Zestaw SDK może automatycznie przerwać cyklu po ograniczonej liczby iteracji, lub możesz zrobić to ręcznie.

### <a name="automatic-poison-message-handling"></a>Obsługa automatycznego Trująca wiadomość
Zestaw SDK wywoła funkcję maksymalnie 5 razy przetwarzania komunikatu w kolejce. W przypadku niepowodzenia spróbuj piątej wiadomość zostanie przeniesiona do kolejki skażone. Możesz dowiedzieć się, jak skonfigurować maksymalną liczbę ponownych prób w [sposobu ustawiania opcji konfiguracji](#how-to-set-configuration-options).

Trująca kolejki o nazwie *{originalqueuename}*-skażone. Można wpisać funkcji do przetwarzania komunikatów z kolejki skażone przez rejestrowania ich lub wysyłania powiadomienia tej uwagi ręczne jest wymagane.

W poniższym przykładzie **CopyBlob** funkcja zakończy się niepowodzeniem, gdy komunikatu w kolejce zawiera nazwę obiektu blob, który nie istnieje. W takim przypadku wiadomość zostanie przeniesiona do kolejki copyblobqueue poison z kolejki copyblobqueue. **ProcessPoisonMessage** zarejestruje Trująca wiadomość.

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

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli z tych funkcji po przetworzeniu Trująca wiadomość.

![Dane wyjściowe konsoli dotyczące obsługi uszkodzonych komunikatów](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Trująca wiadomość została ręcznej obsługi
Liczba wiadomości została pobrana do przetwarzania można uzyskać, dodając **int** parametru o nazwie **dequeueCount** do funkcji. Można sprawdzić liczbę kolejki w kodzie funkcji i wykonać własne Trująca wiadomość została obsługa podczas liczba przekracza próg, jak pokazano w poniższym przykładzie.

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

## <a name="how-to-set-configuration-options"></a>Jak ustawić opcje konfiguracji
Można użyć **JobHostConfiguration** typu można ustawić następujące opcje konfiguracji:

* Ustaw parametry połączenia SDK w kodzie.
* Skonfiguruj **QueueTrigger** ustawienia, takie jak maksymalna liczba usuwania z kolejki.
* Pobierz nazwy kolejki z konfiguracji.

### <a name="set-sdk-connection-strings-in-code"></a>Ustawianie parametrów połączenia SDK w kodzie
Ustawianie parametrów połączenia SDK w kodzie pozwala na użycie własne nazwy ciągu połączenia w plikach konfiguracji lub zmiennych środowiskowych, jak pokazano w poniższym przykładzie.

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

### <a name="configure-queuetrigger--settings"></a>Skonfiguruj ustawienia QueueTrigger
Można skonfigurować następujące ustawienia, które dotyczą przetwarzania komunikatu w kolejce:

* Maksymalna liczba wiadomości w kolejce, które zostały pobrane i przetworzone jednocześnie ma być wykonywana równolegle (wartość domyślna to 16).
* Maksymalna liczba ponownych prób przed wysłaniem komunikatu w kolejce do kolejki skażone (wartość domyślna to 5).
* Maksymalny czas oczekiwania przed sondowania ponownie, gdy kolejka jest pusta (wartość domyślna to 1 minuta).

Poniższy przykład przedstawia sposób konfigurowania tych ustawień:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ustawianie wartości dla zestawu SDK zadań Webjob parametrami konstruktora w kodzie
Czasami chcesz określić nazwę kolejki, nazwa obiektu blob lub kontener lub tabeli nazw w kodzie, a nie kodowane. Na przykład można określić nazwę kolejki **QueueTrigger** w zmiennej środowisku i pliku konfiguracji.

Możesz to zrobić przez przekazywanie **NameResolver** do obiektu **JobHostConfiguration** typu. Obejmują specjalne symbole zastępcze ujęta w znaki procentu (%) w parametrach konstruktora atrybut zestaw SDK zadań Webjob i **NameResolver** kodu określa rzeczywiste wartości do użycia zamiast te symbole zastępcze.

Na przykład załóżmy, że chcesz używać kolejki o nazwie logqueuetest w środowisku testowym i jedną o nazwie logqueueprod w środowisku produkcyjnym. Zamiast nazwę kolejki ustalony chcesz określić nazwę wpisu w **appSettings** kolekcji mającą nazwę kolejki rzeczywistych. Jeśli **appSettings** klucz jest logqueue, funkcja może wyglądać jak w poniższym przykładzie.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Twoje **NameResolver** klasy, można uzyskać nazwy kolejki z **appSettings** jak pokazano w poniższym przykładzie:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Możesz przekazać **NameResolver** klasy w celu **JobHost** obiektów, jak pokazano w poniższym przykładzie.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Uwaga:** nazwy obiektów blob, tabel i kolejki rozwiązywane są zawsze funkcja jest nazywana, ale nazwy kontenera obiektów blob są rozpoznawane tylko podczas uruchamiania aplikacji. Nie można zmienić nazwy kontenera obiektów blob, podczas uruchamiania zadania.

## <a name="how-to-trigger-a-function-manually"></a>Sposób włączania funkcji ręcznie
Aby ręcznie zainicjuje funkcję, użyj **wywołać** lub **CallAsync** metody w **JobHost** obiektu i **NoAutomaticTrigger** atrybutu funkcji, jak pokazano w poniższym przykładzie.

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

## <a name="how-to-write-logs"></a>Jak napisać dzienników
Pulpit nawigacyjny zawiera dzienniki w dwóch miejscach: strona dla zadania WebJob i strony dla poszczególnych wywołań zadania WebJob.

![Dzienniki na stronie zadania WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Dzienniki na stronie wywołania funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Dane wyjściowe z konsoli metody należy wywołać funkcji lub w **Main()** metoda pojawia się na stronie pulpitu nawigacyjnego dla zadania WebJob, a nie dla wywołania metody określonej strony. Na stronie pulpitu nawigacyjnego dla wywołania metody zostaną wyświetlone dane wyjściowe z obiektu TextWriter, który można pobrać z parametrem w podpisie metody.

Dane wyjściowe konsoli nie można połączyć z wywołania konkretnej metody, ponieważ konsola jest jednowątkowy, gdy wiele funkcji zadanie może działać na tym samym czasie. Dlatego zestaw SDK zawiera każde wywołanie funkcji z własnego obiektu zapisującego unikatowy dziennika.

Aby zapisać [dzienniki śledzenia aplikacji](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), użyj **Console.Out** (tworzy Dzienniki oznaczone jako informacje) i **Console.Error** (tworzy Dzienniki oznaczone jako błąd). Alternatywą jest użycie [śledzenia lub TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), która zapewnia pełne, ostrzeżenie, i krytyczne poziomy oprócz informacji i błędów. Dzienniki śledzenia aplikacji są wyświetlane w plikach dzienników aplikacji sieci web, tabelach platformy Azure lub w zależności od sposobu skonfigurowania aplikacji sieci web platformy Azure obiektów blob Azure. Jak wszystkie dane wyjściowe konsoli, najnowsze Dzienniki aplikacji 100 również zostać wyświetlony na stronie pulpitu nawigacyjnego dla zadania WebJob nie strony dla wywołania funkcji.

Dane wyjściowe konsoli zostanie wyświetlony na pulpicie nawigacyjnym tylko wtedy, gdy program jest uruchomiony w zadań WebJob Azure, nie, jeśli program działa lokalnie lub w niektórych innych środowiska.

Można wyłączyć rejestrowanie przez ustawienie parametrów połączenia pulpitu nawigacyjnego na wartość null. Aby uzyskać więcej informacji, zobacz [sposobu ustawiania opcji konfiguracji](#how-to-set-configuration-options).

W poniższym przykładzie pokazano kilka sposobów zapisywanie dzienników:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

Na pulpicie nawigacyjnym zestawu SDK zadań Webjob dane wyjściowe z **TextWriter** pokazuje się po przejściu do strony dla określonego wywołania funkcji i wybierz obiektów **dane wyjściowe Przełącz**:

![Łącze wywołania](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Dzienniki na stronie wywołania funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na pulpicie nawigacyjnym zestawu SDK zadań Webjob najnowszych 100 wierszy konsoli output Pokaż zapasowej przejdź do strony dla zadania WebJob (a nie dla wywołania funkcji) i wybierz **dane wyjściowe Przełącz**.

![Przełącz danych wyjściowych](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Ciągłe zadanie WebJob Dzienniki aplikacji wyświetlani w/data/zadania/ciągłego/*{webjobname}*/job_log.txt w systemie plików aplikacji sieci web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

W usłudze Azure blob wygląd Dzienniki aplikacji następująco: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write — Witaj świecie!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error — Witaj świecie!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out — Witaj świecie!,

W tabeli platformy Azure **Console.Out** i **Console.Error** dzienniki wyglądać następująco:

![Dziennik informacyjny w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Dziennik błędów w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule udostępnił przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze dotyczące pracy z kolejek platformy Azure. Aby uzyskać więcej informacji o sposobie używania zadań Webjob Azure i zestaw SDK zadań Webjob, zobacz [zasoby dokumentacji zadań Webjob Azure](http://go.microsoft.com/fwlink/?linkid=390226).

