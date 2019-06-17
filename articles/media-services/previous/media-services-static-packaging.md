---
title: Korzystanie z funkcji pakowania multimediów platformy Azure do wykonywania zadań statyczne tworzenie pakietów | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono różne zadania, które są realizowane za pomocą funkcji pakowania multimediów platformy Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0582628e-a525-4a78-90ac-9f7fc1cd909f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8665f6daa698f2e885f1fe768ad6b9c87dbbe164
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074520"
---
# <a name="using-azure-media-packager-to-accomplish-static-packaging-tasks"></a>Korzystanie z funkcji pakowania multimediów platformy Azure do wykonywania zadań statyczne tworzenie pakietów  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)


## <a name="overview"></a>Omówienie

Aby można było dostarczać wideo za pośrednictwem Internetu, trzeba skompresować nośniki. Pliki wideo cyfrowe są duże i może być zbyt duża, aby dostarczyć za pośrednictwem Internetu lub dla klientów urządzeń, aby wyświetlać się poprawnie. Kodowanie jest procesem kompresowania audio i wideo, dzięki czemu klienci mogą wyświetlać multimediów. Po zakodowaniu filmu wideo, mogą być umieszczane w kontenerach inny plik. Proces wprowadzania do nośnika zakodowany w kontenerze nosi nazwę pakietu. Na przykład można wykonać pliku MP4 i je przekształcić w zawartości Smooth Streaming i HLS, korzystając z funkcji pakowania multimediów platformy Azure. 

Usługa Media Services obsługuje pakowania statycznych i dynamicznych. Korzystając z statyczne tworzenie pakietów, należy utworzyć kopię zawartości w każdym wymagane przez klientów. Za pomocą funkcji dynamicznego tworzenia pakietów, wszystko co potrzebne jest utworzenie element zawartości zawierający zestaw plików MP4 lub Smooth Streaming adaptacyjną szybkością transmisji bitów. Następnie na podstawie formatu określonego w żądaniu fragmentu ani manifestu przesyłania strumieniowego na żądanie serwera zapewnia, że użytkownicy strumień jest dostarczany w protokole, które wybrali. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

> [!NOTE]
> Zalecane jest użycie [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).
> 
> 

Jednak istnieją pewne scenariusze, które wymagają statyczne tworzenie pakietów: 

* Sprawdzanie poprawności adaptacyjną szybkością transmisji bitów każdego pliku MP4 z zakodowanych przy użyciu koderów zewnętrzne (na przykład przy użyciu koderów innych firm).

Statyczne tworzenie pakietów umożliwia również wykonywanie następujących zadań: Jednak zalecane jest korzystanie z szyfrowania dynamicznego.

* Przy użyciu statycznego szyfrowania do ochrony Smooth i MPEG DASH za pomocą technologii PlayReady
* Przy użyciu statycznego szyfrowania do ochrony HLSv3 przy użyciu algorytmu AES-128
* Przy użyciu statycznego szyfrowania do ochrony HLSv3 przy użyciu technologii PlayReady

