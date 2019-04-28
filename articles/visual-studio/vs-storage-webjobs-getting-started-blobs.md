---
title: Rozpoczynanie pracy z usługą blob storage i Visual Studio podłączone usługi (projekty, zadania WebJob) | Dokumentacja firmy Microsoft
description: Jak rozpocząć korzystanie z magazynu obiektów Blob projektu zadania WebJob, po nawiązaniu połączenia z usługi Azure storage przy użyciu programu Visual Studio podłączone usługi.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5a7c16e6ac565d1660fee02cb7df178344b195e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122927"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Rozpoczynanie pracy z usługą Azure Blob storage i Visual Studio podłączone usługi (projekty, zadania WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera C# przykłady kodu, które pokazują, jak wyzwolić proces w przypadku tworzenia lub aktualizowania obiektu blob platformy Azure. Przykłady kodu, użyj [zestawu SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) wersji 1.x. Po dodaniu konta magazynu do projektu zadania WebJob przy użyciu programu Visual Studio **Dodaj usługi połączone** okno dialogowe, jest zainstalowany odpowiedni pakiet NuGet usługi Azure Storage, odpowiednie odwołania .NET są dodawane do projektu, i Parametry połączenia dla konta magazynu są aktualizowane w pliku App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak wyzwolić funkcję podczas tworzenia lub aktualizowania obiektu blob
W tej sekcji pokazano, jak używać **BlobTrigger** atrybutu.

 **Uwaga:** Zestaw SDK zadań Webjob skanuje pliki dziennika, aby obejrzeć dla nowych lub zmienionych obiektów blob. Ten proces jest natury powolne; funkcja może nie wyzwalane do kilku minut lub dłużej po utworzeniu obiektu blob.  Jeśli aplikacja wymaga w celu przetwarzania obiektów BLOB od razu, zalecaną metodą jest utworzenia komunikatu w kolejce, podczas tworzenia obiektu blob i używania **QueueTrigger** atrybutu zamiast **BlobTrigger** atrybut dla funkcji, która przetwarza obiekt blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Pojedynczego symbolu zastępczego dla nazwy obiektu blob z rozszerzeniem
Poniższy przykładowy kod kopiuje tekst obiektów blob, które pojawiają się w *wejściowych* kontener *dane wyjściowe* kontenera:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atrybutu ma parametr ciągu, który określa nazwę kontenera i symbolu zastępczego dla nazwy obiektu blob. W tym przykładzie, jeśli nazwę obiektu blob *Blob1.txt* jest tworzony w *wejściowych* kontenera, funkcja tworzy obiekt blob o nazwie *Blob1.txt* w *danychwyjściowych* kontenera.

Należy określić wzorzec nazwy z symbolem zastępczym nazwa obiektu blob, jak pokazano w następującym przykładzie kodu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ten kod powoduje skopiowanie tylko obiekty BLOB, które mają nazwy rozpoczynające się od "oryginalnego-". Na przykład *Blob1.txt oryginalny* w *wejściowych* kontenera zostanie skopiowany do *Blob1.txt kopiowania* w *dane wyjściowe* kontenera.

Jeśli musisz określić wzorzec nazw dla nazw obiektów blob, mających nawiasów klamrowych w nazwie dwukrotnie nawiasów klamrowych. Na przykład, jeśli chcesz znaleźć obiekty BLOB w *obrazów* kontenera, w którym mają nazwy w następujący sposób:

        {20140101}-soundfile.mp3

Użyj tego wzorca usługi:

        images/{{20140101}}-{name}

W tym przykładzie *nazwa* wartość symbolu zastępczego będzie *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Symbole zastępcze nazwę i rozszerzenie oddzielnych obiektów blob
Poniższy przykładowy kod powoduje zmianę rozszerzenia pliku jako kopiowania obiektów blob, które pojawiają się w *wejściowych* kontener *dane wyjściowe* kontenera. Ten kod rejestruje rozszerzenie *wejściowych* obiektów blob i Ustawia rozszerzenie *dane wyjściowe* obiektu blob do *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, które można powiązać obiekty BLOB
Możesz użyć **BlobTrigger** atrybutu w następujących typów:

