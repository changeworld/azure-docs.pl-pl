---
title: 'Szybki start: Konwertuj tekst na mowę, .NET Core — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się, jak konwertować mowę przy użyciu interfejsu API REST zamiany tekstu na mowę. Przykładowy tekst uwzględnione w tym przewodniku mają strukturę jako język znaczników synteza mowy (SSML). Dzięki temu możliwe jest wybranie głos i język odpowiedzi mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 5ae63b1738824095073ac6b9e1071f6b4a3e5ae1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518851"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Szybki start: Konwertowanie tekstu na mowę, przy użyciu platformy .NET Core

W tym przewodniku Szybki Start dowiesz się, jak konwertować zamiany tekstu na mowę przy użyciu platformy .NET Core i interfejsu API REST zamiany tekstu na mowę. Tekst przykładowy uwzględnione w tym przewodniku ma strukturę [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md), co pozwala wybrać głos i język w odpowiedzi.

Ten przewodnik Szybki Start wymaga [konta usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usługi mowy

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o tts-sample
cd tts-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `tts-sample`. Drugie polecenie zmienia katalog na katalog projektu.

## <a name="select-the-c-language-version"></a>Wybierz C# wersja językowa

Ten przewodnik Szybki Start wymaga C# 7.1 lub nowszej. Istnieje kilka sposobów, aby zmienić C# wersji dla projektu. W tym przewodniku pokażemy ci jak dostosować `tts-sample.csproj` pliku. Dostępne opcje, takie jak zmiana języka w programie Visual Studio, można znaleźć [wybierz C# wersji językowej](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otwórz swój projekt, a następnie otwórz `tts-sample.csproj`. Upewnij się, że `LangVersion` jest ustawiony do wersji 7.1 lub nowszej. Jeśli nie ma grupy właściwości w wersji językowej, Dodaj następujące wiersze:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Upewnij się zapisać zmiany.

## <a name="add-required-namespaces-to-your-project"></a>Dodawanie wymaganych obszarów nazw do projektu

Uruchomione wcześnie polecenie `dotnet new console` spowodowało utworzenie projektu obejmującego plik `Program.cs`. W tym pliku umieścisz kod aplikacji. Otwórz plik `Program.cs` i zastąp istniejące instrukcje using. Te instrukcje gwarantują dostęp do wszystkich typów wymaganych do skompilowania i uruchomienia przykładowej aplikacji.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Utwórz klasę dla tokenu programu exchange

Zamiany tekstu na mowę interfejsu API REST wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. W tym przykładzie wymienia klucz subskrypcji usług przetwarzania mowy, aby uzyskać dostęp do tokenu przy użyciu `issueToken` punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcji usług przetwarzania mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `FetchTokenUri`. Aby uzyskać pełną listę, zobacz [regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnienia przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Uzyskaj token dostępu i Ustaw adres URL hosta

Znajdź `static void Main(string[] args)` i zastąp go wartością `static async Task Main(string[] args)`.

Następnie skopiuj ten kod w metodzie głównej. Robi kilka rzeczy, ale najważniejsze, przyjmuje tekst jako dane wejściowe i wywołania `Authentication` funkcję, aby wymienić klucz subskrypcji dla tokenu dostępu. Jeśli coś pójdzie nie tak, błąd jest drukowany w konsoli.

Upewnij się dodać klucz subskrypcji przed uruchomieniem aplikacji.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Następnie ustaw hosta i trasę dla zamiany tekstu na mowę:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Żądanie SSML kompilacji

Tekst jest wysyłany jako treść metody `POST` żądania. Za pomocą SSML można określić, głos i język. W tym przewodniku Szybki Start użyjemy SSML z ustawionym językiem `en-US` i głosu Ustaw jako `ZiraRUS`. Możemy utworzyć SSML dla żądania:

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> W tym przykładzie użyto `ZiraRUS` czcionka głosowa. Aby uzyskać pełną listę Microsoft podany głosów/języków, zobacz [języki](language-support.md). Jeśli interesuje Cię tworzenie unikatowy, rozpoznawalny głos na marki, zobacz [tworzenia czcionki głosowe niestandardowe](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Utwórz wystąpienie klienta, zgłosić wniosek i zapisać w pliku audio syntetyzowany

Istnieje wiele dzieje się w tym przykładzie kodu. Szybko Omówmy co się dzieje:

* Klient i żądania są tworzone.
* Metoda HTTP jest ustawiony jako `POST`.
* Wymagane nagłówki są dodawane do żądania.
* Żądanie jest wysyłane i kod stanu jest zaznaczone.
* Odpowiedź jest odczytywana asynchronicznie i zapisywana w pliku o nazwie sample.wav.

Skopiuj ten kod do projektu. Pamiętaj zastąpić wartość `User-Agent` nagłówek o nazwie zasobu w witrynie Azure portal.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, wszystko jest gotowe do uruchomienia aplikacji zamiany tekstu na mowę. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

Jeśli to się powiedzie, plik mowy jest zapisywany w folderze projektu. Odtwarzać je za pomocą odtwarzacza media ulubionych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy .NET w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
* [Próbki głosu rekord do utworzenia niestandardowych voice](record-custom-voice-samples.md)
