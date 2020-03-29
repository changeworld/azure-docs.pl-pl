---
title: Przekazywanie plików na konto programu Media Services przy użyciu platformy .NET | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać zawartość multimedialną do usługi Media Services, tworząc i przekazując zasoby.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 03b9995eab503ac1fcd4615882419dde31d4f8bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64869456"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Przekazywanie plików na konto usługi Media Services na platformie .NET 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. Encja **Zasobów** może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki podpisów kodowanych (oraz metadane dotyczące tych plików).  Po przesłaniu plików zawartość jest bezpiecznie przechowywana w chmurze w celu dalszego przetwarzania i przesyłania strumieniowego.

Pliki w elementach zawartości są nazywane **plikami elementów zawartości**. **AssetFile wystąpienie** i rzeczywisty plik multimedialny są dwa odrębne obiekty. AssetFile wystąpienie zawiera metadane dotyczące pliku multimedialnego, podczas gdy plik multimedialny zawiera rzeczywistą zawartość multimedialną.

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia:
 
 * Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL zawartości przesyłanej strumieniowo (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters).) Z tego powodu kodowanie procentowe jest niedozwolone. Wartość **Name** właściwości nie może mieć żadnego z następujących [znaków zarezerwowanych do kodowania procentowego:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;@&=+$,/?%#[]". Ponadto może istnieć tylko jeden '.' dla rozszerzenia nazwy pliku.
* Długość nazwy nie powinna być większa niż 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.
* Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Podczas tworzenia zasobów można określić następujące opcje szyfrowania:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. Podczas korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania lub przechowywania w magazynie.
  Jeśli planujesz dostarczyć MP4 przy użyciu pobierania progresywnego, użyj tej opcji: 
* **CommonEncryption** — użyj tej opcji, jeśli przesyłasz zawartość, która została już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub funkcji PlayReady DRM (na przykład płynne przesyłanie strumieniowe chronione za pomocą programu PlayReady DRM).
* **EnvelopeEncrypted** — użyj tej opcji, jeśli przesyłasz HLS zaszyfrowane za pomocą AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.
* **StorageEncrypted** — szyfruje wyczyszczaną zawartość lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazuje ją do usługi Azure Storage, gdzie jest przechowywana zaszyfrowana w spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
  
    Usługi Media Services zapewniają szyfrowanie magazynu na dysku dla zasobów, a nie za przekrojowe, takie jak Digital Rights Manager (DRM).
  
    Jeśli element zawartości jest szyfrowany w magazynie, musisz skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-dotnet-configure-asset-delivery-policy.md).

Jeśli określisz, czy zasób ma być zaszyfrowany za pomocą opcji **CommonEncrypted** lub **EnvelopeEncrypted,** musisz skojarzyć zasób z **contentkeyem**. Aby uzyskać więcej informacji, zobacz [Jak utworzyć contentkey](media-services-dotnet-create-contentkey.md). 

Jeśli określisz, czy zasób ma być zaszyfrowany za pomocą opcji **StorageEncrypted,** zestaw SDK usługi Media Services dla platformy .NET utworzy **klucz zawartości zaszyfrowanej pamięci StorageEncrypted** **ContentKey** dla zasobu.

W tym artykule pokazano, jak używać sdk programu Media Services .NET oraz rozszerzeń SDK programu Media Services .NET do przekazywania plików do zasobu usługi Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Przekazywanie pojedynczego pliku za pomocą pliku Media Services .NET SDK

Poniższy kod używa platformy .NET do przekazania pojedynczego pliku. AccessPolicy i Lokalizator są tworzone i niszczone przez upload funkcji. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Przekazywanie wielu plików za pomocą zestawu SDK usługi Media Services .NET
Poniższy kod pokazuje, jak utworzyć zasób i przekazać wiele plików.

Kod wykonuje następujące czynności:

* Tworzy pusty zasób przy użyciu Metody CreateEmptyAsset zdefiniowanej w poprzednim kroku.
* Tworzy **AccessPolicy** wystąpienie, które definiuje uprawnienia i czas trwania dostępu do zasobu.
* Tworzy wystąpienie **lokalizatora,** które zapewnia dostęp do zasobu.
* Tworzy **wystąpienie BlobTransferClient.** Ten typ reprezentuje klienta, który działa na obiektach blob platformy Azure. W tym przykładzie klient monitoruje postęp przekazywania. 
* Wylicza pliki w określonym katalogu i tworzy **wystąpienie AssetFile** dla każdego pliku.
* Przekazuje pliki do usługi Media Services przy użyciu **UploadAsync** metody. 