## <a name="validating-adaptive-bitrate-mp4s-encoded-with-external-encoders"></a>Sprawdzanie poprawności adaptacyjne każdego pliku MP4 z szybkości transmisji bitów zakodowanych przy użyciu koderów zewnętrznych
Jeśli chcesz użyć zestawu plików MP4 adaptacyjną szybkością transmisji bitów (bitów), które nie zostały zakodowane przy użyciu koderów Media Services, należy sprawdzić, czy pliki przed dalszym przetwarzaniem. Z funkcji pakowania usługi multimediów można sprawdzać poprawność element zawartości zawierający zestaw plików MP4 i sprawdź, czy zasób można spakować do Smooth Streaming i HLS. Jeśli zadanie sprawdzania poprawności nie powiedzie się, zadanie, które przetwarza zadanie zostanie ukończone z powodu błędu. Plik XML, który definiuje ustawienie wstępne zadania sprawdzania poprawności można znaleźć w [zadań wstępnie zdefiniowanych dla funkcji pakowania multimediów platformy Azure](https://msdn.microsoft.com/library/azure/hh973635.aspx) artykułu.

> [!NOTE]
> Aby uniknąć problemów w czasie wykonywania, należy użyć usługi Media Encoder Standard do produkcji lub z funkcji pakowania usługi multimediów do weryfikacji zawartości. Jeśli serwer przesyłania strumieniowego na żądanie nie jest w stanie przeanalizować plików źródłowych w czasie wykonywania, pojawi się błąd HTTP 1.1 "415 Nieobsługiwany typ nośnika." Wielokrotnie powoduje serwerowi nie można przeanalizować plików źródłowych ma wpływ na wydajność serwera przesyłania strumieniowego na żądanie i może zmniejszyć przepustowość dostępną dla obsługująca innych żądań. Usługa Azure Media Services oferuje poziomu usług umowy (SLA) w usługach przesyłania strumieniowego na żądanie; Jednak ta umowa SLA nie mogą być uwzględniane, jeśli serwer jest używane w sposób opisany powyżej.
> 
> 

W tej sekcji przedstawiono sposób przetwarzania zadania sprawdzania poprawności. Pokazano również, jak wyświetlić stan i zadania, które kończy się za pomocą JobStatus.Error komunikat o błędzie.

Aby sprawdzić poprawność plików MP4 z funkcji pakowania multimediów usługi, należy utworzyć plik manifestu (ISM) i przekazać go razem z plików źródłowych do konta usługi Media Services. Poniższe przykładowe plik .ism jest generowany przez Media Encoder Standard. Nazwy plików jest rozróżniana wielkość liter. Ponadto upewnij się, że tekst w plik .ism jest zakodowany w formacie UTF-8.

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
    <!-- Tells the server that these input files are MP4s – specific to Dynamic Packaging -->
        <meta name="formats" content="mp4" /> 
      </head>
      <body>
        <switch>
          <video src="BigBuckBunny_1000.mp4" />
          <video src="BigBuckBunny_1500.mp4" />
          <video src="BigBuckBunny_2250.mp4" />
          <video src="BigBuckBunny_3400.mp4" />
          <video src="BigBuckBunny_400.mp4" />
          <video src="BigBuckBunny_650.mp4" />
          <audio src="BigBuckBunny_400.mp4" />
        </switch>
      </body>
    </smil>
```

Po utworzeniu adaptacyjną szybkością transmisji bitów zestawu w formacie MP4, możesz korzystać z dynamicznego tworzenia pakietów. Dynamiczne tworzenie pakietów umożliwia dostarczanie strumieni w określonym protokole bez dalszego pakowania. Aby uzyskać więcej informacji, zobacz [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

Poniższy przykład kodu używa rozszerzenia SDK .NET usługi Azure Media Services.  Upewnij się zaktualizować kod, aby wskazać folder, w którym znajdują się Twoje pliki wejściowe w formacie MP4 i plik .ism. A także gdzie znajduje się plik MediaPackager_ValidateTask.xml. Ten plik XML jest zdefiniowany w [zadanie ustawienie wstępne dla funkcji pakowania multimediów platformy Azure](https://msdn.microsoft.com/library/azure/hh973635.aspx) artykułu.

```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Xml.Linq;

    namespace MediaServicesStaticPackaging
    {
        class Program
        {
            private static readonly string _mediaFiles =
                Path.GetFullPath(@"../..\Media");

            // The MultibitrateMP4Files folder should also
            // contain the .ism manifest file.
            private static readonly string _multibitrateMP4s =
                Path.Combine(_mediaFiles, @"MultibitrateMP4Files");

            // XML Configuration files path.
            private static readonly string _configurationXMLFiles = @"../..\Configurations";

            private static MediaServicesCredentials _cachedCredentials = null;
            private static CloudMediaContext _context = null;

            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials =
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                // Ingest a set of multibitrate MP4s.
                //
                // Use the SDK extension method to create a new asset by 
                // uploading files from a local directory.
                IAsset multibitrateMP4sAsset = _context.Assets.CreateFromFolder(
                    _multibitrateMP4s,
                    AssetCreationOptions.None,
                    (af, p) =>
                    {
                        Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
                    });

                // Use Azure Media Packager to validate the files.
                IAsset validatedMP4s =
                    ValidateMultibitrateMP4s(multibitrateMP4sAsset);

                // Publish the asset.
                _context.Locators.Create(
                    LocatorType.OnDemandOrigin,
                    validatedMP4s,
                    AccessPermissions.Read,
                    TimeSpan.FromDays(30));

                                     // Get the streaming URLs.
                Console.WriteLine("Smooth Streaming URL:");
                Console.WriteLine(validatedMP4s.GetSmoothStreamingUri().ToString());
                Console.WriteLine("MPEG DASH URL:");
                Console.WriteLine(validatedMP4s.GetMpegDashUri().ToString());
                Console.WriteLine("HLS URL:");
                Console.WriteLine(validatedMP4s.GetHlsUri().ToString());
            }

            public static IAsset ValidateMultibitrateMP4s(IAsset multibitrateMP4sAsset)
            {
                // Set .ism as a primary file 
                // in a multibitrate MP4 set.
                SetISMFileAsPrimary(multibitrateMP4sAsset);

                // Create a new job.
                IJob job = _context.Jobs.Create("MP4 validation and conversion to Smooth Stream job.");

                // Read the task configuration data into a string. 
                string configMp4Validation = File.ReadAllText(Path.Combine(
                        _configurationXMLFiles,
                        "MediaPackager_ValidateTask.xml"));

                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Create a task with the conversion details, using the configuration data. 
                ITask task = job.Tasks.AddNew("Mp4 Validation Task",
                    processor,
                    configMp4Validation,
                    TaskOptions.None);

                // Specify the input asset to be validated.
                task.InputAssets.Add(multibitrateMP4sAsset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted). 
                task.OutputAssets.AddNew("Validated output asset",
                        AssetCreationOptions.None);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // If the validation task fails and job completes with JobState.Error,
                // display the error message and throw an exception.
                if (job.State == JobState.Error)
                {
                    Console.WriteLine("  Job ID: " + job.Id);
                    Console.WriteLine("  Name: " + job.Name);
                    Console.WriteLine("  State: " + job.State);

                    foreach (var jobTask in job.Tasks)
                    {
                        Console.WriteLine("  Task Id: " + jobTask.Id);
                        Console.WriteLine("  Name: " + jobTask.Name);
                        Console.WriteLine("  Progress: " + jobTask.Progress);
                        Console.WriteLine("  Configuration: " + jobTask.Configuration);
                        Console.WriteLine("  Running time: " + jobTask.RunningDuration);
                        if (jobTask.ErrorDetails != null)
                        {
                            foreach (var errordetail in jobTask.ErrorDetails)
                            {

                                Console.WriteLine("  Error Message:" + errordetail.Message);
                                Console.WriteLine("  Error Code:" + errordetail.Code);
                            }
                        }
                    }
                    throw new Exception("The specified multi-bitrate MP4 set is not valid.");
                }


                return job.OutputMediaAssets[0];
            }

            static void SetISMFileAsPrimary(IAsset asset)
            {
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase));

                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
            }
        }
    }
