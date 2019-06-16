---
title: Przekazywanie plików na konto usługi Media Services przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać zawartości multimedialnej do usługi Media Services, tworząc i przekazując zasoby.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869456"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Przekazywanie plików na konto usługi Media Services przy użyciu platformy .NET 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. **Zasobów** jednostki może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst śledzi i napisów plików (i metadane dotyczące tych plików.)  Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze, na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

Pliki w elementach zawartości są nazywane **plikami elementów zawartości**. **AssetFile** wystąpienia i plik multimedialna znajdują się dwa różne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące plików multimedialnych, a plik multimedialny zawiera zawartość multimedialna.

## <a name="considerations"></a>Zagadnienia do rozważenia

Mają zastosowanie następujące kwestie:
 
 * Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent nie jest dozwolone. Wartość **nazwa** właściwość nie może zawierać żadnych z następujących [procent kodowanie — zastrzeżone znaki](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * "();: @& = + $, /? [] % #". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.
* Długość nazwy nie powinna być dłuższa niż 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.
* Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Podczas tworzenia zasobów, można określić następujące opcje szyfrowania:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. Przy użyciu tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.
  Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji: 
* **CommonEncryption** — Użyj tej opcji, jeśli przekazujesz zawartość, która jest już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (na przykład, Smooth Streaming chronione za pomocą usług PlayReady DRM).
* **EnvelopeEncrypted** — Użyj tej opcji, jeśli przekazujesz HLS zaszyfrowanych z użyciem standardu AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.
* **StorageEncrypted** — zawartości lokalnie przy użyciu algorytmu AES-256-bitowego szyfruje i przekazuje je do usługi Azure Storage gdzie są przechowywane, szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
  
    Usługa Media Services udostępnia szyfrowanie magazynu na dysku dla zasobów, nie w locie podobnie jak Menedżer prawami cyfrowymi (DRM).
  
    Jeśli element zawartości jest szyfrowany w magazynie, należy skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

Jeśli określisz dla swojego elementu zawartości być zaszyfrowany przy użyciu **CommonEncrypted** opcji lub **EnvelopeEncrypted** opcji, należy skojarzyć element zawartości za pomocą **ContentKey**. Aby uzyskać więcej informacji, zobacz [sposób tworzenia ContentKey](media-services-dotnet-create-contentkey.md). 

Jeśli określisz dla swojego elementu zawartości być zaszyfrowany przy użyciu **StorageEncrypted** opcję Media Services SDK dla platformy .NET tworzy **StorageEncrypted** **ContentKey** dla swojego elementu zawartości.

W tym artykule pokazano, jak używać zestawu SDK .NET usługi Media Services, a także Media Services .NET SDK rozszerzenia przekazywanie plików do zasobów usługi Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Przekazać jeden plik przy użyciu zestawu SDK .NET usługi Media Services

W poniższym kodzie użyto .NET, aby przekazać jeden plik. AccessPolicy i lokalizatora są tworzone i niszczone, przy użyciu funkcji przekazywania. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Przekazywanie wielu plików za pomocą zestawu SDK .NET usługi Media Services
Poniższy kod przedstawia sposób tworzenia elementu zawartości i przekazać wiele plików.

Kod wykonuje następujące czynności:

* Tworzy pusty zasobów przy użyciu metody CreateEmptyAsset zdefiniowanej w poprzednim kroku.
* Tworzy **AccessPolicy** wystąpienia, który definiuje uprawnienia i czas trwania dostępu do elementu zawartości.
* Tworzy **lokalizatora** wystąpienia, która zapewnia dostęp do zasobu.
* Tworzy **BlobTransferClient** wystąpienia. Ten typ przedstawia klienta, który działa na obiektach blob platformy Azure. W tym przykładzie klient monitoruje postęp przekazywania. 
* Wylicza pliki w określonym katalogu i tworzy **AssetFile** wystąpienia dla każdego pliku.
* Przekazywanie plików do usługi Media Services za pomocą **UploadAsync** metody. 

> [!NOTE]
> Aby upewnić się, że nie blokują wywołania i pliki są przekazywane w sposób równoległy, należy użyć metody UploadAsync.
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


Podczas przekazywania dużej liczby zasobów, należy rozważyć następujące kwestie:

* Utwórz nową **CloudMediaContext** obiektu na wątek. **CloudMediaContext** klasa nie jest metodą o bezpiecznych wątkach.
* Zwiększ NumberOfConcurrentTransfers z domyślnej wartości 2 do wyższa wartość, np. 5. Ustawienie tej właściwości ma wpływ na wszystkie wystąpienia elementu **CloudMediaContext**. 
* Zachowaj ParallelTransferThreadCount na wartość domyślną równą 10.

## <a id="ingest_in_bulk"></a>Pozyskaniu elementów zawartości w trybie zbiorczym przy użyciu zestawu SDK .NET usługi Media Services
Przekazywanie plików dużych zasobów może być "wąskie gardło" podczas tworzenia zasobu. Pozyskaniu elementów zawartości luzem lub "Wprowadzane zbiorczo", polega na rozdzieleniu tworzenia zasobu z procesu przekazywania. Aby użyć zbiorcze, umożliwiając pozyskiwanie podejście, należy utworzyć manifest (IngestManifest), który opisuje elementu zawartości i skojarzone z nią pliki. Następnie użyj wybranej metody przekazywania, aby przekazać skojarzone pliki do kontenera obiektów blob manifestu. Microsoft Azure Media Services obserwuje skojarzony z manifestem kontener obiektów blob. Po przekazaniu pliku do kontenera obiektów blob, Microsoft Azure Media Services kończy się tworzenie zasobów na podstawie konfiguracji zasobów w manifeście (IngestManifestAsset).

Aby utworzyć nowy IngestManifest, należy wywołać metodę tworzenia udostępnianych przez kolekcji IngestManifests na CloudMediaContext. Ta metoda tworzy nowy IngestManifest o nazwie manifestu, których udzielasz.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Utwórz zasoby, które są skojarzone z zbiorcze IngestManifest. Skonfiguruj opcje szyfrowania odpowiednią dla zasobu, który służy do wprowadzania zbiorczego.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset kojarzy zasobu za pomocą zbiorczego IngestManifest służy do wprowadzania zbiorczego. Powoduje również skojarzenie AssetFiles, tworzącą każdego zasobu. Aby utworzyć IngestManifestAsset, użyj metody tworzenia kontekstu serwera.

W poniższym przykładzie pokazano, dodanie dwóch IngestManifestAssets nowe, które kojarzą dwa zasoby utworzone wcześniej w celu zbiorczego pozyskiwania manifestu. Każdy IngestManifestAsset powoduje również skojarzenie zestawu plików, które zostały załadowane dla każdego zasobu podczas wprowadzania zbiorczego.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Możesz użyć, aby każda aplikacja kliencka o dużej szybkości, możliwość przekazywania plików zasobów do kontenera magazynu obiektów blob identyfikatora URI, dostarczone przez **IIngestManifest.BlobStorageUriForUpload** właściwość IngestManifest. 

Poniższy kod przedstawia sposób używania zestawu SDK platformy .NET do przekazywania plików zasobów.

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

Kod dla przekazywania plików zasobów dla przykładu, używane w tym artykule pokazano w poniższym przykładzie kodu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Można określić postęp zbiorcze wprowadzane dla wszystkich zasobów skojarzonych z **IngestManifest** przez sondowanie statystyki własności **IngestManifest**. Aby zaktualizować informacje o postępie, należy użyć nowego **CloudMediaContext** każdorazowo sondowania właściwość statystyk.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Przekazywanie plików za pomocą rozszerzenia zestawu .NET SDK
Poniższy przykład pokazuje, jak przekazać jeden plik przy użyciu rozszerzenia zestawu .NET SDK. W tym przypadku **CreateFromFile** używana jest metoda, ale dostępna jest także wersja asynchroniczne (**CreateFromFileAsync**). **CreateFromFile** metoda pozwala określić nazwę pliku, opcji szyfrowania i wywołanie zwrotne w celu raportowania postępu przekazywania pliku.

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

Poniższy przykład wywołuje funkcję UploadFile i określa szyfrowanie magazynu jako opcję tworzenia zasobów.  

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
Teraz, gdy element zawartości został przekazany do usługi Media Services, przejdź do [sposobu uzyskania procesor multimediów] [ How to Get a Media Processor] artykułu.

[How to Get a Media Processor]: media-services-get-media-processor.md

