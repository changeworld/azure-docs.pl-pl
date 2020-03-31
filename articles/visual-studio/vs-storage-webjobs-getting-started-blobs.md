---
title: Wprowadzenie do magazynu obiektów blob przy użyciu programu Visual Studio (projekty webjob)
description: Jak rozpocząć korzystanie z magazynu obiektów Blob w projekcie WebJob po nawiązaniu połączenia z magazynem platformy Azure przy użyciu usług połączonych z programem Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299978"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Wprowadzenie do usługi Azure Blob Storage i usług połączonych z programem Visual Studio (projekty webjob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera przykłady kodu języka C#, które pokazują, jak wyzwolić proces podczas tworzenia lub aktualizowanie obiektu blob platformy Azure. Przykłady kodu używają [zestawów SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) w wersji 1.x. Po dodaniu konta magazynu do projektu WebJob przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio, zainstalowany jest odpowiedni pakiet Azure Storage NuGet, odpowiednie odwołania .NET są dodawane do projektu, a parametry połączenia dla konta magazynu są aktualizowane w pliku App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak wyzwolić funkcję podczas tworzenia lub aktualizowanie obiektu blob
W tej sekcji pokazano, jak używać atrybutu **BlobTrigger.**

 **Uwaga:** WebJobs SDK skanuje pliki dziennika, aby obserwować nowe lub zmienione obiekty blob. Proces ten jest z natury powolny; funkcja może nie zostać wyzwolona do kilku minut lub dłużej po utworzeniu obiektu blob.  Jeśli aplikacja musi przetwarzać obiekty blob natychmiast, zalecaną metodą jest utworzenie komunikatu kolejki podczas tworzenia obiektu blob i użyć **atrybutu QueueTrigger** zamiast atrybutu **BlobTrigger** w funkcji, która przetwarza obiekt blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Pojedynczy symbol zastępczy nazwy obiektu blob z rozszerzeniem
Poniższy przykładowy kod kopiuje obiekty blob tekstu, które pojawiają się w kontenerze *wejściowym* do kontenera *wyjściowego:*

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atrybutów przyjmuje parametr string, który określa nazwę kontenera i symbol zastępczy nazwy obiektu blob. W tym przykładzie jeśli obiekt *blob1.txt* jest tworzony w kontenerze *wejściowym,* funkcja tworzy obiekt blob1.txt w kontenerze *wyjściowym.* *Blob1.txt*

Można określić wzorzec nazwy z symbolem zastępczym nazwy obiektu blob, jak pokazano w poniższym przykładzie kodu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ten kod kopiuje tylko obiekty BLOB, które mają nazwy zaczynające się od "original-". Na przykład *original-Blob1.txt* w kontenerze *wejściowym* jest kopiowany do *copy-Blob1.txt* w kontenerze *danych wyjściowych.*

Jeśli chcesz określić wzorzec nazwy dla nazw obiektów blob, które mają nawiasy klamrowe w nazwie, podwoić nawiasy klamrowe. Na przykład, jeśli chcesz znaleźć obiekty BLOB w kontenerze *obrazów,* które mają nazwy takie jak:

        {20140101}-soundfile.mp3

użyj tego dla swojego wzoru:

        images/{{20140101}}-{name}

W tym przykładzie wartością zastępczą *nazwy* będzie *plik.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Oddzielne symbole zastępcze nazw obiektów blob i rozszerzenia
Poniższy przykładowy kod zmienia rozszerzenie pliku, ponieważ kopiuje obiekty BLOB, które pojawiają się w kontenerze *wejściowym* do kontenera *wyjściowego.* Kod rejestruje rozszerzenie *wejściowego* obiektu blob i ustawia rozszerzenie *wyjściowego* obiektu blob na *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, które można powiązać z obiektami blob
Atrybutu **BlobTrigger** można użyć następujących typów:

* **Ciąg**
* **Textreader**
* **Strumień**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Inne typy deserializowane przez [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Jeśli chcesz pracować bezpośrednio z kontem usługi Azure storage, możesz również dodać parametr **CloudStorageAccount** do podpisu metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Uzyskiwanie zawartości obiektu blob tekstu przez powiązanie z ciągiem
Jeśli oczekuje się obiektów blob, **blobTrigger** można zastosować do parametru **ciągu.** Poniższy przykładowy kod wiąże obiekt blob tekstowy z parametrem **ciągu** o nazwie **logMessage**. Funkcja używa tego parametru do zapisania zawartości obiektu blob na pulpicie nawigacyjnym zestawu SDK webjobs.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Uzyskiwanie seryjnych treści obiektów blob przy użyciu funkcji ICloudBlobStreamBinder
Poniższy przykładowy kod używa klasy, która implementuje **ICloudBlobStreamBinder,** aby włączyć atrybut **BlobTrigger** do powiązania obiektu blob z **typem WebImage.**

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

Kod powiązania **WebImage** jest dostarczany w klasie **WebImageBinder,** która wywodzi się z **iCloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Jak radzić sobie z trującymi plamami
Gdy funkcja **BlobTrigger** nie powiedzie się, SDK wywołuje go ponownie, w przypadku, gdy błąd został spowodowany przez błąd przejściowy. Jeśli błąd jest spowodowany przez zawartość obiektu blob, funkcja kończy się niepowodzeniem za każdym razem, gdy próbuje przetworzyć obiekt blob. Domyślnie SDK wywołuje funkcję do 5 razy dla danego obiektu blob. Jeśli piąta próba zakończy się niepowodzeniem, SDK dodaje wiadomość do kolejki o nazwie *webjobs-blobtrigger-poison*.

Maksymalna liczba ponownych prób jest konfigurowalna. To samo ustawienie **MaxDequeueCount** jest używane do obsługi trujących obiektów blob i obsługi wiadomości kolejki trującej.

Komunikat kolejki dla obiektów blob poison jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w formacie *{Nazwa WebJob}*. Funkcje. *{Nazwa funkcji}*, na przykład: WebJob1.Functions.CopyBlob)
* Typ obiektu BlobType ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* Nazwa obiektu Blob
* ETag (identyfikator wersji obiektu blob, na przykład: "0x8D1DC6E70A277EF")

