---
title: Tworzenie modelu dzierżawy (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Automatycznie wygeneruj bezpieczny, zgodny model dzierżawy (Usługa Mowa niestandardowa z danymi usługi Office 365), który używa danych usługi Office 365 do dostarczania optymalnego rozpoznawania mowy dla terminów specyficznych dla organizacji.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469021"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Samouczek: Tworzenie modelu dzierżawy (wersja zapoznawcza)

Model dzierżawy (Niestandardowa mowa z danymi usługi Office 365) to usługa opt-in dla klientów korporacyjnych usługi Office 365, która automatycznie generuje niestandardowy model rozpoznawania mowy na podstawie danych usługi Office 365 w organizacji. Model jest zoptymalizowany pod kątem pod względem technicznym, żargonu i nazw osób, a wszystko to w bezpieczny i zgodny z przepisami sposób.

> [!IMPORTANT]
> Jeśli organizacja rejestruje się przy użyciu usługi Model dzierżawy, usługa mowy może uzyskać dostęp do modelu języka organizacji. Model jest generowany z publicznych wiadomości e-mail i dokumentów grupy usługi Office 365, które mogą być widoczne dla wszystkich osób w organizacji. Administrator usługi Office 365 w organizacji może włączyć lub wyłączyć korzystanie z modelu języka dla całej organizacji z portalu administracyjnego usługi Office 365.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie się w modelu dzierżawy przy użyciu centrum administracyjnego usługi Microsoft 365
> * Pobierz klucz subskrypcji mowy
> * Tworzenie modelu dzierżawy
> * Wdrażanie modelu dzierżawy
> * Używanie modelu dzierżawy przy użyciu sdk mowy

## <a name="enroll-in-the-tenant-model-service"></a>Zarejestruj się w usłudze modelu dzierżawcy

Przed wdrożeniem modelu dzierżawy, należy zarejestrować się w usłudze modelu dzierżawy. Rejestracja jest zakończona w centrum administracyjnym usługi Microsoft 365 i może być wykonywana tylko przez administratora usługi Microsoft 365.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

1. W lewym okienku wybierz **pozycję Ustawienia**, a następnie wybierz **pozycję Ustawienia** z menu zagnieżdżonego, a następnie wybierz pozycję Usługi rozpoznawania mowy **platformy Azure** w oknie głównym.

   ![Okienko "Usługi & dodatki"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Zaznacz pole wyboru **Zezwalaj na model języka dla całej organizacji,** a następnie wybierz pozycję **Zapisz zmiany**.

   ![Okienko Usługi mowy platformy Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Aby wyłączyć wystąpienie modelu dzierżawy:
1. Powtórz poprzednie kroki 1 i 2.
1. Wyczyść pole wyboru **Zezwalaj na model języka dla całej organizacji,** a następnie wybierz pozycję **Zapisz zmiany**.

## <a name="get-a-speech-subscription-key"></a>Pobierz klucz subskrypcji mowy

Aby użyć modelu dzierżawy z SDK mowy, potrzebujesz zasobu mowy i skojarzonego z nim klucza subskrypcji.

1. Zaloguj się do [Portalu Azure](https://aka.ms/azureportal).
1. Wybierz pozycję **Utwórz zasób**.
1. W polu **Wyszukiwania** wpisz polecenie **Mowa**.
1. Na liście wyników wybierz pozycję **Mowa**, a następnie wybierz pozycję **Utwórz**.
1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby utworzyć zasób. Upewnij się, że:
   * **Lokalizacja** jest ustawiona na **eastus** lub **westus**.
   * **Warstwa cenowa** jest ustawiona na **S0**.
1. Wybierz **pozycję Utwórz**.

   Po kilku minutach zasób jest tworzony. Klucz subskrypcji jest dostępny w sekcji **Przegląd** zasobu.

## <a name="create-a-language-model"></a>Tworzenie modelu języka

Po włączeniu modelu dzierżawy administratora w organizacji można utworzyć model języka oparty na danych usługi Office 365.

1. Zaloguj się do [Speech Studio](https://speech.microsoft.com/).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Łącze "Ustawienia modelu dzierżawy"](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio wyświetla komunikat, który informuje, czy masz kwalifikacje do utworzenia modelu dzierżawy.

   > [!NOTE]
   > Klienci korporacyjni usługi Office 365 w Ameryce Północnej mogą utworzyć model dzierżawy (w języku angielskim). Jeśli jesteś klientem skrytki klienta, klucza klienta lub usługi Office 365 dla instytucji rządowych, ta funkcja jest niedostępna. Aby ustalić, czy jesteś klientem skrytki klienta, czy kluczem klienta, zobacz:
   > * [Skrytka klienta](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klucz klienta](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 dla instytucji rządowych](https://www.microsoft.com/microsoft-365/government)

1. Wybierz pozycję **Wyraź zgodę**.

   Gdy model dzierżawy jest gotowy, otrzymasz wiadomość e-mail z potwierdzeniem z dalszymi instrukcjami.

## <a name="deploy-your-tenant-model"></a>Wdrażanie modelu dzierżawy

Gdy wystąpienie modelu dzierżawy jest gotowe, wdrożyć go, wykonując następujące czynności:

1. W wiadomości e-mail z potwierdzeniem wybierz przycisk **Wyświetl model.** Lub zaloguj się do [Speech Studio](https://speech.microsoft.com/).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Łącze "Ustawienia modelu dzierżawy"](media/tenant-language-model/tenant-language-settings.png)

1. Wybierz pozycję **Wdróż**.

   Po wdrożeniu modelu stan zmieni się na *Wdrożony*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Używanie modelu dzierżawy przy użyciu sdk mowy

Teraz, gdy wdrożono model, można go używać z SDK mowy. W tej sekcji użyj przykładowego kodu do wywołania usługi mowy przy użyciu uwierzytelniania usługi Azure Active Directory (Azure AD).

Przyjrzyjmy się kodowi, którego użyjesz do wywołania SDK mowy w języku C#. W tym przykładzie można wykonać rozpoznawanie mowy przy użyciu modelu dzierżawy. Ten przewodnik zakłada, że platforma jest już skonfigurowana. Jeśli potrzebujesz pomocy w instalacji, zobacz [Szybki start: Rozpoznawanie mowy, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // ServiceApplicationId is a fixed value. No need to change it.

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

Następnie należy odbudować i uruchomić projekt z wiersza polecenia. Przed uruchomieniem polecenia należy zaktualizować kilka parametrów, wykonując następujące czynności:

1. Zamień `<Username>` i `<Password>` wartości dla prawidłowego użytkownika dzierżawy.
1. Zamień `<Subscription-Key>` klucz subskrypcji zasobu mowy. Ta wartość jest dostępna w sekcji **Przegląd** zasobu mowy w [witrynie Azure portal](https://aka.ms/azureportal).
1. Zastąp `<Endpoint-Uri>` następującym punktem końcowym. Upewnij się, `{your region}` że zastąpisz region, w którym utworzono zasób Mowy. Regiony te są `westus`obsługiwane: `westus2` `eastus`, , i . Informacje o regionie są dostępne w sekcji **Przegląd** zasobu mowy w [witrynie Azure portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Uruchom następujące polecenie:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

W tym samouczku dowiesz się, jak utworzyć niestandardowy model rozpoznawania mowy za pomocą danych usługi Office 365, wdrożyć go i używać za pomocą zestawu SDK mowy.

## <a name="next-steps"></a>Następne kroki

* [Studio mowy](https://speech.microsoft.com/)
* [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