```

## <a name="using-static-encryption-to-protect-your-smooth-and-mpeg-dash-with-playready"></a>Przy użyciu statycznego szyfrowania do ochrony Twojej Smooth i MPEG DASH za pomocą technologii PlayReady
Jeśli chcesz chronić zawartość przy użyciu technologii PlayReady, masz do wyboru przy użyciu [szyfrowania dynamicznego](media-services-protect-with-playready-widevine.md) (zalecana opcja) lub szyfrowania statyczne (zgodnie z opisem w tej sekcji).

W przykładzie w tej sekcji koduje pliku mezzanine (w tym MP4 wielkości liter) w plików MP4. Go następnie pakiety każdego pliku MP4 do Smooth Streaming, a następnie szyfruje Smooth Streaming przy użyciu technologii PlayReady. W rezultacie jesteś w stanie przesyłać strumieniowo Smooth Streaming lub MPEG DASH.

Usługa Media Services udostępnia teraz usługę dostarczania licencji PlayReady firmy Microsoft. W przykładzie w tym artykule przedstawiono sposób konfigurowania usługi dostarczania licencji PlayReady usługi Media Services (zobacz metoda ConfigureLicenseDeliveryService zdefiniowane w poniższym kodzie). Aby uzyskać więcej informacji na temat usługi dostarczania licencji PlayReady usługi Media Services, zobacz [za pomocą szyfrowania dynamicznego PlayReady i usługi dostarczania licencji](media-services-protect-with-playready-widevine.md).

> [!NOTE]
> Aby dostarczyć MPEG DASH szyfrowane za pomocą usług PlayReady, upewnij się, że opcje CENC przez ustawienie właściwości useSencBox i adjustSubSamples (opisane w [zadanie ustawienie wstępne dla obsługi funkcji](https://msdn.microsoft.com/library/azure/hh973610.aspx) artykuł) na wartość true.  
> 
> 

Upewnij się zaktualizować następujący kod, aby wskazać folder, w którym znajduje się plik wejściowy w formacie MP4.

A także gdzie znajdują się pliki MediaPackager_MP4ToSmooth.xml i MediaEncryptor_PlayReadyProtection.xml. MediaPackager_MP4ToSmooth.xml jest zdefiniowany w [zadanie ustawienie wstępne dla funkcji pakowania multimediów platformy Azure](https://msdn.microsoft.com/library/azure/hh973635.aspx) i MediaEncryptor_PlayReadyProtection.xml jest zdefiniowany w [zadanie ustawienie wstępne dla obsługi funkcji](https://msdn.microsoft.com/library/azure/hh973610.aspx) artykułu. 

W przykładzie zdefiniowano metodę UpdatePlayReadyConfigurationXMLFile, która umożliwia dynamiczne aktualizowanie pliku MediaEncryptor_PlayReadyProtection.xml. W przypadku klucza inicjatora dostępne CommonEncryption.GeneratePlayReadyContentKey metoda służy do generowania klucza zawartości na podstawie keySeedValue i wartości Identyfikator klucza.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Xml.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

    namespace PlayReadyStaticEncryptAndKeyDeliverySvc
    {
        class Program
        {

            private static readonly string _mediaFiles =
                Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
                Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

            // XML Configuration files path.
            private static readonly string _configurationXMLFiles = @"../..\Configurations\";
          
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials =
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                // Encoding and encrypting assets //////////////////////
                // Load a single MP4 file.
                IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);

                // Encode an MP4 file to a set of multibitrate MP4s.
                // Then, package a set of MP4s to clear Smooth Streaming.
                IAsset clearSmoothStreamAsset =
                    ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);

                // Create a common encryption content key that is used 
                // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
                //    that is used for encryption.
                // b) to configure the license delivery service and 
                //
                Guid keyId;
                byte[] contentKey;

                IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);

                // The content key authorization policy must be configured by you 
                // and met by the client in order for the PlayReady license
                // to be delivered to the client. 
                // In this example the Media Services PlayReady license delivery service is used.
                ConfigureLicenseDeliveryService(key);

                // Get the Media Services PlayReady license delivery URL.
                // This URL will be assigned to the licenseAcquisitionUrl property 
                // of the MediaEncryptor_PlayReadyProtection.xml file.
                Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

                // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
                UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);


                // Encrypt your clear Smooth Streaming to Smooth Streaming with PlayReady.
                IAsset outputAsset = CreateSmoothStreamEncryptedWithPlayReady(clearSmoothStreamAsset);


                // You can use the http://smf.cloudapp.net/healthmonitor player 
                // to test the smoothStreamURL URL.
                string smoothStreamURL = outputAsset.GetSmoothStreamingUri().ToString();
                Console.WriteLine("Smooth Streaming URL:");
                Console.WriteLine(smoothStreamURL);

                // You can use the http://dashif.org/reference/players/javascript/ player 
                // to test the dashURL URL.
                string dashURL = outputAsset.GetMpegDashUri().ToString();
                Console.WriteLine("MPEG DASH URL:");
                Console.WriteLine(dashURL);
            }

            /// <summary>
            /// Creates a job with 2 tasks: 
            /// 1 task - encodes a single MP4 to multibitrate MP4s,
            /// 2 task - packages MP4s to Smooth Streaming.
            /// </summary>
            /// <returns>The output asset.</returns>
            public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
            {
                // Create a new job.
                IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");

                // Add task 1 - Encode single MP4 into multibitrate MP4s.
                IAsset MP4sAsset = EncodeMP4IntoMultibitrateMP4sTask(job, asset);
                // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Stream.
                IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // Get the output asset that contains the Smooth Streaming asset.
                return job.OutputMediaAssets[1];
            }

            /// <summary>
            /// Encrypts Smooth Stream with PlayReady.
            /// Then creates a Smooth Streaming Url.
            /// </summary>
            /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
            /// <returns>The output asset.</returns>
            public static IAsset CreateSmoothStreamEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
            {
                // Create a job.
                IJob job = _context.Jobs.Create("Encrypt to PlayReady Smooth Streaming.");

                // Add task 1 - Encrypt Smooth Streaming with PlayReady 
                IAsset encryptedSmoothAsset =
                    EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // The OutputMediaAssets[0] contains the desired asset.
                _context.Locators.Create(
                    LocatorType.OnDemandOrigin,
                    job.OutputMediaAssets[0],
                    AccessPermissions.Read,
                    TimeSpan.FromDays(30));

                return job.OutputMediaAssets[0];
            }

            /// <summary>
            /// Create a common encryption content key that is used 
            /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
            /// that is used for encryption.
            /// </summary>
            /// <param name="keyId"></param>
            /// <param name="contentKey"></param>
            /// <returns></returns>
            public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
            {
                keyId = Guid.NewGuid();
                contentKey = GetRandomBuffer(16);

                IContentKey key = _context.ContentKeys.Create(
                                        keyId,
                                        contentKey,
                                        "ContentKey",
                                        ContentKeyType.CommonEncryption);

                return key;
            }

            /// <summary>
            /// Update your configuration .xml file dynamically.
            /// </summary>
            public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
            {
                string xmlFileName = Path.Combine(_configurationXMLFiles,
                                            @"MediaEncryptor_PlayReadyProtection.xml");

                XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";

                // Prepare the encryption task template
                XDocument doc = XDocument.Load(xmlFileName);

                var licenseAcquisitionUrlEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
                        .FirstOrDefault();
                var contentKeyEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "contentKey")
                        .FirstOrDefault();
                var keyIdEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "keyId")
                        .FirstOrDefault();

                // Update the "value" property.
                if (licenseAcquisitionUrlEl != null)
                    licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());

                if (contentKeyEl != null)
                    contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));

                if (keyIdEl != null)
                    keyIdEl.Attribute("value").SetValue(keyId);

                doc.Save(xmlFileName);
            }

            /// <summary>
            /// Uploads a single file.
            /// </summary>
            /// <param name="fileDir">The location of the files.</param>
            /// <param name="assetCreationOptions">
            ///  You can specify the following encryption options for the AssetCreationOptions.
            ///      None:  no encryption.  
            ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
            ///        before it is uploaded to Azure storage. 
            ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
            ///        For example, a set of files that are already PlayReady encrypted. 
            ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
            ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
            ///     </param>
            /// <returns>Returns an asset that contains a single file.</returns>
            /// </summary>
            /// <returns></returns>
            private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
            {
                // Use the SDK extension method to create a new asset by 
                // uploading a mezzanine file from a local path.
                IAsset asset = _context.Assets.CreateFromFile(
                    fileDir,
                    assetCreationOptions,
                    (af, p) =>
                    {
                        Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
                    });

                return asset;
            }

            /// <summary>
            /// Creates a task to encode to Adaptive Bitrate. 
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset EncodeMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Media Encoder Standard.
                IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.MediaEncoderStandard);

                ITask adaptiveBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
                   encoder,
                   "Adaptive Streaming",
                   TaskOptions.None);

                // Specify the input Asset
                adaptiveBitrateTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset abrAsset = adaptiveBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
                                        AssetCreationOptions.None);

                return abrAsset;
            }

            /// <summary>
            /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Azure Media Packager does not accept string presets, so load xml configuration
                string smoothConfig = File.ReadAllText(Path.Combine(
                            _configurationXMLFiles,
                            "MediaPackager_MP4toSmooth.xml"));

                // Create a new Task to convert adaptive bitrate to Smooth Streaming.
                ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
                   packager,
                   smoothConfig,
                   TaskOptions.None);

                // Specify the input Asset, which is the output Asset from the first task
                smoothStreamingTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset smoothOutputAsset =
                    smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Stream",
                        AssetCreationOptions.None);

                return smoothOutputAsset;
            }


            /// <summary>
            /// Creates a task to encrypt Smooth Streaming with PlayReady.
            /// Note: To deliver DASH, make sure to set the useSencBox and adjustSubSamples 
            /// configuration properties to true. 
            /// In this example, MediaEncryptor_PlayReadyProtection.xml contains configuration.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
                IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaEncryptor);

                // Read the configuration XML.
                //
                // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
                // is using keySeedValue. It is recommended that you do this only for testing 
                // and not in production. For more information, see 
                // https://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
                //
                string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
                                            @"MediaEncryptor_PlayReadyProtection.xml"));

                ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
                   playreadyProcessor,
                   configPlayReady,
                   TaskOptions.ProtectedConfiguration);

                playreadyTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
                IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
                                                "PlayReady Smooth Streaming",
                                                AssetCreationOptions.CommonEncryptionProtected);

                return playreadyAsset;
            }

            /// <summary>
            /// Configures authorization policy for the content key. 
            /// </summary>
            /// <param name="contentKey">The content key.</param>
            static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
            {
                // Create ContentKeyAuthorizationPolicy with Open restrictions 
                // and create authorization policy          

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction 
                    { 
                        Name = "Open", 
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                        Requirements = null
                    }
                };

                // Configure PlayReady license template.
                string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                IContentKeyAuthorizationPolicyOption policyOption =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, newLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with no restrictions").
                            Result;

                contentKeyAuthorizationPolicy.Options.Add(policyOption);

                // Associate the content key authorization policy with the content key.
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;
            }

            static private string ConfigurePlayReadyLicenseTemplate()
            {
                // The following code configures PlayReady License Template using .NET classes
                // and returns the XML string.

                PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
                PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

                responseTemplate.LicenseTemplates.Add(licenseTemplate);

                return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
            }

            static private byte[] GetRandomBuffer(int length)
            {
                var returnValue = new byte[length];

                using (var rng =
                    new System.Security.Cryptography.RNGCryptoServiceProvider())
                {
                    rng.GetBytes(returnValue);
                }

                return returnValue;
            }
        }
    }
```