* **ciąg**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Inne typy przeprowadzona przez [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Jeśli chcesz współpracować bezpośrednio z konta magazynu platformy Azure, możesz również dodać **CloudStorageAccount** parametr podpis metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Pobieranie zawartości obiektu blob tekstu przez powiązanie parametrów
Jeżeli oczekuje tekstu obiektów blob **BlobTrigger** mogą być stosowane do **ciąg** parametru. Poniższy przykładowy kod wiąże obiekt blob tekstu do **ciąg** parametr o nazwie **logmessage —**. Funkcja używa tego parametru do zapisania zawartość obiektu blob do pulpitu nawigacyjnego zestawu SDK usługi WebJobs.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Wprowadzenie serializacji zawartość obiektu blob przy użyciu ICloudBlobStreamBinder
Poniższy przykładowy kod używa klasy, która implementuje **ICloudBlobStreamBinder** umożliwiające **BlobTrigger** atrybut do powiązania obiektu blob do **WebImage** typu.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

**WebImage** kod powiązania jest podawany jako **WebImageBinder** klasę pochodzącą od **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Jak obsługiwać skażone obiektów blob
Gdy **BlobTrigger** funkcja kończy się niepowodzeniem, zestaw SDK wywołuje go ponownie w przypadku, gdy przyczyną niepowodzenia był błąd przejściowy. Jeśli błąd jest spowodowany przez zawartość obiektu blob, funkcja kończy się niepowodzeniem, za każdym razem, gdy próbuje przetworzyć obiektu blob. Domyślnie zestaw SDK wywołuje funkcję maksymalnie 5 razy dla danego obiektu blob. Jeśli piątym kończy się niepowodzeniem, zestaw SDK dodaje komunikat do kolejki o nazwie *webjobs-blobtrigger-poison*.

Maksymalna liczba ponownych prób może zostać skonfigurowana. Taki sam **MaxDequeueCount** ustawienie jest używane do obsługi skażone obiektów blob i obsługa komunikatów w kolejce skażone.

Komunikat w kolejce dla obiektów blob poison to obiekt JSON, który zawiera następujące właściwości:

* FunctionId (w formacie *{Nazwa zadania WebJob}*. Funkcje. *{Nazwa funkcji}*, na przykład: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* BlobName
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

W poniższym przykładzie kodu **CopyBlob** funkcja ma kod, który powoduje, że niepowodzenie za każdym razem, gdy jest wywoływana. Po zestaw SDK wymaga ona maksymalną liczbę ponownych prób, komunikat jest tworzony w kolejce skażone obiektów blob i ten komunikat jest przetwarzany przez **LogPoisonBlob** funkcji.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Zestaw SDK automatycznie deserializuje komunikat JSON. Oto **PoisonBlobMessage** klasy:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algorytm sondowania obiektów blob
Zestaw SDK zadań Webjob skanuje wszystkie kontenery, określony przez **BlobTrigger** atrybuty przy uruchamianiu aplikacji. W ramach konta magazynu dużych to skanowanie może zająć trochę czasu, więc może być chwilę, zanim zostaną znalezione nowe obiekty BLOB i **BlobTrigger** są wykonywane funkcje.

Aby wykryć nowe lub zmienione obiekty BLOB, po uruchomieniu aplikacji, zestaw SDK co jakiś czas odczytuje z dzienników magazynu obiektów blob. Dzienniki obiektów blob są buforowane i tylko fizycznie są zapisywane co 10 minut lub tak, dlatego mogą występować znaczne opóźnienie po obiekt blob jest tworzony lub aktualizowany przed odpowiednimi **BlobTrigger** funkcja wykonuje.

Występuje wyjątek dla obiektów blob, które tworzysz przy użyciu **Blob** atrybutu. Gdy zestaw SDK zadań Webjob tworzy nowy obiekt blob, przekazuje on nowy obiekt blob bezpośrednio do żadnych pasujących **BlobTrigger** funkcji. W związku z tym jeśli łańcuch obiektu blob danych wejściowych i wyjściowych, zestaw SDK może je przetwarzać wydajnie. Ale jeśli chcesz małych opóźnień uruchamiania funkcji do przetwarzania dla obiektów blob, które są tworzone lub zaktualizowany w inny sposób obiekt blob, zalecamy użycie **QueueTrigger** zamiast **BlobTrigger**.

### <a name="blob-receipts"></a>Potwierdzenia obiektów blob
Zestaw SDK zadań Webjob zapewniają, że nie **BlobTrigger** funkcja jest wywoływana więcej niż jeden raz dla tego samego obiektu blob nowe lub zaktualizowane. Jest to realizowane poprzez utrzymywanie *blob potwierdzenia* w celu ustalenia, jeśli wersja danego obiektu blob została przetworzona.

Potwierdzenia obiektów blob są przechowywane w kontenerze o nazwie *azure webjobs hostów* koncie magazynu platformy Azure, określona przez ciąg połączenia AzureWebJobsStorage. Odbieranie obiektów blob zawiera następujące informacje:

* Funkcja, która została wywołana dla obiektu blob ("*{Nazwa zadania WebJob}*. Funkcje. *{Nazwa funkcji}*", na przykład: "WebJob1.Functions.CopyBlob")
* Nazwa kontenera
* Typ obiektu blob ("BlockBlob" lub "PageBlob")
* Nazwa obiektu blob
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

Jeśli chcesz wymusić ponowne przetworzenie obiektu blob, można ręcznie usunąć potwierdzenia obiektów blob, dla tego obiektu blob z *azure webjobs hostów* kontenera.

## <a name="related-topics-covered-by-the-queues-article"></a>Tematy pokrewne objętych artykułem kolejek
Aby uzyskać informacje dotyczące obsługi przetwarzania obiektów blob wyzwolone przez komunikatu w kolejce lub dla zestawu SDK usługi WebJobs scenariuszy nie odnoszą się do obiektu blob przetwarzania, zapoznaj się z [sposób użycia usługi Azure queue storage z zestawem SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Tematy pokrewne omówione w tym artykule są następujące:

* Funkcje asynchroniczne
* Wiele wystąpień
* Łagodne zamykanie
* Używanie atrybutów zestawu SDK usługi WebJobs w treści funkcji
* Ustaw parametry połączenia SDK w kodzie.
* Ustawianie wartości dla zestawu SDK usługi WebJobs parametry konstruktora w kodzie
* Konfigurowanie **MaxDequeueCount** obsługi skażone obiektu blob.
* Ręczne wyzwalanie funkcji
* Zapisywanie dzienników

## <a name="next-steps"></a>Kolejne kroki
W tym artykule udostępnił przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze dotyczące pracy z obiektami blob platformy Azure. Aby uzyskać więcej informacji o sposobie używania usługi Azure WebJobs i zestaw SDK zadań Webjob, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

