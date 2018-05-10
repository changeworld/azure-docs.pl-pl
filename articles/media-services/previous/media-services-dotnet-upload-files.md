---
title: Przekazywanie plików do konta usługi Media Services przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać zawartości nośnika do usługi Media Services przez tworzenie i przekazywanie zasoby.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: juliako
ms.openlocfilehash: 0197aabbb3a0594ed8a7c54282491298fdf7cd23
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Przekazywanie plików do konta usługi Media Services przy użyciu platformy .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. **Zasobów** może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst ścieżek i napisów plików (oraz metadane dotyczące tych plików.)  Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze, na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

Pliki w elementach zawartości są nazywane **plikami elementów zawartości**. **AssetFile** wystąpienia oraz plik multimedialna są dwa różne obiekty. Wystąpienia AssetFile zawiera metadanych dotyczących pliku nośnika, a plik nośnika zawiera zawartość multimedialna.

> [!NOTE]
> Następujące kwestie:
> 
> * Usługa Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresy URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent jest niedozwolone. Wartość **nazwa** właściwość nie może mieć następujące [procent kodowanie zarezerwowanych znaków](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.
> * Długość nazwy nie może być większa niż 260 znaków.
> * Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.
> * Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.
> 

Podczas tworzenia zasobów, można określić następujące opcje szyfrowania:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. Korzystając z tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.
  Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji: 
* **CommonEncryption** — ta opcja umożliwia przekazanie zawartości, który został już szyfrowane i chronione za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (na przykład, Smooth Streaming chronione za pomocą technologii PlayReady DRM).
* **EnvelopeEncrypted** — Użyj tej opcji, Jeśli przesyłasz HLS zaszyfrowanych z użyciem standardu AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.
* **StorageEncrypted** — zawartości lokalnie, przy użyciu standardu AES 256-bitowego szyfruje i przekazuje go do magazynu Azure gdzie jest przechowywana szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
  
    Usługa Media Services udostępnia szyfrowanie magazynu na dysku dla zasobów, nie w locie podobnie jak Menedżer prawami cyfrowymi (DRM).
  
    Jeśli element zawartości jest szyfrowany w magazynie, należy skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

Jeśli określisz dla zawartości do zaszyfrowania z **CommonEncrypted** opcji lub **EnvelopeEncypted** opcji, należy skojarzyć zawartości z **ContentKey**. Aby uzyskać więcej informacji, zobacz [tworzenie ContentKey](media-services-dotnet-create-contentkey.md). 

Jeśli określisz dla zawartości do zaszyfrowania z **StorageEncrypted** opcję SDK usługi Media Services dla platformy .NET tworzy **StorateEncrypted** **ContentKey** dla zawartości.

W tym artykule pokazano, jak na potrzeby przekazywania plików do zawartości Media Services SDK .NET usługi Media Services, a także rozszerzenia .NET SDK usługi Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Przekaż pojedynczy plik z zestawu .NET SDK usługi multimediów
Następujący kod do przekazania pojedynczego pliku będzie używał programu .NET. AccessPolicy i Locator są tworzone i niszczone przy użyciu funkcji przekazywania. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Przekazywania wielu plików z zestawu .NET SDK usługi multimediów
Poniższy kod przedstawia sposób tworzenia elementu zawartości i przekazywania wielu plików.

Kod wykonuje następujące czynności:

* Tworzy pusty zasobów przy użyciu metody CreateEmptyAsset zdefiniowane w poprzednim kroku.
* Tworzy **AccessPolicy** wystąpienia, który definiuje uprawnienia i czas trwania dostępu do elementu zawartości.
* Tworzy **lokalizatora** wystąpienia, która zapewnia dostęp do zasobów.
* Tworzy **BlobTransferClient** wystąpienia. Ten typ przedstawia klienta, który działa na obiekty BLOB platformy Azure. W tym przykładzie klient monitoruje postęp przekazywania. 
* Wylicza pliki w określonym katalogu i tworzy **AssetFile** wystąpienia dla każdego pliku.
* Przekazywanie plików do usługi Media Services przy użyciu **UploadAsync** metody. 

> [!NOTE]
> Metoda UploadAsync służy do zapewnienia, że nie blokuje połączenia i pliki są wysyłane równolegle.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Podczas przekazywania dużej liczby zasobów, należy rozważyć następujące kwestie:

* Utwórz nową **CloudMediaContext** obiektu na wątek. **CloudMediaContext** klasa nie jest bezpieczne wątkowo.
* Zwiększ NumberOfConcurrentTransfers z domyślnej wartości 2 wyższej wartości podobnie jak 5. Ustawienie tej właściwości dotyczy wszystkich wystąpień **CloudMediaContext**. 
* Zachowaj ParallelTransferThreadCount na wartość domyślną równą 10.

## <a id="ingest_in_bulk"></a>Pozyskaniu elementów zawartości zbiorczo przy użyciu zestawu .NET SDK usługi multimediów
Przekazywanie zawartości dużych plików może być wąskie gardło podczas tworzenia zasobu. Pozyskaniu elementów zawartości w zbiorczej lub "Zbiorczego wprowadzania", polega na rozdzieleniu tworzenie zasobów za pomocą procesu przekazywania. Aby użyć masowego wprowadzania podejście, tworzenie manifestu (IngestManifest) dotyczące elementu zawartości i skojarzonych z nim plików. Następnie należy użyć metody przesyłania wybranych przez użytkownika do przekazania skojarzone pliki do kontenera obiektów blob manifestu. Microsoft Azure Media Services Obserwujący kontenera obiektów blob skojarzony z manifestu. Po przekazaniu pliku do kontenera obiektów blob, Microsoft Azure Media Services kończy tworzenie zasobów na podstawie konfiguracji zasobu w manifeście (IngestManifestAsset).

Aby utworzyć nowy IngestManifest, należy wywołać metody Create udostępnianych przez kolekcji IngestManifests na CloudMediaContext. Ta metoda tworzy nowy IngestManifest z manifestu o podanej nazwie.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Utwórz zasoby, które są skojarzone z zbiorczego IngestManifest. Skonfiguruj opcje szyfrowania żądaną na zasobów służy do wprowadzania zbiorczego.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset kojarzy zasób z zbiorczego IngestManifest służy do wprowadzania zbiorczego. Powoduje również skojarzenie AssetFiles, tworzącą każdego zasobu. Aby utworzyć IngestManifestAsset, należy użyć metody Create dla kontekstu serwera.

W poniższym przykładzie pokazano, dodawanie dwóch IngestManifestAssets nowe kojarzące dwa zasoby, które wcześniej utworzone w celu zbiorczego pozyskiwania manifestu. Każdy IngestManifestAsset powoduje również skojarzenie zestaw plików, które są przekazywane za każdy zasób podczas wprowadzania zbiorczego.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Można użyć dowolnego możliwość przekazywania plików zasobów do kontenera magazynu obiektów blob dostarczony przez identyfikator URI aplikacji klienckiej szybkich **IIngestManifest.BlobStorageUriForUpload** właściwości IngestManifest. Jedna usługa zauważalne szybkich przekazywania jest [Aspera na żądanie dla aplikacji Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Można również napisać kod, aby przekazać pliki zasobów, jak pokazano w poniższym przykładzie kodu.

```csharp
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Kod przekazywania plików zasobów przykładowej używane w tym artykule przedstawiono w poniższym przykładzie kodu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Można określić postęp zbiorczego wprowadzania dla wszystkich zasobów skojarzonych z **IngestManifest** przez sondowanie właściwość statystyki **IngestManifest**. Aby można było zaktualizować informacje o postępie, należy użyć nowego **CloudMediaContext** zawsze sondowania właściwości statystyk.

W poniższym przykładzie pokazano sondowania IngestManifest przez jego **identyfikator**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Przekazywanie plików przy użyciu rozszerzenia zestawu .NET SDK
Poniższy przykład przedstawia sposób przekazywania jednego pliku przy użyciu rozszerzenia zestawu .NET SDK. W takim przypadku **CreateFromFile** metoda jest używana, ale jest również dostępna wersja asynchroniczne (**CreateFromFileAsync**). **CreateFromFile** metoda pozwala określić nazwy pliku, opcja szyfrowania i wywołanie zwrotne w celu raportowania postępu przekazywania pliku.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

W poniższym przykładzie wywołuje funkcję UploadFile i określa szyfrowanie magazynu jako opcję tworzenia zasobów.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Teraz, gdy zasób zostały przekazane do usługi Media Services, przejdź do [jak uzyskać procesor multimediów] [ How to Get a Media Processor] artykułu.

[How to Get a Media Processor]: media-services-get-media-processor.md