> [!NOTE]
> Użyj UploadAsync metody, aby upewnić się, że wywołania nie są blokowane i pliki są przekazywane równolegle.
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

                // It is recommended to validate AssetFiles before upload. 
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


Przesyłając dużą liczbę zasobów, należy wziąć pod uwagę następujące kwestie:

* Utwórz nowy obiekt **CloudMediaContext** na wątek. **CloudMediaContext** Klasa nie jest bezpieczne dla wątków.
* Zwiększ NumberOfConcurrentTransfers z domyślnej wartości 2 do wyższej wartości, takiej jak 5. Ustawienie tej właściwości ma wpływ na wszystkie wystąpienia **CloudMediaContext**. 
* Zachowaj ParallelTransferThreadCount na domyślnej wartości 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Zbiorcze pozyskiwania zasobów przy użyciu sdk usługi Media Services .NET
Przekazywanie dużych plików zasobów może być wąskim gardłem podczas tworzenia zasobów. Pojmowanie zasobów zbiorczo lub zbiorczo pochłonie", obejmuje oddzielenie tworzenia zasobów od procesu przekazywania. Aby użyć podejścia pozyskiwania zbiorczego, należy utworzyć manifest (IngestManifest), który opisuje zasób i skojarzone z nim pliki. Następnie użyj metody przekazywania do przekazywania skojarzonych plików do kontenera obiektów blob manifestu. Usługi Microsoft Azure Media Services obserwuje kontener obiektów blob skojarzony z manifestem. Po przekazaniu pliku do kontenera obiektu blob usługa Microsoft Azure Media Services kończy tworzenie zasobu na podstawie konfiguracji zasobu w manifeście (IngestManifestAsset).

Aby utworzyć nowy IngestManifest, wywołać Create metody udostępniane przez IngestManifests kolekcji na CloudMediaContext. Ta metoda tworzy nowy IngestManifest o nazwie manifestu, który podasz.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Utwórz zasoby skojarzone z zbiorczym ingestmanifest. Skonfiguruj żądane opcje szyfrowania dla zasobu do zbiorczego pozyskiwania.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset kojarzy zasób z zbiorczym ingestmanifest do zbiorczego pozyskiwania. Kojarzy również AssetFiles, który składa się na każdy zasób. Aby utworzyć zestaw IngestManifestAsset, należy użyć metody Utwórz w kontekście serwera.

W poniższym przykładzie pokazano dodanie dwóch nowych ingestmanifestAssets, które kojarzą dwa zasoby wcześniej utworzone do manifestu pozyskiwania zbiorczego. Każdy zestaw IngestManifestAsset kojarzy również zestaw plików, które są przekazywane dla każdego zasobu podczas zbiorczego pozyskiwania.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Można użyć dowolnej aplikacji klienckiej o dużej szybkości, która umożliwia przekazywanie plików zasobów do identyfikatora URI kontenera magazynu obiektów blob dostarczonej przez właściwość **IIngestManifest.BlobStorageUriForUpload** właściwości ingestmanifest. 

Poniższy kod pokazuje, jak używać pliku .NET SDK do przekazywania plików zasobów.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

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

Kod do przekazywania plików zasobów dla przykładu użytego w tym artykule jest pokazany w poniższym przykładzie kodu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Można określić postęp pozyskiwania zbiorczego dla wszystkich zasobów skojarzonych z **IngestManifest,** sondując właściwość Statystyka **IngestManifest**. Aby zaktualizować informacje o postępie, należy użyć nowego **CloudMediaContext** za każdym razem, gdy sondujesz właściwość Statistics.

Poniższy przykład pokazuje sondowanie IngestManifest przez jego **identyfikator**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Przekazywanie plików przy użyciu rozszerzeń SDK .NET
W poniższym przykładzie pokazano, jak przekazać pojedynczy plik przy użyciu rozszerzeń SDK .NET. W tym przypadku używana jest metoda **CreateFromFile,** ale dostępna jest również wersja asynchroniczna (**CreateFromFileAsync**). **CreateFromFile** Metoda pozwala określić nazwę pliku, opcja szyfrowania i wywołania zwrotnego w celu raportowania postępu przekazywania pliku.

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

## <a name="next-steps"></a>Następne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Teraz, gdy został przekazany zasób do usługi Media Services, przejdź do artykułu [Jak uzyskać procesor multimediów.][How to Get a Media Processor]

[How to Get a Media Processor]: media-services-get-media-processor.md

