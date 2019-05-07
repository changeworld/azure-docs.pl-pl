---
title: Równoległe przekazywanie dużych ilości danych losowych do usługi Azure Storage | Microsoft Docs
description: Dowiedz się, jak przy użyciu zestawu Azure SDK przekazywać równolegle duże ilości danych losowych na konto usługi Azure Storage
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0673d97f755d7e01d42d0be7c611720ff1e4ad01
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65187779"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Równoległe przekazywanie dużych ilości danych losowych do usługi Azure Storage

Ten samouczek jest drugą częścią serii. Przedstawia sposób wdrażania aplikacji, która przekazuje dużą ilość danych losowych na konto usługi Azure Storage.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie parametrów połączenia
> * Kompilowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Usługa Azure Blob Storage to skalowalna usługa do przechowywania danych. Aby zagwarantować maksymalną wydajność aplikacji, warto wiedzieć, jak działa magazyn obiektów blob. Warto znać ograniczenia obiektów blob na platformie Azure, które opisano w temacie [Blob storage scalability targets (Wartości docelowe skalowalności usługi Blob Storage)](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Nazewnictwo partycji](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) to kolejny ważny czynnik, który należy wziąć pod uwagę, opracowując aplikacje o wysokiej wydajności, korzystające z obiektów blob. Podczas skalowania i równoważenia obciążenia w usłudze Azure Storage używany jest schemat partycjonowania na podstawie zakresów. Ta konfiguracja oznacza, że pliki z podobną konwencją nazewnictwa lub podobnymi prefiksami są umieszczane w tej samej partycji. Ta logika dotyczy także nazwy kontenera, do którego przekazywane są pliki. W tym samouczku używane są pliki, których nazwa zawiera identyfikator GUID, a ich zawartość jest generowana losowo. Są one przekazywane do pięciu różnych kontenerów o losowych nazwach.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, konieczne jest ukończenie poprzedniego samouczka na temat usługi Storage: [Tworzenie maszyny wirtualnej i konta magazynu dla skalowalnej aplikacji][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Tworzenie zdalnej sesji na maszynie wirtualnej

Użyj następującego polecenia na maszynie lokalnej, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zamień adres IP na publiczny adres IP Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurowanie parametrów połączenia

W witrynie Azure Portal przejdź do swojego konta magazynu. Wybierz pozycję **Klucze dostępu** w obszarze **Ustawienia** konta magazynu. Skopiuj **parametry połączenia** z klucza podstawowego lub pomocniczego. Zaloguj się do maszyny wirtualnej utworzonej w poprzednim samouczku. Otwórz **wiersz polecenia** z uprawnieniami administratora i uruchom polecenie `setx` z przełącznikiem `/m`. To polecenie zapisuje zmienną środowiskową ustawień maszyny. Zmienna środowiskowa będzie dostępna po ponownym załadowaniu **wiersza polecenia**. Zastąp ciąg **\<storageConnectionString\>** w następującym przykładzie:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Po zakończeniu operacji otwórz kolejny **wiersz polecenia**, przejdź do lokalizacji `D:\git\storage-dotnet-perf-scale-app` i wpisz polecenie `dotnet build`, aby skompilować aplikację.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Przejdź do adresu `D:\git\storage-dotnet-perf-scale-app`.

Wpisz polecenie `dotnet run`, aby uruchomić aplikację. Przy pierwszym uruchomieniu polecenia `dotnet` wypełnia ono lokalną pamięć podręczną pakietu w celu zwiększenia szybkości przywracania i umożliwienia dostępu w trybie offline. Wykonanie tego polecenia trwa maksymalnie minutę i odbywa się tylko raz.

```
dotnet run
```

Aplikacja tworzy pięć losowo nazwanych kontenerów i rozpoczyna przekazywanie plików z katalogu przemieszczania do konta magazynu. Aplikacja ustawia minimalną liczbę wątków na 100, a wartość [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) na 100, aby zezwalać na dużą liczbę współbieżnych połączeń podczas uruchamiania aplikacji.

Oprócz skonfigurowania ustawień wątków oraz limitu połączeń, opcje [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet) metody [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) są konfigurowane tak, aby używać funkcji równoległości i wyłączyć sprawdzanie poprawności wartości skrótu MD5. Pliki są przekazywane w blokach po 100 MB. Ta konfiguracja zapewnia lepszą wydajność, ale może być kosztowna, jeśli jest używana w sieci o niskiej wydajności — jeśli wystąpi błąd, konieczne będzie ponowne przekazanie całego bloku 100 MB.

|Właściwość|Wartość|Opis|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| To ustawienie dzieli obiekt blob na bloki na potrzeby przekazywania. Aby osiągnąć najwyższą wydajność, ta wartość powinna być 8-krotnością liczby rdzeni. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Ta właściwość wyłącza sprawdzanie skrótu MD5 przekazanej zawartości. Wyłączenie sprawdzania poprawności skrótu MD5 zwiększa szybkość transferu. Jednak poprawność i integralność przekazywanych plików nie jest wtedy sprawdzana.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Ta właściwość określa, czy skrót MD5 jest obliczany i przechowywany w pliku.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| Czas wycofania: 2 sekundy, maksymalna liczba ponawianych prób: 10 |Określa zasady ponawiania żądań. Połączenia zakończone niepowodzeniem są ponawianie. W tym przykładzie skonfigurowano zasadę [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry?view=azure-dotnet) z 2-sekundowych wycofaniem, a maksymalna liczba prób wynosi 10. To ustawienie jest istotne, jeśli aplikacja zbliży się do osiągnięcia [docelowych wartości skalowalności usługi Blob Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

Zadanie `UploadFilesAsync` jest przedstawione w poniższym przykładzie:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Poniższy przykład to skrócone dane wyjściowe aplikacji uruchomionej w systemie Windows.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Weryfikowanie połączeń

Podczas przekazywania plików możesz sprawdzić liczbę współbieżnych połączeń do konta magazynu. Otwórz **wiersz polecenia** i wpisz polecenie `netstat -a | find /c "blob:https"`. To polecenie zwraca liczbę połączeń, które są obecnie otwarte przy użyciu polecenia `netstat`. Poniższy przykład przedstawia dane wyjściowe podobne do tych, które zobaczysz podczas samodzielnego wykonywania instrukcji samouczka. Jak widać na tym przykładzie, podczas przekazywania plików losowych na konto magazynu otwarto 800 połączeń. Ta wartość zmienia się podczas przekazywania. Przekazywanie w równoległych blokach znacząco zmniejsza ilość czasu potrzebnego na przekazanie zawartości.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Kolejne kroki

W drugiej części serii omówiono równoległe przekazywanie dużej ilości danych losowych na konto magazynu, w tym następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie parametrów połączenia
> * Kompilowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Przejdź do trzeciej części serii, aby dowiedzieć się, jak pobierać dużą ilość danych z konta magazynu.

> [!div class="nextstepaction"]
> [Pobieranie dużych ilości danych losowych z usługi Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