## <a name="using-static-encryption-to-protect-hlsv3-with-aes-128"></a>Przy użyciu statycznego szyfrowania do ochrony HLSv3 przy użyciu algorytmu AES-128
Jeśli chcesz zaszyfrować przez moduł HLS z algorytmu AES-128, masz do wyboru przy użyciu statycznej lub dynamicznej szyfrowania (opcja zalecana) (jak pokazano w tej sekcji). Jeśli zdecydujesz się użyć dynamicznego szyfrowania, zobacz [za pomocą dynamicznego szyfrowania AES-128 i usługę dostarczania kluczy](media-services-protect-with-aes128.md).

> [!NOTE]
> Aby można było przekonwertować zawartości HLS, użytkownik musi najpierw przekonwertować/Zakoduj swoją zawartość do Smooth Streaming.
> Dla protokołu HLS do zaszyfrowane przy użyciu standardu AES upewnij się również ustawić następujące właściwości w pliku MediaPackager_SmoothToHLS.xml: Ustaw właściwość Szyfruj na wartość true, ustaw wartość klucza i wartości keyuri, aby wskazywały serwer uwierzytelniania/autoryzacji.
> Usługi Media Services tworzy plik klucza i umieszcza je w kontenerze zasobów. Należy skopiować plik /asset-containerguid/\*.key z serwerem (lub Utwórz własny plik klucza), a następnie usuń plik \*.key z kontenera zasobów.
> 
> 