W poniższym przykładzie kodu **CopyBlob** funkcja ma kod, który powoduje, że nie powiódł się za każdym razem, gdy jest wywoływana. Po SDK wywołuje go dla maksymalnej liczby ponownych prób, komunikat jest tworzony w kolejce obiektu blob trucizny i że komunikat jest przetwarzany przez **Funkcję LogPoisonBlob.**

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

SDK automatycznie deserializuje komunikat JSON. Oto **PoisonBlobMessage** klasy:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algorytm sondowania obiektów blob
WebJobs SDK skanuje wszystkie kontenery określone przez atrybuty **BlobTrigger** na początku aplikacji. Na dużym koncie magazynu to skanowanie może zająć trochę czasu, więc może upłynąć trochę czasu, zanim zostaną znalezione nowe obiekty blobtrigger i funkcje **BlobTrigger.**

Aby wykryć nowe lub zmienione obiekty blob po uruchomieniu aplikacji, SDK okresowo odczytuje z dzienników magazynu obiektów blob. Dzienniki obiektów blob są buforowane i tylko uzyskać fizycznie zapisywane co 10 minut lub tak, więc może być znaczne opóźnienie po utworzeniu lub zaktualizowaniu obiektu blob przed wykonaniem odpowiedniej funkcji **BlobTrigger.**

Istnieje wyjątek dla obiektów blob, które można utworzyć przy użyciu atrybutu **blob.** Gdy webjobs SDK tworzy nowy obiekt blob, przekazuje nowy obiekt blob natychmiast do wszystkich pasujących funkcji **BlobTrigger.** W związku z tym, jeśli masz łańcuch wejść i wyjść obiektów blob, SDK można przetwarzać je wydajnie. Ale jeśli chcesz, aby małe opóźnienia z uruchomionymi funkcjami przetwarzania obiektów blob dla obiektów blob, które są tworzone lub aktualizowane w inny sposób, zalecamy użycie **funkcji QueueTrigger** zamiast **BlobTrigger**.

### <a name="blob-receipts"></a>Przyjęcia obiektów blob
WebJobs SDK upewnia się, że żadna funkcja **BlobTrigger** nie zostanie wywołana więcej niż jeden raz dla tego samego nowego lub zaktualizowanego obiektu blob. Robi to, utrzymując *przyjęcia obiektów blob* w celu ustalenia, czy dana wersja obiektu blob została przetworzona.

Potwierdzenia obiektów blob są przechowywane w kontenerze o nazwie *azure-webjobs-hosts* na koncie usługi Azure storage określonym przez ciąg połączenia Usługi AzureWebJobsStorage. Paragon obiektu blob ma następujące informacje:

* Funkcja, która została wywołana dla obiektu blob ("*{Nazwa WebJob}*. Funkcje. *{Nazwa funkcji}*", na przykład: "WebJob1.Functions.CopyBlob")
* Nazwa kontenera
* Typ obiektu blob ("BlockBlob" lub "PageBlob")
* Nazwa obiektu blob
* ETag (identyfikator wersji obiektu blob, na przykład: "0x8D1DC6E70A277EF")

Jeśli chcesz wymusić ponowne przetwarzanie obiektu blob, można ręcznie usunąć przyjęcie obiektu blob dla tego obiektu blob z kontenera *azure-webjobs-hosts.*

## <a name="related-topics-covered-by-the-queues-article"></a>Tematy pokrewne objęte artykułem kolejki
Aby uzyskać informacje dotyczące obsługi przetwarzania obiektów blob wyzwalanego przez komunikat kolejki lub scenariuszy zestawów SDK webjobs niespecyfizowanych dla przetwarzania obiektów blob, zobacz [Jak używać magazynu kolejek platformy Azure z zestawem SDK webjobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Tematy pokrewne omówione w tym artykule są następujące:

* Funkcje asynchronizowe
* Wiele wystąpień
* Wdzięczne zamknięcie
* Używanie atrybutów SDK webjobs w treści funkcji
* Ustaw parametry połączenia zestawu SDK w kodzie.
* Ustawianie wartości parametrów konstruktora zestawu SDK webjobs w kodzie
* Skonfiguruj **MaxDequeueCount** dla obsługi obiektu blob trucizny.
* Wyzwalanie funkcji ręcznie
* Zapis dzienniki

## <a name="next-steps"></a>Następne kroki
W tym artykule podano przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z obiektami blob platformy Azure. Aby uzyskać więcej informacji na temat korzystania z usługi Azure WebJobs i webjobs SDK, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

