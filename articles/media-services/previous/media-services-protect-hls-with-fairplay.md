---
title: Ochrona HLS zawartości za pomocą PlayReady firmy Microsoft lub technologii FairPlay firmy Apple — Azure | Dokumentacja firmy Microsoft
description: 'Ten temat zawiera omówienie i pokazuje, jak używać usługi Azure Media Services do dynamicznego szyfrowania zawartości z użyciem technologii FairPlay firmy Apple HTTP Live Streaming (HLS). Pokazano również, jak na potrzeby dostarczać licencje usługi fairplay: dla klientów usługi dostarczania licencji Media Services.'
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
ms.openlocfilehash: 7763338a5c2d5ccb58fe912d9989ab5bb57d5932
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557483"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Ochrona zawartości przy użyciu technologii FairPlay firmy Apple lub Microsoft PlayReady przez moduł HLS
Usługa Azure Media Services umożliwia dynamiczne szyfrowanie zawartości HTTP Live Streaming (HLS), korzystając z następujących formatów:  

* **Czysty klucz AES-128 obwiedni**

    Całego fragmentu, są szyfrowane przy użyciu **CBC AES-128** trybu. Odszyfrowywanie strumień jest obsługiwany natywnie przez systemy iOS i OS X odtwarzacza. Aby uzyskać więcej informacji, zobacz [dynamicznego szyfrowania przy użyciu algorytmu AES-128 i usługa dostarczania kluczy](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Poszczególne próbki audio i wideo są szyfrowane przy użyciu **CBC AES-128** trybu. **FairPlay Streaming** (kl. / s) jest zintegrowana z systemów operacyjnych urządzeń z natywną obsługą w systemach iOS i Apple TV. Safari w systemie OS X umożliwia kl. / s za pomocą obsługi interfejsu Encrypted Media Extensions (EME).
* **Microsoft PlayReady**

Na poniższej ilustracji przedstawiono **HLS + FairPlay lub technologii PlayReady szyfrowania dynamicznego** przepływu pracy.

![Diagram przepływu pracy szyfrowania dynamicznego](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

W tym artykule pokazano, jak dynamiczne szyfrowanie zawartości HLS z użyciem technologii FairPlay firmy Apple za pomocą usługi Media Services. Pokazano również, jak na potrzeby dostarczać licencje usługi fairplay: dla klientów usługi dostarczania licencji Media Services.

> [!NOTE]
> Chcesz także szyfrowanie zawartości przy użyciu technologii PlayReady HLS, musisz utworzyć wspólny klucz zawartości i skojarz go z Twojej zawartości. Należy również skonfigurować zasady autoryzacji klucza zawartości, zgodnie z opisem w [przy użyciu technologii PlayReady, dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Wymagania i uwagi

Wymagane jest spełnienie następujących podczas korzystania z usługi Media Services, aby dostarczać HLS zaszyfrowanych z użyciem technologii FairPlay i dostarczać licencje usługi fairplay::

  * Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Konto usługi Media Services. Aby go utworzyć, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).
  * Zarejestruj się przy użyciu [programem tworzącym Apple](https://developer.apple.com/).
  * Firma Apple wymaga właścicielem zawartości w celu uzyskania [pakietu wdrożeniowego](https://developer.apple.com/contact/fps/). Stan już zaimplementowany klucz zabezpieczeń modułu (KSM) za pomocą usługi Media Services i żądają pakiecie końcowym kl. / s. Istnieją instrukcje w pakiecie końcowym kl. / s, aby wygenerować certyfikacji i Uzyskaj klucz klucz tajny aplikacji (AKS). ZADAJ umożliwiają konfigurowanie technologii FairPlay.
  * Wersja usługi Azure Media Services .NET SDK **3.6.0** lub nowszej.

Następujące elementy musi być ustawione na stronie usługi Media Services do dostarczania kluczy:

  * **Certyfikat aplikacji (AC)**: Jest to plik PFX, który zawiera klucz prywatny. Utwórz ten plik i szyfrować je przy użyciu hasła.

       Po skonfigurowaniu zasad dostarczania kluczy, należy podać to hasło, a plik pfx w formacie Base64.

      W poniższych krokach opisano sposób generowania plik certyfikatu PFX dla technologii FairPlay:

    1. Zainstalować protokół OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

        Przejdź do folderu, w których certyfikat FairPlay i inne pliki dostarczane przez firmę Apple.
    2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to konwersję plik cer do pliku PEM.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 — informuje der — w FairPlay.cer-out out.pem technologii FairPlay
    3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to konwersję plik PEM do pliku .pfx, przy użyciu klucza prywatnego. Hasło dla pliku PFX jest następnie proszeni przez OpenSSL.

        Pkcs12 "C:\OpenSSL-Win32\bin\openssl.exe" — Eksportowanie — limit FairPlay out.pfx-inkey privatekey.pem — w file:privatekey-pem-pass.txt - passin out.pem technologii FairPlay
  * **Hasło certyfikatu aplikacji**: Hasło do tworzenia pliku .pfx.
  * **Hasło certyfikatu aplikacji**: Należy przekazać hasła, podobnie jak przekazują inne klucze usługi Media Services. Użyj **ContentKeyType.FairPlayPfxPassword** wartości wyliczenia można pobrać identyfikatora usługi multimediów. Jest to, czego potrzebują do użycia w opcji zasad dostarczania kluczy.
  * **iv**: Jest to wartość losowe 16 bajtów. Musi on być zgodny iv w zasad dostarczania elementów zawartości. Generowanie iv i umieścić go w obu miejscach: zasady dostarczania elementu zawartości i opcji zasad dostarczania kluczy.
  * **POPROŚ**: Ten klucz jest zgłaszany, jeśli Generowanie certyfikacji przy użyciu portalu dla deweloperów firmy Apple. Każdy zespół programistyczny otrzymuje unikatowy pytanie. Zapisz kopię ZADAJ i zapisz go w bezpiecznym miejscu. Należy skonfigurować poproś jako FairPlayAsk Media Services.
  * **POPROŚ IDENTYFIKATOR**: Ten identyfikator jest uzyskać podczas przekazywania poproś do usługi Media Services. Należy przekazać pytanie za pomocą **ContentKeyType.FairPlayAsk** wartość wyliczenia. W rezultacie identyfikator usługi multimediów jest zwracane, a jest to, jakie powinny być używane, gdy ustawienie opcji zasad dostarczania kluczy.

Po stronie klienta kl. / s, należy ustawić następujące elementy:

  * **Certyfikat aplikacji (AC)**: Jest to plik.cer/.der, który zawiera klucz publiczny, który system operacyjny używa szyfrowania niektóre ładunku. Usługa Media Services musi znać na jego temat, ponieważ jest to wymagane przez gracza. Usługa dostarczania kluczy odszyfrowuje ją przy użyciu odpowiedniego klucza prywatnego.

Aby odtworzyć strumień zaszyfrowanej technologia FairPlay, Pobierz rzeczywistych ZADAJĄ pierwszy, a następnie wygeneruj prawdziwym certyfikatem. Ten proces tworzy wszystkie trzy elementy:

  * pliku der
  * plik PFX
  * hasło PFX

Następujący klienci obsługują HLS z **CBC AES-128** szyfrowania: Safari on OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurowanie technologii FairPlay dynamiczne szyfrowanie i licencji usług dostarczania
Poniżej przedstawiono ogólne kroki chroni Twoje zasoby, z użyciem technologii FairPlay przy użyciu usługi dostarczania licencji Media Services, a także za pomocą szyfrowania dynamicznego.

1. Utwórz element zawartości i przekaż do niego pliki.
2. Przekoduj element zawartości zawierający plik na zestaw MP4 o adaptacyjnej szybkości bitowej.
3. Utwórz klucz zawartości i skojarz go z zakodowanym elementem zawartości.  
4. Skonfiguruj zasady autoryzacji klucza zawartości. Określ następujące ustawienia:

   * Metoda dostarczania (w tym przypadku FairPlay).
   * Technologia FairPlay konfiguracji opcje zasad. Aby uzyskać więcej informacji na temat konfigurowania technologii FairPlay, zobacz **ConfigureFairPlayPolicyOptions()** metody w poniższym przykładzie.

     > [!NOTE]
     > Zazwyczaj należy skonfigurować opcje zasad FairPlay tylko raz, ponieważ masz tylko jeden zestaw certyfikacji i poproś.
     >
     >
   * Ograniczenia (otwarte lub tokenu).
   * Informacje specyficzne dla typu klucza dostarczania, który definiuje sposób dostarczania klucza do klienta.
5. Konfigurowanie zasad dostarczania elementów zawartości. Konfiguracja zasad dostarczania obejmuje:

   * Protokół dostarczania (HLS).
   * Typ szyfrowania dynamicznego (common CBC encryption).
   * Adres URL pozyskiwania licencji.

     > [!NOTE]
     > Jeśli użytkownik chce dostarczać strumień, który jest szyfrowana za pomocą technologii FairPlay i innym systemem Digital Rights Management (DRM), musisz skonfigurować zasady dostarczania oddzielne:
     >
     > * Jeden IAssetDeliveryPolicy skonfigurować Dynamic Adaptive Streaming za pośrednictwem protokołu HTTP (DASH) za pomocą Common Encryption (CENC) (PlayReady i Widevine) i Smooth za pomocą usług PlayReady
     > * Inny IAssetDeliveryPolicy konfigurowanie technologii FairPlay dla HLS
     >
     >
6. Utwórz lokalizator OnDemand w celu pobrania adresu URL przesyłania strumieniowego.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Technologia FairPlay dostarczania kluczy za pomocą aplikacji odtwarzacza
Możesz opracowywać aplikacje player przy użyciu zestawu SDK dla systemu iOS. Aby można było do odtwarzania zawartości z użyciem technologii FairPlay, musisz zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez firmę Apple. Jest każda aplikacja sposób wysyłania żądań dostarczania kluczy. Usługa dostarczania kluczy Media Services FairPlay oczekuje SPC na uszkodzonym www-form-url wpis zakodowany komunikat w następującej postaci:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Usługa Azure Media Player obsługuje FairPlay odtwarzania. Zobacz [dokumentacji usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) Aby uzyskać więcej informacji.
>
>

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli element zawartości został zaszyfrowany za pomocą więcej niż jeden DRM, należy używać szyfrowania tagu w adresu URL przesyłania strumieniowego: (format = "m3u8-aapl" szyfrowania = "xxx").

Mają zastosowanie następujące kwestie:

* Można określić tylko zero lub jeden typ szyfrowania.
* Typ szyfrowania nie ma określonego w adresie URL, jeśli tylko jeden szyfrowania została zastosowana do zasobu.
* Typ szyfrowania jest uwzględniana wielkość liter.
* Można określić następujące typy szyfrowania:  
  * **cenc**:  Stosowanie szyfrowania Common encryption (PlayReady lub Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: Szyfrowanie koperty AES

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 
2. Dodaj następujące elementy do węzła **appSettings** zdefiniowanego w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Przykład

W poniższym przykładzie pokazano możliwość korzystania z usługi Media Services do dostarczania zawartości zaszyfrowany z użyciem technologii FairPlay. Ta funkcja została wprowadzona w usłudze Azure Media Services SDK dla platformy .NET w wersji 3.6.0. 

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

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
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

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
