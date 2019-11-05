---
title: Tworzenie modelu dzierżawy (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Automatycznie Generuj niestandardowy model mowy, który korzysta z danych usługi Office 365, aby zapewnić optymalne rozpoznawanie mowy dla specyficznych dla organizacji warunków, które są bezpieczne i zgodne.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 85b9291ee24c024ebc8ce81ddba46d04f7744081
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502660"
---
# <a name="create-a-tenant-model-preview"></a>Tworzenie modelu dzierżawy (wersja zapoznawcza)

Model dzierżawy to usługa z dodatkiem dla klientów korporacyjnych usługi Office 365, która automatycznie generuje niestandardowy model rozpoznawania mowy z danych usługi Office 365. Tworzony model jest zoptymalizowany pod kątem warunków technicznych, żargon i nazwisk osób, a wszystko to w bezpieczny i zgodny sposób.

> [!IMPORTANT]
> Jeśli Twoja organizacja rejestruje się w modelu dzierżawy, usługa mowy może uzyskać dostęp do modelu języka organizacji, który jest generowany przez zasoby pakietu Office 365, takie jak wiadomości e-mail i dokumenty. Administrator programu Office 365 w organizacji może włączyć/wyłączyć użycie modelu języka w całej organizacji przy użyciu portalu administracyjnego pakietu Office 365.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zarejestruj się, aby korzystać z modelu dzierżawy w centrum administracyjnym Microsoft 365
> * Pobierz klucz subskrypcji mowy
> * Tworzenie modelu dzierżawy
> * Wdrażanie modelu dzierżawy
> * Korzystanie z modelu dzierżawy z zestawem SDK mowy

![Diagram modelu dzierżawców](media/tenant-language-model/tenant-language-model-diagram.png)

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Rejestrowanie przy użyciu Centrum administracyjnego Microsoft 365

Przed wdrożeniem modelu dzierżawy należy najpierw zarejestrować się przy użyciu Centrum administracyjnego Microsoft 365. To zadanie można wykonać tylko przez administratora Microsoft 365.

1. Zaloguj się do [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com ).
2. W lewym panelu wybierz kolejno pozycje **Ustawienia** i **aplikacje**.

   ![Diagram modelu dzierżawców](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Znajdź i wybierz pozycję **Azure Speech Services**.

   ![Diagram modelu dzierżawców](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Kliknij pole wyboru i Zapisz.

Jeśli musisz wyłączyć model dzierżawców, przejdź z powrotem do tego ekranu, usuń zaznaczenie pola wyboru i Zapisz.

## <a name="get-a-speech-subscription-key"></a>Pobierz klucz subskrypcji mowy

Aby można było używać modelu dzierżawy z zestawem SDK mowy, potrzebny będzie zasób mowy i skojarzony z nim klucz subskrypcji.

1. Zaloguj się w witrynie [Azure Portal](https://aka.ms/azureportal).
2. Wybierz pozycję **Utwórz zasób**.
3. Na pasku wyszukiwania wpisz: **Speech**.
4. Wybierz pozycję **Speech**, a następnie kliknij pozycję **Utwórz**.
5. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby utworzyć zasób. Upewnij się:
   * **Lokalizacja** jest ustawiona na **Wschodnie** lub **zachodnie**.
   * **Warstwa cenowa** jest ustawiona na **S0**.
6. Kliknij pozycję **Utwórz**.
7. Po kilku minutach zasób zostanie utworzony. Klucz subskrypcji jest dostępny w sekcji **Przegląd** dla zasobu.

## <a name="create-a-model"></a>Tworzenie modelu

Gdy administrator włączył model dzierżawy dla Twojej organizacji, możesz utworzyć model języka oparty na danych usługi Office 365.

1. Zaloguj się do programu [Speech Studio](https://speech.microsoft.com/).
2. W prawym górnym rogu Znajdź i kliknij ikonę koła zębatego (Ustawienia), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Menu Ustawienia](media/tenant-language-model/tenant-language-settings.png)

3. W tym momencie zobaczysz komunikat informujący o tym, że masz kwalifikacje do utworzenia modelu dzierżawy.
   > [!NOTE]
   > Klienci korzystający z pakietu Office 365 Enterprise w Ameryka Północna mogą tworzyć model dzierżawy (angielski). Jeśli jesteś klientem z kluczem Skrytka klienta (CLB) lub klientem, ta funkcja jest niedostępna. Aby określić, czy jesteś klientem z kluczem Skrytka klienta lub klientem, wykonaj następujące instrukcje:
   > * [Skrytka klienta](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Klucz klienta](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)

4. Następnie wybierz pozycję zgadzam **się**. Gdy model dzierżawy jest gotowy, otrzymasz wiadomość e-mail z instrukcjami.

## <a name="deploy-your-model"></a>Wdrażanie modelu

Gdy model dzierżawy jest gotowy, wykonaj następujące kroki, aby wdrożyć model:

1. Kliknij przycisk **Wyświetl model** w otrzymanej wiadomości e-mail z potwierdzeniem lub Zaloguj się do programu [Speech Studio](https://speech.microsoft.com/).
2. W prawym górnym rogu Znajdź i kliknij ikonę koła zębatego (Ustawienia), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Menu Ustawienia](media/tenant-language-model/tenant-language-settings.png)

3. Kliknij przycisk **Deploy (Wdróż)** .
4. Po wdrożeniu modelu stan zmieni się na **wdrożone**.

## <a name="use-your-model-with-the-speech-sdk"></a>Korzystanie z modelu za pomocą zestawu Speech SDK

Teraz, gdy już wdrożono model, możesz go używać z zestawem Speech SDK. W tej sekcji użyjesz przykładowego kodu do wywołania usługi mowy przy użyciu uwierzytelniania usługi Azure AD.

Przyjrzyjmy się kodowi, który będzie używany do wywoływania zestawu Speech SDK C#w programie. W tym przykładzie wykonasz rozpoznawanie mowy przy użyciu modelu dzierżawy. W tym przewodniku zakłada się, że platforma została już skonfigurowana. Jeśli potrzebujesz pomocy przy konfigurowaniu, zobacz [Szybki Start: Rozpoznawanie mowy, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Skopiuj ten kod do projektu:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Następnie należy ponownie skompilować i uruchomić projekt z wiersza polecenia. Istnieje kilka parametrów, które należy zaktualizować przed uruchomieniem polecenia.

1. Zastąp `<Username>` i `<Password>` wartościami dla prawidłowego użytkownika dzierżawy.
2. Zastąp `<Subscription-Key>` kluczem subskrypcji dla zasobu mowy. Ta wartość jest dostępna w sekcji **Przegląd** dla zasobu mowy w [Azure Portal](https://aka.ms/azureportal).
3. Zastąp `<Endpoint-Uri>` następującym punktem końcowym. Upewnij się, że zastąpisz `{your-region}` w regionie, w którym został utworzony zasób mowy. Obsługiwane są następujące regiony: `westus`, `westus2`i `eastus`. Informacje o regionie są dostępne w sekcji **Przegląd** dla zasobu mowy w [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Uruchom polecenie:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Następne kroki

* [Speech Studio](https://speech.microsoft.com/)
* [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