W przykładzie w tej sekcji koduje pliku mezzanine (w tym przypadku MP4) w metodę pliki MP4, a następnie pakiety każdego pliku MP4 do Smooth Streaming. Go następnie pakiety Smooth Streaming na HTTP Live Streaming (HLS) zaszyfrowany przy użyciu szyfrowania strumienia 128-bitowego Advanced Encryption Standard (AES). Upewnij się zaktualizować następujący kod, aby wskazać folder, w którym znajduje się plik wejściowy w formacie MP4. A także gdzie znajdują się pliki konfiguracji MediaPackager_MP4ToSmooth.xml i MediaPackager_SmoothToHLS.xml. Można znaleźć definicji dla tych plików w [zadanie ustawienie wstępne dla funkcji pakowania multimediów platformy Azure](https://msdn.microsoft.com/library/azure/hh973635.aspx) artykułu.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Xml.Linq;

    namespace MediaServicesContentProtection
    {
        class Program
        {
            // Paths to support files (within the above base path). You can use 
            // the provided sample media files from the "SupportFiles" folder, or 
            // provide paths to your own media files below to run these samples.

            private static readonly string _mediaFiles =
                Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
                Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");

            // XML Configuration files path.
            private static readonly string _configurationXMLFiles = @"../..\Configurations\";

            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials =
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                // Encoding and encrypting assets //////////////////////

                // Load an MP4 file.
                IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);

                // Encode an MP4 file to a set of multibitrate MP4s.
                // Then, package a set of MP4s to clear Smooth Streaming.
                IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);

                // Create HLS encrypted with AES.
                IAsset HLSEncryptedWithAESAsset = CreateHLSEncryptedWithAES(clearSmoothStreamAsset);

                // You can use the following player to test the HLS with AES stream.
                // https://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614 
                string hlsWithAESURL = HLSEncryptedWithAESAsset.GetHlsUri().ToString();
                Console.WriteLine("HLS with AES URL:");
                Console.WriteLine(hlsWithAESURL);
            }


            /// <summary>
            /// Creates a job with 2 tasks: 
            /// 1 task - encodes a single MP4 to multibitrate MP4s,
            /// 2 task - packages MP4s to Smooth Streaming.
            /// </summary>
            /// <returns>The output asset.</returns>
            public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
            {
                // Create a new job.
                IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");

                // Add task 1 - Encode single MP4 into multibitrate MP4s.
                IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
                // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
                IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // Get the output asset that contains Smooth Streaming.
                return job.OutputMediaAssets[1];
            }

            /// <summary>
            /// Encrypts an HLS with AES-128.
            /// </summary>
            /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
            /// <returns>The output asset.</returns>
            public static IAsset CreateHLSEncryptedWithAES(IAsset clearSmoothStreamAsset)
            {
                IJob job = _context.Jobs.Create("Encrypt to HLS with AES.");

                // Add task 1 - Package clear Smooth Streaming to HLS with AES.
                PackageSmoothStreamToHLS(job, clearSmoothStreamAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // The OutputMediaAssets[0] contains the desired asset.
                _context.Locators.Create(
                    LocatorType.OnDemandOrigin,
                    job.OutputMediaAssets[0],
                    AccessPermissions.Read,
                    TimeSpan.FromDays(30));

                return job.OutputMediaAssets[0];
            }

            /// <summary>
            /// Uploads a single file.
            /// </summary>
            /// <param name="fileDir">The location of the files.</param>
            /// <param name="assetCreationOptions">
            ///  You can specify the following encryption options for the AssetCreationOptions.
            ///      None:  no encryption.  
            ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
            ///        before it is uploaded to Azure storage. 
            ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
            ///        For example, a set of files that are already PlayReady encrypted. 
            ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
            ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
            ///     </param>
            /// <returns>Returns an asset that contains a single file.</returns>
            /// </summary>
            /// <returns></returns>
            private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
            {
                // Use the SDK extension method to create a new asset by 
                // uploading a mezzanine file from a local path.
                IAsset asset = _context.Assets.CreateFromFile(
                    fileDir,
                    assetCreationOptions,
                    (af, p) =>
                    {
                        Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
                    });

                return asset;
            }

            /// <summary>
            /// Creates a task to encode to Adaptive Bitrate. 
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Media Encoder Standard.
                IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.MediaEncoderStandard);

                ITask adaptiveBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
                   encoder,
                   "Adaptive Streaming",
                   TaskOptions.None);

                // Specify the input Asset
                adaptiveBitrateTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset abrAsset = adaptiveBitrateTask.OutputAssets.AddNew("Multibitrate MP4s", 
                                        AssetCreationOptions.None);

                return abrAsset;
            }

            /// <summary>
            /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Azure Media Packager does not accept string presets, so load xml configuration
                string smoothConfig = File.ReadAllText(Path.Combine(
                            _configurationXMLFiles, 
                            "MediaPackager_MP4toSmooth.xml"));

                // Create a new Task to convert adaptive bitrate to Smooth Streaming.
                ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
                   packager,
                   smoothConfig,
                   TaskOptions.None);

                // Specify the input Asset, which is the output Asset from the first task
                smoothStreamingTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset smoothOutputAsset = 
                    smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming", 
                        AssetCreationOptions.None);

                return smoothOutputAsset;
            }

            /// <summary>
            /// Converts Smooth Streaming to HLS.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The Smooth Streaming asset.</param>
            /// <returns>The asset that was packaged to HLS.</returns>
            private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Read the configuration data into a string. 
                // For the HLS to get encrypted with AES make sure to set the
                // encrypt configuration property to true.
                //
                // In production, it is recommended to do the following:
                //    Set a Key url for your authn/authz server.
                //    Copy the /asset-containerguid/*.key file to your server (or craft a key file for yourself).
                //    Delete *.key from the asset container.
                //
                string configuration = File.ReadAllText(Path.Combine(_configurationXMLFiles, @"MediaPackager_SmoothToHLS.xml"));

                // Create a task with the encoding details, using a configuration file.
                ITask task = job.Tasks.AddNew("My Smooth Streaming to HLS Task",
                   processor,
                   configuration,
                   TaskOptions.ProtectedConfiguration);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(smoothStreamAsset);

                // Add an output asset to contain the results of the job. 
                IAsset outputAsset = 
                    task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);


                return outputAsset;
            }
        }
    }
