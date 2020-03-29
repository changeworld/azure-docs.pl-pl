---
title: Ochrona zawartości HLS za pomocą programu Microsoft PlayReady lub Apple FairPlay — Azure | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie i pokazano, jak korzystać z usługi Azure Media Services do dynamicznego szyfrowania zawartości http live streaming (HLS) za pomocą usługi Apple FairPlay. Pokazuje również, jak korzystać z usługi dostarczania licencji usługi Media Services w celu dostarczania licencji FairPlay klientom.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968769"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Chroń zawartość HLS za pomocą Apple FairPlay lub Microsoft PlayReady

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do usługi Media Services w wersji 2 nie są dodawane żadne nowe funkcje ani funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)
>

Usługa Azure Media Services umożliwia dynamiczne szyfrowanie zawartości http live streaming (HLS) przy użyciu następujących formatów:  

* **AES-128 koperta wyczyszczenie klucza**

    Cały fragment jest szyfrowany przy użyciu trybu **CBC AES-128.** Odszyfrowywanie strumienia jest obsługiwane przez natywnie odtwarzacz iOS i OS X. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi szyfrowania dynamicznego aes-128 i dostarczania kluczy](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Poszczególne próbki wideo i audio są szyfrowane przy użyciu trybu **CBC AES-128.** **Usługa FairPlay Streaming** (FPS) jest zintegrowana z systemami operacyjnymi urządzeń, z natywną obsługą na iOS i Apple TV. Safari w os X włącza FPS przy użyciu obsługi interfejsu szyfrowanych rozszerzeń multimediów (EME).
* **Microsoft PlayReady**

Na poniższej ilustracji przedstawiono przepływ pracy **szyfrowania dynamicznego HLS + FairPlay lub PlayReady.**

![Diagram dynamicznego przepływu pracy szyfrowania](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

W tym artykule pokazano, jak korzystać z usługi Media Services do dynamicznego szyfrowania zawartości HLS za pomocą usługi Apple FairPlay. Pokazuje również, jak korzystać z usługi dostarczania licencji usługi Media Services w celu dostarczania licencji FairPlay klientom.

> [!NOTE]
> Jeśli chcesz również zaszyfrować zawartość HLS za pomocą PlayReady, musisz utworzyć wspólny klucz zawartości i skojarzyć go z zasobem. Należy również skonfigurować zasady autoryzacji klucza zawartości, zgodnie z opisem w [temacie Używanie wspólnego szyfrowania dynamicznego PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Wymagania i uwagi

Podczas korzystania z usługi Media Services do dostarczania usługi HLS zaszyfrowanej za pomocą usługi FairPlay oraz do dostarczania licencji FairPlay wymagane są następujące elementy:

  * Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Konto usługi Media Services. Aby go utworzyć, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
  * Zarejestruj się w [Programie Rozwoju Firmy Apple](https://developer.apple.com/).
  * Firma Apple wymaga od właściciela zawartości uzyskania [pakietu wdrożeniowego](https://developer.apple.com/contact/fps/). Oświadcza, że moduł zabezpieczeń kluczy (KSM) został już zaimplementowany w u usług media services i że żądasz ostatecznego pakietu FPS. Istnieją instrukcje w ostatecznym pakiecie FPS do generowania certyfikacji i uzyskania klucza tajnego aplikacji (ASK). Ask służy do konfigurowania FairPlay.
  * Zestaw SDK usługi Azure Media Services .NET w wersji **3.6.0** lub nowszej.

Po stronie dostarczania kluczy usługi Media Services należy ustawić następujące czynności:

  * **Certyfikat aplikacji (AC)**: Jest to plik .pfx, który zawiera klucz prywatny. Tworzysz ten plik i szyfrujesz go hasłem.

       Podczas konfigurowania zasad dostarczania kluczy należy podać to hasło i plik pfx w formacie Base64.

      W poniższych krokach opisano sposób generowania pliku certyfikatu pfx dla gry FairPlay:

    1. Zainstaluj OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlz .

        Przejdź do folderu, w którym znajduje się certyfikat FairPlay i inne pliki dostarczane przez firmę Apple.
    2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku cer na plik pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku pem na plik .pfx z kluczem prywatnym. Hasło do pliku .pfx jest następnie zadawane przez OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Hasło certyfikatu aplikacji**: Hasło do tworzenia pliku .pfx.
  * **Identyfikator hasła certyfikatu aplikacji:** musisz przesłać hasło, podobnie jak przesyłane są inne klucze usługi Media Services. Użyj **contentkeytype.FairPlayPfpfxPassword** wartość wyliczenia, aby uzyskać identyfikator usługi Media Services. To jest to, czego muszą użyć w opcji polityki dostarczania kluczy.
  * **iv**: Jest to wartość losowa 16 bajtów. Musi być zgodny z iv w polityce dostarczania aktywów. Generujesz iv i umieszczasz go w obu miejscach: zasadach dostarczania aktywów i opcji polityki dostarczania kluczy.
  * **ASK:** Ten klucz jest odbierany podczas generowania certyfikacji przy użyciu portalu Apple Developer. Każdy zespół programistów otrzymuje unikatowy ASK. Zapisz kopię ask i przechowuj ją w bezpiecznym miejscu. Należy skonfigurować ASK jako FairPlayAsk do usługi Media Services później.
  * **Identyfikator ASK:** Ten identyfikator jest uzyskiwany podczas przekazywania ask do usługi Media Services. Funkcja Wyliczenia **ContentKeyType.FairPlayAsk** musi zostać przekazana przez program ASK. W rezultacie identyfikator usługi Media Services jest zwracany i jest to, co powinno być używane podczas ustawiania opcji zasad dostarczania kluczy.

Po stronie klienta FPS muszą być ustawione następujące czynności:

  * **Certyfikat aplikacji (AC)**: Jest to plik .cer/.der, który zawiera klucz publiczny, którego system operacyjny używa do szyfrowania ładunku. Usługi media muszą o tym wiedzieć, ponieważ jest to wymagane przez odtwarzacza. Usługa dostarczania kluczy odszyfrowuje go przy użyciu odpowiedniego klucza prywatnego.

Aby odtworzyć zaszyfrowany strumień FairPlay, najpierw uzyskaj prawdziwy ask, a następnie wygeneruj prawdziwy certyfikat. Proces ten tworzy wszystkie trzy części:

  * plik der
  * .pfx plik
  * hasło do .pfx

Następujący klienci obsługują hls z **szyfrowaniem CBC AES-128:** Safari na OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurowanie usług szyfrowania dynamicznego i dostarczania licencji FairPlay
Poniżej przedstawiono ogólne kroki ochrony zasobów za pomocą usługi FairPlay przy użyciu usługi dostarczania licencji usługi Media Services, a także przy użyciu szyfrowania dynamicznego.

1. Utwórz element zawartości i przekaż do niego pliki.
2. Przekoduj element zawartości zawierający plik na zestaw MP4 o adaptacyjnej szybkości bitowej.
3. Utwórz klucz zawartości i skojarz go z zakodowanym elementem zawartości.  
4. Skonfiguruj zasady autoryzacji klucza zawartości. Określ następujące ustawienia:

   * Metoda dostawy (w tym przypadku FairPlay).
   * Konfiguracja opcji zasad FairPlay. Aby uzyskać szczegółowe informacje na temat konfigurowania gry FairPlay, zobacz **configureFairPlayPolicyOptions()** metody w poniższym przykładzie.

     > [!NOTE]
     > Zazwyczaj chcesz skonfigurować opcje zasad FairPlay tylko raz, ponieważ będziesz mieć tylko jeden zestaw certyfikatów i ASK.
     >
     >
   * Ograniczenia (otwarte lub token).
   * Informacje specyficzne dla typu dostarczania klucza, który definiuje sposób dostarczania klucza do klienta.
5. Skonfiguruj zasady dostarczania zasobów. Konfiguracja zasad dostarczania obejmuje:

   * Protokół dostawy (HLS).
   * Typ szyfrowania dynamicznego (typowe szyfrowanie CBC).
   * Adres URL nabycia licencji.

     > [!NOTE]
     > Jeśli chcesz dostarczyć strumień zaszyfrowany za pomocą fairplay i innego systemu zarządzania prawami cyfrowymi (DRM), musisz skonfigurować oddzielne zasady dostarczania:
     >
     > * Jedna iassetDeliveryPolicy do konfigurowania dynamicznego adaptacyjnego przesyłania strumieniowego przez HTTP (DASH) z powszechnym szyfrowaniem (CENC) (PlayReady + Widevine) i Smooth z PlayReady
     > * Kolejna IAssetDeliveryPolicy skonfigurować FairPlay dla HLS
     >
     >
6. Utwórz lokalizator OnDemand w celu pobrania adresu URL przesyłania strumieniowego.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Korzystanie z dostarczania kluczy FairPlay przez aplikacje gracza
Aplikacje dla graczy można tworzyć za pomocą sdk systemu iOS. Aby móc odtwarzać zawartość FairPlay, musisz zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez apple. To do każdej aplikacji, jak wysyłać żądania dostarczania kluczy. Usługa dostarczania kluczy Programu Media Services FairPlay oczekuje, że SPC pojawi się jako wiadomość post zakodowana w formularzu www-form-url w następującej formie:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Usługa Azure Media Player obsługuje odtwarzanie fairplay. Aby uzyskać więcej informacji, zobacz [dokumentację programu Azure Media Player.](https://amp.azure.net/libs/amp/latest/docs/index.html)
>
>

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli twój zasób został zaszyfrowany za pomocą więcej niż jednego drm, należy użyć tagu szyfrowania w adresie URL przesyłania strumieniowego: (format='m3u8-aapl', encryption='xxx').

Obowiązują następujące zastrzeżenia:

* Można określić tylko zero lub jeden typ szyfrowania.
* Typ szyfrowania nie musi być określony w adresie URL, jeśli tylko jedno szyfrowanie zostało zastosowane do zasobu.
* Typ szyfrowania jest bez uwzględniania wielkości liter.
* Można określić następujące typy szyfrowania:  
  * **cenc**: Typowe szyfrowanie (PlayReady lub Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: Szyfrowanie kopert AES

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 
2. Dodaj następujące elementy do węzła **appSettings** zdefiniowanego w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Przykład

Poniższy przykład pokazuje możliwość korzystania z usługi Media Services do dostarczania zawartości zaszyfrowanej za pomocą fairplay. Ta funkcja została wprowadzona w usłudze Azure Media Services SDK dla platformy .NET w wersji 3.6.0. 

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Upewnij się, że zaktualizowano zmienne, tak aby wskazywały foldery, w których znajdują się pliki danych wejściowych.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
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


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
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

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
