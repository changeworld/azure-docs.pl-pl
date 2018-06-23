---
title: Visual samouczek aplikacji mobilnej wyszukiwania
description: Otwórz aplikację źródłowego C# Implementowanie visual wyszukiwania przy użyciu platformy i platform wizji komputera kognitywnych usług interfejsu API, interfejsu API Bing sieci Web wyszukiwania i platformy Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347628"
---
# <a name="visual-search-mobile-app-tutorial"></a>Visual samouczek aplikacji mobilnej wyszukiwania

## <a name="introduction"></a>Wprowadzenie  
W tym samouczku Eksploruje [API przetwarzania obrazów komputera](https://azure.microsoft.com/services/cognitive-services/computer-vision/) i [interfejsu API Bing sieci Web wyszukiwania](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) punktów końcowych i pokazuje, jak ich może służyć do tworzenia aplikacji wyszukiwania visual basic z [platformy Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  Ogólne w tym samouczku omówiono następujące tematy: 

> [!div class="checklist"]
> * Konfigurowanie programu Visual Studio do tworzenia aplikacji platformy Xamarin.Forms
> * Przy użyciu [dodatek Media Xamarin](https://github.com/jamesmontemagno/MediaPlugin) do przechwytywania i zaimportować dane obrazu
> * Analizowanie tekstu z obrazu przy użyciu interfejsy API przetwarzania obrazów
> * Wysyłanie żądań wyszukiwania do interfejsu API wyszukiwania usługi Bing sieci Web
> * Analiza kodu JSON odpowiedzi z obu interfejsów API za pomocą [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (LINQ i modelu obiektu deserializacji)
> * Tworzenie interfejsu użytkownika platformy Xamarin.Forms wieloplatformowych visual wyszukiwania 

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie został utworzony przy użyciu platformy Xamarin.Forms z [programu Visual Studio 2017](https://www.visualstudio.com/downloads/). Mogą być używane Community Edition programu Visual Studio 2017 r. Aby uzyskać więcej informacji o korzystaniu z programu Visual Studio Xamarin, zobacz [dokumentacji Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/).

W tym przykładzie korzysta z następujących pakietów NuGet:

> [!div class="checklist"]
> * [Dodatek Xamarin Media](https://github.com/jamesmontemagno/MediaPlugin)
> * [Struktury Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

Aplikacja używa następujących kognitywnych interfejsów API usługi:

> [!div class="checklist"]
> * [Wyszukiwania usługi Bing w sieci Web interfejsu API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [Interfejs API przetwarzania obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Aby uzyskać 30-dniowej wersji próbnej kluczy dla tych interfejsów API, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/). Aby uzyskać więcej informacji na temat uzyskiwania kluczy do użytku komercyjnego, zobacz [cennik](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Ustawienia dla rozwoju  

### <a name="installing-xamarin-on-windows"></a>Instalowanie platformy Xamarin w systemie Windows
Z dowolnej wersji programu Visual Studio 2017 r zainstalowany, otwórz Instalator programu Visual Studio, wybierz menu hamburger związane z instalacją programu Visual Studio i wybierz **Modyfikuj**.  

![Instalator programu Visual Studio](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Przewiń w dół do gier & przenośnych i włączanie **programowania aplikacji mobilnych z platformą .NET**, jeśli nie jest już włączony.

![Instalator programu Visual Studio z wybrane platformy Xamarin.Forms](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Na koniec kliknij **Modyfikuj** w prawym dolnym rogu okna i poczekaj program Xamarin, aby zainstalować.

### <a name="installing-xamarin-on-macos"></a>Instalowanie na macOS Xamarin
Xamarin pochodzi opakowanych z programem Visual Studio dla komputerów Mac. Instalacja nie powinny być jest wymagana.

## <a name="building-and-running-the-app"></a>Tworzenie i uruchamianie aplikacji

Plik rozwiązania Visual Studio *(.sln)* Visual wyszukiwania można pobrać aplikacji z [Visual aplikacji wyszukiwania z usługami kognitywnych](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Można pobrać archiwum ZIP za pomocą przeglądarki sieci Web, sklonować go do stacji roboczej z usługi GitHub lub pobrać za pomocą programu Visual Studio.

Aby rozpocząć pracę z próbką, otwórz `VisualSearchApp.sln` w programie Visual Studio.  Podczas inicjowania składnika może potrwać kilka minut.

Aplikacja wymaga dwóch bibliotek innych firm: **Json.NET** i **dodatek Media Xamarin**. Należy zainstalować te prawa bibliotek w programie Visual Studio z Menedżera pakietów NuGet. Wybierz **Narzędzia > Menedżera pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania**, lub kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj pakietami NuGet** z menu kontekstowego.

W oknie NuGet wyszukiwanie i instalowanie **dodatek Xamarin Media** (Xam.Plugin.Media) wersja 2.6.2 i **Json.NET** (Newtonsoft.Json) 10.0.3. Należy wybrać wszystkie projekty, podczas instalacji.

Aby utworzyć aplikację dla wszystkich dostępnych platform, naciśnij klawisz **Ctrl + Shift + B**, lub kliknij przycisk **kompilacji** menu Wstążki i wybierz polecenie **Kompiluj rozwiązanie**.  Aby skompilować i testowania kodu dla systemu iOS z systemem Windows, zobacz [w tym przewodniku](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) Aby uzyskać pomoc.

Przed uruchomieniem aplikacji, musisz wybrać docelowy konfiguracji, platformy i projektu.  Aplikacje platformy Xamarin.Forms skompiluj do kodu natywnego dla systemu Windows, Android i iOS. W tym samouczku opisano zrzuty ekranu próbki wersji systemu Windows, ale wszystkie wersje działają tak samo. Ustawienia wdrożenia używanych w tym przewodniku są wyświetlane tutaj.  

![Visual Studio skonfigurowany tak, aby skompilować dla telefonów z systemem Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Po pomyślnym zakończeniu procesu kompilacji i wybrano platformę docelową, kliknij przycisk **Start** przycisku w pasku narzędzi lub naciśnij **F5**. Visual Studio wdraża rozwiązanie na docelowej platformy i go uruchamia.

Zostanie wyświetlona strona Dodawanie kluczy. (Ta strona jest zdefiniowany w `AddKeysPage.xaml`.)  

![Obraz dodawania kluczy strony](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

W tym miejscu Wklej klucze komputera wizji i interfejsu API Bing sieci Web wyszukiwania. Interfejs API wizji komputera wymaga również wybierz serwer, na którym jest hostowany punkt końcowy.

> [!TIP]
> Aby przejść do tej strony, wprowadź te informacje w odpowiednich miejscach w `App.xaml.cs`. 

Aplikacja weryfikuje klucze, wykonując zapytanie testu, następnie będzie można wybrać Rozpoznawania strony (zdefiniowany w `OcrSelectPage.xaml`).
   
![Obraz strony wybierz opcję Rozpoznawania](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

U góry tego ekranu można wybrać, czy tekst, który ma być rozpoznaje jest drukowany lub odręcznie.

> [!TIP]
> Zawiera element, z którego użytkownik próbuje rozpoznać tekstu możliwie jak to możliwe i upewnij się, że równomiernie jest włączone z nie odbić. Wykryto, że Rozpoznawania pisma ręcznego czasami działa lepiej czcionki skrypt lub inny tekst "ozdobne".

Następnie kliknij przycisk **zająć fotografii** lub **fotografii importu** Zrób zdjęcie przy użyciu aparatu urządzenia lub Wybierz zdjęcie przechowywane na urządzeniu.

Po zdjęcie jest poświęcony lub wybrany, obraz jest przekazywany do interfejsu API przetwarzania obrazów komputera. Znaleziono słów strony (zdefiniowany w `OcrResultsPage.xaml`) Wyświetla słów rozpoznane w obrazie.

![Obraz strony wyników Rozpoznawania](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> Obraz użyliśmy dla tych wyników znajduje się w repozytorium źródłowe jako `SamplePhotos\TestImage.jpg`.

Po kliknięciu elementu na znaleziono słów strony, strony sieci Web wyników (`WebResultsPage.xaml`) zostanie wyświetlony, wyświetlając górnej Bing wyników dla tego wyszukiwania.

![Obraz strony sieci Web, wyniki](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Na koniec wybierz element na stronę wyników sieci Web, aby otworzyć łącze w osadzonych widoku sieci Web. (Lub przejdź wstecz, aby wybrać inny wynik.)

![Obraz strony widoku sieci Web](./media/computer-vision-web-search-tutorial/web-view-page.png)  

W dowolnej przeglądarki sieci Web lub przejdź do strony sieci Web, wyniki mogą współdziałać ze stroną. 

## <a name="review-and-learn"></a>Przejrzyj i Dowiedz się więcej

Teraz, gdy zajęło aplikacji Visual wyszukiwania dla pokrętła, Przyjrzyjmy się dokładnie sposób firma Microsoft korzysta z dwóch Microsoft kognitywnych usługi interfejsów API.  Czy używasz tego przykładu jako punktu wyjścia dla swojej aplikacji lub jako instrukcje dla interfejsów API, jest przydatna do przeszukania za pośrednictwem aplikacji ekran po ekranie, aby zbadać dokładnie, jak to działa.

### <a name="add-keys-page"></a>Dodaj stronę kluczy
Strona Dodawanie kluczy interfejsu użytkownika jest zdefiniowany w `AddKeysPage.xaml`, a jej logiki podstawowy jest zdefiniowany w `AddKeysPage.xaml.cs`. Ponieważ klucze są weryfikowane przez żądanie testu, czas jest dojrzałe w celu ustalenia sposobu używania punktów końcowych usługi kognitywnych w języku C#.  

Podstawowa struktura interakcji jest: 

1. Utwórz wystąpienie *HttpResponseMessage* i *HttpClient* obiektów z *System.Net.Http*
2. Dołącz wszystkie żądane nagłówki (zdefiniowany w odwołaniu do każdego punktu końcowego interfejsu API) do *HttpClient* obiektu
3. Wyślij żądanie GET lub POST z danymi, dodanie jakichkolwiek parametrów niezbędne do identyfikatora URI punktu końcowego
4. Sprawdź, czy odpowiedź zakończyła się pomyślnie
5. Podaj odpowiedź dla dalszego przetwarzania

Funkcja, która sprawdza poprawność klucz interfejsu API wyszukiwania usługi Bing jest `CheckBingSearchKey()`, pokazano poniżej.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Podobną funkcję, `CheckComputerVisionKey()`, jest używany do sprawdzania poprawności klucza wizji komputera.

### <a name="ocr-select-page"></a>Wybierz opcję Rozpoznawania strony

Na stronie wybierz Rozpoznawania (`OcrSelectPage.xaml`) dostępne są dwie role ważne. Najpierw określa rodzaj Rozpoznawania jest wykonywana na fotografii docelowej. Po drugie powiadamia użytkownika przechwytywania lub Otwórz obraz, który ma być przetworzyć.

Drugie zadanie jest tradycyjnie skomplikowane w aplikacji i platform, ponieważ każdej z platform wymaga inny kod. Dodatek Media Xamarin obsługuje go przy użyciu kilku wierszy kodu.

Następująca funkcja zawiera przykład użycia Xamarin wtyczki nośnika przechwytywania zdjęcie.  W ramach tego firma Microsoft:

1. Upewnij się, że aparatu jest dostępny na bieżącego urządzenia
2. Utwórz wystąpienie `StoreCameraMediaOptions` obiekt, aby określić miejsce zapisania przechwyconego obrazu
3. Przechwytywanie obrazu i uzyskiwanie `MediaFile` obiekt zawierający dane obrazu
4. Rozpakuj `MediaFile` do tablicy typu byte
5. Zwraca tablicę bajtów dla dalszego przetwarzania

Oto `TakePhoto()`, funkcji, która używa Xamarin wtyczki nośnika przechwytywania zdjęcie.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
Zdjęcie zaimportować narzędzie działa w podobny sposób. Oba elementy funkcji można znaleźć w `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> Punkt końcowy odręcznie Rozpoznawania może obsługiwać tylko zdjęć, które są mniejsze niż 4 MB. Aby uniknąć problemów z rozmiarem plików, firma Microsoft zmniejsza zdjęcie do 50% jego oryginalny rozmiar przez ustawienie opcji `PhotoSize = PhotoSize.Medium` na `StoreCameraMediaOptions` obiektu.  Jeśli urządzenie ma wyjątkowo wysokiej jakości zdjęć i występują błędy, możesz spróbować `PhotoSize = PhotoSize.Small` zamiast tego.

W tym miejscu jest to funkcja narzędzia służący do konwertowania *MediaFile* tablicy bajtów.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Strona wyników Rozpoznawania

Strona wyników Rozpoznawania Wyświetla tekst wyodrębniony z obrazu przy użyciu **Json.NET** [metody SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm).  Dwa punkty końcowe Rozpoznawania działać nieco inaczej, dlatego jest przydatna do omówienia zarówno.  

Ponieważ API wizji komputer znajduje się tylko w kilku lokalizacjach serwera, punktu końcowego musi być skonstruowany dynamicznie w czasie wykonywania. Funkcja `SetOcrLocation` (część statycznych *AppConstants* znaleziono klasy w `AppConstants.cs`) Ustawia lokalizację URI punktu końcowego. Odpowiada wybranej przez użytkownika na stronie Dodawanie kluczy, lub wartość ustawiona używana `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Spójrzmy bliższe spojrzenie na te żądania interfejsu API. API Rozpoznawania wizji komputer jest w stanie analizowanie tekstu z nieokreślonej języka, ale możemy stwierdzić, aby wyszukać tekst w języku angielskim do poprawy wyników. Nie możemy udostępnić również orientację tekstu w firmie Microsoft wykrywać interfejsu API. Przy użyciu stałej orientacji może zwiększyć naszych wyniki analizy, ale wykrywanie orientacji mogą być przydatne w aplikacji mobilnej.

Dowiedz się więcej o parametry dostępne z tego punktu końcowego z [dokumentacja interfejsu API OCR drukowania](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

Interfejs API Rozpoznawania odręcznie jest dostępny w wersji zapoznawczej i aktualnie działa tylko tekst w języku angielskim.  Z tego powodu jej tylko parametr jest obecnie flagę wskazującą, czy nie można przeanalizować tekstu ręcznych w ogóle. Odręcznie API Rozpoznawania można analizować zarówno komputera tekstu wydruku i ręcznych, ale `handwriting=false` wydajności lepsze wyniki w tekście wydruku. 

Ponieważ ta aplikacja jest w języku angielskim, firma Microsoft może mieć tylko odręcznie Rozpoznawania dla tego przykładu i skonfigurować `handwriting` flagi zgodnie z co użytkownik informuje, nam do rozpoznania.  My używamy oba punkty końcowe w celach ilustracyjnych.  

Dowiedz się więcej o parametry dostępne z tego punktu końcowego z [odręcznie dokumentacja interfejsu API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Teraz Przyjrzyjmy się funkcji, które wywołują te interfejsy API.

`FetchPrintedWordList()` używa punktu końcowego Rozpoznawania wydruku można przeanalizować tekstu wydruku z obrazów. Żądanie HTTP następuje strukturę podobną do połączenia, który pozwala na stronie Dodawanie kluczy sprawdzanie poprawności klucza, ale musimy wysłać zdjęcie. Zdjęcie jest zbyt duży, aby przekazać w ciągu zapytania, więc używamy żądania POST protokołu HTTP zamiast żądanie GET. Musimy kodowania naszych zdjęcie (który znajduje się w pamięci w postaci tablicy bajtów) do `ByteArrayContent` obiektu i dodanie nagłówka do tego obiektu definiujący typ danych została wysłana. 

Informacje o dopuszczalne typy zawartości w [API przetwarzania obrazów komputera odniesienia](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Należy zwrócić uwagę, jak firma Microsoft korzysta z **Json.NET** [metody SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) można wyodrębnić tekst z obiektu odpowiedzi. `SelectToken` Potrzebujemy określoną część odpowiedzi JSON, które firma Microsoft może następnie przekazania funkcji next, jest idealny. W innych częściach kodu JSON odpowiedzi możemy deserializacji do modelu obiektów zdefiniowanych w `ModelObjects.cs`.

Podstawowa różnica między Rozpoznawania drukowania i Rozpoznawania odręcznie żądania usługi drukowania Rozpoznawania zwracanych jest rozpoznany jako część odpowiedzi HTTP, gdy usługa Rozpoznawania odręcznie wymaga dodatkowego żądania, aby uzyskać informacje. Początkowe żądanie odręcznie Rozpoznawania zwraca stan HTTP 202, który tylko sygnalizuje, że przetwarzanie zostało uruchomione. Odpowiedź zawiera punktu końcowego, który klient musi sprawdzić uzyskanie ukończonej odpowiedzi, gdy jest ona dostępna. Zobacz `FetchHandwrittenWordList()` do sprawdzenia jego wszystkie działania.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` jest drugiej części proces Rozpoznawania pisma ręcznego. Pakiety usługi ping URI wyodrębniony z metadanych wstępnej reakcji albo dopóki wynik uzyskuje się lub upłynął limit czasu funkcji.  Należy pamiętać, że ta funkcja jest wywoływana asynchronicznie w jego własnym wątku. W przeciwnym razie ta metoda może zablokować interfejsu użytkownika momentu zakończenia przetwarzania.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Wyniki stronę sieci Web
Po wybraniu terminu wyszukiwania wyświetlany na stronie wyników Rozpoznawania możemy przesunąć wzdłuż do strony sieci Web wyniki.  W tym miejscu możemy utworzyć [interfejsu API Bing sieci Web wyszukiwania](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) żądania, wysyłają je do punktu końcowego usługi i deserializacji za pomocą odpowiedzi JSON **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) metody.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

Interfejs API wyszukiwania usługi Bing sieci Web działa najlepiej, gdy Podaj jak najwięcej informacji o co użytkownik może być. W szczególności prawie zawsze należy używać `mkt` i `setLang` ma parametrów (w tym miejscu ustaw dla tego języka) do identyfikowania lokalizacji użytkownika i określ preferowany język.

> [!NOTE]
> Firma Microsoft uproszczone zapytanie wyszukiwania usługi Bing w sieci Web w celu upewnij się, że kod źródłowy został łatwy do zrozumienia.  W rzeczywistej aplikacji należy dodać następujące nagłówki na żądanie HTTP, aby uzyskać lepsze wyniki. 
> * Agent użytkownika  
> * X-MSEdge-ClientID  
> * ClientIP-X-wyszukiwania  
> * X wyszukiwania lokalizacji  
>
> Dowiedz się więcej na temat tych wartości nagłówka w [dokumentacja interfejsu API wyszukiwania usługi Bing w sieci Web](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>Następne kroki
Kognitywnych usług firmy Microsoft Podaj wiele dodatkowych usług, które można łatwo zintegrować tej aplikacji.  Na przykład można:

* Dodaj [wyszukiwania usługi Bing jednostki](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) można rozszerzyć wyników wyszukiwania w sieci web
* Zamień w [wyszukiwania usługi Bing niestandardowe](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) zamiast wyszukiwania usługi Bing w sieci Web
* Użyj [wyszukiwania usługi Bing obrazu](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) obrazu możliwości szczegółowe informacje, aby dowiedzieć się więcej o przechwycony obraz i znaleźć podobne obrazy w sieci web
* Stosować [sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) do dalszego poprawić jakość przeanalizowany tekst
* Integracja [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) do wyświetlenia wyodrębnionego tekstu w różnych językach
* Mieszać i dopasowywać innych usług z [kognitywnych Portal usługi](https://azure.microsoft.com/services/cognitive-services/); niebo jego limit!