```

## <a name="using-static-encryption-to-protect-hlsv3-with-playready"></a>Przy użyciu statycznego szyfrowania do ochrony HLSv3 przy użyciu technologii PlayReady
Jeśli chcesz chronić zawartość przy użyciu technologii PlayReady, masz do wyboru przy użyciu [szyfrowania dynamicznego](media-services-protect-with-playready-widevine.md) (zalecana opcja) lub szyfrowania statyczne (zgodnie z opisem w tej sekcji).

> [!NOTE]
> Aby chronić zawartość przy użyciu technologii PlayReady użytkownik musi najpierw przekonwertować/Zakoduj swoją zawartość do formatu, Smooth Streaming.
> 
> 

W przykładzie w tej sekcji koduje pliku mezzanine (w tym MP4 wielkości liter) w plikach metodę MP4. Następnie pakiety każdego pliku MP4 do Smooth Streaming i szyfruje, Smooth Streaming przy użyciu technologii PlayReady. Aby wygenerować HTTP Live Streaming (HLS) zaszyfrowany za pomocą usług PlayReady, elementu zawartości PlayReady Smooth Streaming musi umieszczone w HLS. W tym artykule pokazano, jak wykonać te kroki.

Usługa Media Services udostępnia teraz usługę dostarczania licencji PlayReady firmy Microsoft. W przykładzie w tym artykule przedstawiono sposób konfigurowania usługi dostarczania licencji PlayReady usługi Media Services (zobacz **ConfigureLicenseDeliveryService** metody zdefiniowanej w poniższym kodzie). 

Upewnij się zaktualizować następujący kod, aby wskazać folder, w którym znajduje się plik wejściowy w formacie MP4. A także gdzie znajdują się pliki MediaPackager_MP4ToSmooth.xml MediaPackager_SmoothToHLS.xml i MediaEncryptor_PlayReadyProtection.xml. MediaPackager_MP4ToSmooth.xml i MediaPackager_SmoothToHLS.xml są definiowane w [zadanie ustawienie wstępne dla funkcji pakowania multimediów platformy Azure](https://msdn.microsoft.com/library/azure/hh973635.aspx) i MediaEncryptor_PlayReadyProtection.xml jest zdefiniowany w [zadanie ustawienie wstępne multimediów Azure Modułu szyfrującego](https://msdn.microsoft.com/library/azure/hh973610.aspx) artykułu.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Xml.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

    namespace MediaServicesContentProtection
    {
        class Program
        {
            // Paths to support files (within the above base path). You can use 
            // the provided sample media files from the "SupportFiles" folder, or 
            // provide paths to your own media files below to run these samples.

            private static readonly string _mediaFiles =
                Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
                Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");

            // XML Configuration files path.
            private static readonly string _configurationXMLFiles = @"../..\Configurations\";

            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials =
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


                // Load an MP4 file.
                IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);

                // Encode an MP4 file to a set of multibitrate MP4s.
                // Then, package a set of MP4s to clear Smooth Streaming.
                IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);

                // Create a common encryption content key that is used 
                // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
                //    that is used for encryption.
                // b) to configure the license delivery service and 
                //
                Guid keyId;
                byte[] contentKey;

                IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);

                // The content key authorization policy must be configured by you 
                // and met by the client in order for the PlayReady license
                // to be delivered to the client. 
                // In this example the Media Services PlayReady license delivery service is used.
                ConfigureLicenseDeliveryService(key);

                // Get the Media Services PlayReady license delivery URL.
                // This URL will be assigned to the licenseAcquisitionUrl property 
                // of the MediaEncryptor_PlayReadyProtection.xml file.
                Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

                // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
                UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);

                // Create HLS encrypted with PlayReady.
                IAsset playReadyHLSAsset = CreateHLSEncryptedWithPlayReady(clearSmoothStreamAsset);
                //
                string hlsWithPlayReadyURL = playReadyHLSAsset.GetHlsUri().ToString();
                Console.WriteLine("HLS with PlayReady URL:");
                Console.WriteLine(hlsWithPlayReadyURL);
            }

            /// <summary>
            /// Creates a job with 2 tasks: 
            /// 1 task - encodes a single MP4 to multibitrate MP4s,
            /// 2 task - packages MP4s to Smooth Streaming.
            /// </summary>
            /// <returns>The output asset.</returns>
            public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
            {
                // Create a new job.
                IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");

                // Add task 1 - Encode single MP4 into multibitrate MP4s.
                IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
                // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
                IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // Get the output asset that contains Smooth Streaming.
                return job.OutputMediaAssets[1];
            }

            /// <summary>
            /// Create a common encryption content key that is used 
            /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
            /// that is used for encryption.
            /// </summary>
            /// <param name="keyId"></param>
            /// <param name="contentKey"></param>
            /// <returns></returns>
            public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
            {
                keyId = Guid.NewGuid();
                contentKey = GetRandomBuffer(16);

                IContentKey key = _context.ContentKeys.Create(
                                        keyId,
                                        contentKey,
                                        "ContentKey",
                                        ContentKeyType.CommonEncryption);

                return key;
            }

            /// <summary>
            /// Update your configuration .xml file dynamically.
            /// </summary>
            public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
            {
                string xmlFileName = Path.Combine(_configurationXMLFiles,
                                            @"MediaEncryptor_PlayReadyProtection.xml");

                XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";

                // Prepare the encryption task template
                XDocument doc = XDocument.Load(xmlFileName);

                var licenseAcquisitionUrlEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
                        .FirstOrDefault();
                var contentKeyEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "contentKey")
                        .FirstOrDefault();
                var keyIdEl = doc
                        .Descendants(xmlns + "property")
                        .Where(p => p.Attribute("name").Value == "keyId")
                        .FirstOrDefault();

                // Update the "value" property.
                if (licenseAcquisitionUrlEl != null)
                    licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());

                if (contentKeyEl != null)
                    contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));

                if (keyIdEl != null)
                    keyIdEl.Attribute("value").SetValue(keyId);

                doc.Save(xmlFileName);
            }

            /// <summary>
            // Encrypts clear Smooth Streaming to Smooth Streaming with PlayReady.
            // Then, packages the PlayReady Smooth Streaming to HLS with PlayReady.
            /// </summary>
            /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
            /// <returns>The output asset.</returns>
            public static IAsset CreateHLSEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
            {
                IJob job = _context.Jobs.Create("Encrypt to HLS with PlayReady.");

                // Add task 1 - Encrypt Smooth Streaming with PlayReady 
                IAsset encryptedSmoothAsset =
                    EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);

                // Add task 2 - Package to HLS with PlayReady.
                PackageSmoothStreamToHLS(job, encryptedSmoothAsset);

                // Submit the job and wait until it is completed.
                job.Submit();
                job = job.StartExecutionProgressTask(
                    j =>
                    {
                        Console.WriteLine("Job state: {0}", j.State);
                        Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
                    },
                    CancellationToken.None).Result;

                // Since we had two tasks, the OutputMediaAssets[1]
                // contains the desired asset.
                _context.Locators.Create(
                    LocatorType.OnDemandOrigin,
                    job.OutputMediaAssets[1],
                    AccessPermissions.Read,
                    TimeSpan.FromDays(30));

                return job.OutputMediaAssets[1];
            }

            /// <summary>
            /// Uploads a single file.
            /// </summary>
            /// <param name="fileDir">The location of the files.</param>
            /// <param name="assetCreationOptions">
            ///  You can specify the following encryption options for the AssetCreationOptions.
            ///      None:  no encryption.  
            ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
            ///        before it is uploaded to Azure storage. 
            ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
            ///        For example, a set of files that are already PlayReady encrypted. 
            ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
            ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
            ///     </param>
            /// <returns>Returns an asset that contains a single file.</returns>
            /// </summary>
            /// <returns></returns>
            private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
            {
                // Use the SDK extension method to create a new asset by 
                // uploading a mezzanine file from a local path.
                IAsset asset = _context.Assets.CreateFromFile(
                    fileDir,
                    assetCreationOptions,
                    (af, p) =>
                    {
                        Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
                    });


                return asset;

            }
            /// <summary>
            /// Creates a task to encode to Adaptive Bitrate. 
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Media Encoder Standard.
                IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.MediaEncoderStandard);

                ITask adaptiveBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
                   encoder,
                   "Adaptive Streaming",
                   TaskOptions.None);

                // Specify the input Asset
                adaptiveBitrateTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset abrAsset = adaptiveBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
                                        AssetCreationOptions.None);

                return abrAsset;
            }

            /// <summary>
            /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
            /// Adds the new task to a job.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Azure Media Packager does not accept string presets, so load xml configuration
                string smoothConfig = File.ReadAllText(Path.Combine(
                            _configurationXMLFiles,
                            "MediaPackager_MP4toSmooth.xml"));

                // Create a new Task to convert adaptive bitrate to Smooth Streaming.
                ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
                   packager,
                   smoothConfig,
                   TaskOptions.None);

                // Specify the input Asset, which is the output Asset from the first task
                smoothStreamingTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is in the clear (unencrypted).
                IAsset smoothOutputAsset =
                    smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming",
                        AssetCreationOptions.None);

                return smoothOutputAsset;
            }


            /// <summary>
            /// Converts Smooth Stream to HLS.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The Smooth Stream asset.</param>
            /// <returns>The asset that was packaged to HLS.</returns>
            private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Packager.
                IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaPackager);

                // Read the configuration data into a string. 
                //
                string configuration = File.ReadAllText(
                            Path.Combine(_configurationXMLFiles,
                                        @"MediaPackager_SmoothToHLS.xml"));

                // Create a task with the encoding details, using a configuration file.
                ITask task = job.Tasks.AddNew("My Smooth to HLS Task",
                   processor,
                   configuration,
                   TaskOptions.ProtectedConfiguration);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(smoothStreamAsset);

                // Add an output asset to contain the results of the job. 
                IAsset outputAsset =
                    task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);


                return outputAsset;
            }

            /// <summary>
            /// Creates a task to encrypt Smooth Streaming with PlayReady.
            /// Note: Do deliver DASH, make sure to set the useSencBox and adjustSubSamples 
            /// configuration properties to true.
            /// </summary>
            /// <param name="job">The job to which to add the new task.</param>
            /// <param name="asset">The input asset.</param>
            /// <returns>The output asset.</returns>
            private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
            {
                // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
                IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
                    MediaProcessorNames.WindowsAzureMediaEncryptor);

                // Read the configuration XML.
                //
                // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
                // is using keySeedValue. It is recommended that you do this only for testing 
                // and not in production. For more information, see 
                // https://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
                //
                string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
                                            @"MediaEncryptor_PlayReadyProtection.xml"));

                ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
                   playreadyProcessor,
                   configPlayReady,
                   TaskOptions.ProtectedConfiguration);

                playreadyTask.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
                IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
                                                "PlayReady Smooth Streaming",
                                                AssetCreationOptions.CommonEncryptionProtected);


                return playreadyAsset;
            }


            /// <summary>
            /// Configures authorization policy for the content key. 
            /// </summary>
            /// <param name="contentKey">The content key.</param>
            static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
            {
                // Create ContentKeyAuthorizationPolicy with Open restrictions 
                // and create authorization policy          

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction 
                    { 
                        Name = "Open", 
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                        Requirements = null
                    }
                };

                // Configure PlayReady license template.
                string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                IContentKeyAuthorizationPolicyOption policyOption =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, newLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with no restrictions").
                            Result;


                contentKeyAuthorizationPolicy.Options.Add(policyOption);

                // Associate the content key authorization policy with the content key.
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;
            }

            static private string ConfigurePlayReadyLicenseTemplate()
            {
                // The following code configures PlayReady License Template using .NET classes
                // and returns the XML string.

                PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
                PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

                responseTemplate.LicenseTemplates.Add(licenseTemplate);

                return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
            }
            static private byte[] GetRandomBuffer(int length)
            {
                var returnValue = new byte[length];

                using (var rng =
                    new System.Security.Cryptography.RNGCryptoServiceProvider())
                {
                    rng.GetBytes(returnValue);
                }

                return returnValue;
            }

        }
    }
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

