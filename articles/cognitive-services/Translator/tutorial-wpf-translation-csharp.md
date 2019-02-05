---
title: 'Samouczek: Tworzenie aplikacji WPF w języku C# — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację WPF w języku C#, aby dowiedzieć się, jak tłumaczyć tekst, pobierać zlokalizowaną listę obsługiwanych języków i wykonywać inne operacje przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: erhopf
ms.openlocfilehash: f9fe1f40f758ead06c1e86fc0daeb64b72c5fd8b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476506"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>Samouczek: Pisanie aplikacji WPF dla interfejsu API tłumaczenia tekstu w usłudze Translator w języku C&#35;

W tym samouczku utworzysz interakcyjne narzędzie do tłumaczenia tekstu, używając interfejsu API tłumaczenia tekstu w usłudze Translator (wersja 3), który jest częścią usług Microsoft Cognitive Services na platformie Azure. Omawiane tematy:

> [!div class="checklist"]
> * Pobieranie listy języków obsługiwanych przez usługę
> * Tłumaczenie tekstu wprowadzonego przez użytkownika na inny język

Ta aplikacja obsługuje też integrację funkcji z dwóch innych usług Microsoft Cognitive Services.

|||
|-|-|
|[Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Umożliwia opcjonalne automatyczne wykrywanie języka źródłowego tekstu do tłumaczenia|
|[Sprawdzanie pisowni Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|W przypadku tekstów źródłowych w języku angielskim umożliwia poprawianie błędów pisowni, aby tłumaczenie było dokładniejsze

![[Uruchomiony program samouczka]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchamiania tego kodu w systemie Windows jest potrzebny [program Visual Studio 2017](https://www.visualstudio.com/downloads/). (Można korzystać z bezpłatnej wersji Community Edition).

Są też potrzebne klucze subskrypcji dla trzech usług platformy Azure używanych w programie. Klucz usługi tłumaczenia tekstu w usłudze Translator można uzyskać z poziomu pulpitu nawigacyjnego platformy Azure. Dostępna jest bezpłatna warstwa cenowa, która umożliwia tłumaczenie maksymalnie dwóch milionów znaków miesięcznie bez opłat.

Zarówno usługa analizy tekstu, jak i usługa sprawdzania pisowni Bing oferują bezpłatne wersje próbne, z których możesz skorzystać po zarejestrowaniu się na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Możesz też utworzyć subskrypcję dowolnej z tych usług za pośrednictwem pulpitu nawigacyjnego platformy Azure. Usługa analizy tekstu oferuje warstwę bezpłatną.

Kod źródłowy dla tego samouczka jest dostępny poniżej. Klucze subskrypcji należy skopiować do kodu źródłowego jako zmienne `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` i tak dalej w pliku `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Usługa analizy tekstu jest dostępna w wielu regionach. Identyfikator URI w kodzie źródłowym tego samouczka należy do regionu `westus`, który jest używany w bezpłatnych wersjach próbnych. Jeśli masz subskrypcję w innym regionie, odpowiednio zaktualizuj ten identyfikator URI.

## <a name="source-code"></a>Kod źródłowy

Oto kod źródłowy dla interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator. Aby uruchomić aplikację, skopiuj kod źródłowy do odpowiedniego pliku w nowym projekcie WPF w programie Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Jest to plik związany z kodem, który udostępnia funkcje aplikacji.

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Ten plik definiuje interfejs użytkownika dla aplikacji (formularz WPF). Jeśli chcesz zaprojektować własną wersję formularza, nie potrzebujesz tego kodu XAML.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox"
                HorizontalAlignment="Left"
                Margin="306,88,0,0"
                VerticalAlignment="Top"
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox"
            HorizontalAlignment="Left"
            Margin="42,88,0,0"
            VerticalAlignment="Top"
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Punkty końcowe usługi

Usługa Microsoft Translator ma różne punkty końcowe, które zapewniają różne funkcje tłumaczenia. W tym samouczku są używane następujące:

|||
|-|-|
|`Languages`|Zwraca zestaw języków, które są obecnie obsługiwane przez inne operacje interfejsu API tłumaczenia tekstu w usłudze Translator.|
|`Translate`|Dla danego tekstu źródłowego, kodu języka źródłowego i kodu języka docelowego zwraca tłumaczenie tekstu źródłowego na język docelowy.|

## <a name="the-translation-app"></a>Aplikacja tłumaczenia

Interfejs użytkownika aplikacji tłumaczenia utworzono przy użyciu platformy Windows Presentation Foundation (WPF). Utwórz nowy projekt WPF w programie Visual Studio, wykonując następujące kroki.

* W menu **Plik** wybierz pozycję **Nowy > Projekt**.
* W oknie nowego projektu otwórz pozycję **Zainstalowane > Szablony > Visual C#**. Pośrodku okna dialogowego zostanie wyświetlona lista dostępnych szablonów projektów.
* Upewnij się, że jest wybrana pozycja **.NET Framework 4.5.2** w menu rozwijanym powyżej listy szablonów projektów.
* Kliknij pozycję **Aplikacja WPF (.NET Framework)** na liście szablonów projektów.
* Używając pól w dolnej części okna dialogowego, określ nazwę nowego projektu i rozwiązania, do którego on należy.
* Kliknij pozycję **OK**, aby utworzyć nowy projekt i rozwiązanie.

![[Tworzenie nowej aplikacji WPF w programie Visual Studio]](media/translator-text-csharp-new-project.png)

Dodaj do projektu odwołania do następujących zestawów .NET Framework.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Ponadto zainstaluj pakiet NuGet `Newtonsoft.Json` w projekcie.

Znajdź plik `MainWindow.xaml` w Eksploratorze rozwiązań i otwórz go. Początkowo jest on pusty. Oto jak powinien wyglądać gotowy interfejs użytkownika. Nazwy kontrolek oznaczono kolorem niebieskim. Użyj tych samych nazw kontrolek w swoim interfejsie użytkownika, ponieważ występują one też w kodzie.

![[Widok głównego okna w projektancie programu Visual Studio oznaczony adnotacjami]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Kod źródłowy dla tego samouczka zawiera kod źródłowy XAML tego formularza. Możesz go wkleić do projektu, zamiast tworzyć formularz w programie Visual Studio.

* `FromLanguageComboBox` *(ComboBox)*  — wyświetla listę języków tłumaczenia tekstu obsługiwanych przez usługę Microsoft Translator. Użytkownik wybiera źródłowy język tłumaczenia.
* `ToLanguageComboBox` *(ComboBox)* — wyświetla tę samą listę języków co kontrolka `FromComboBox`, ale służy do wybierania docelowego języka tłumaczenia.
* `TextToTranslate` *(TextBox)* — tutaj użytkownik wprowadza tekst do przetłumaczenia.
* `TranslateButton` *(Button)* — użytkownik klika ten przycisk (lub naciska klawisz Enter), aby przetłumaczyć tekst.
* `TranslatedTextLabel` *(Label)* — tutaj pojawia się tłumaczenie tekstu użytkownika.

Jeśli tworzysz własną wersję tego formularza, nie musi on być *dokładnie* taki sam jak użyty tutaj. Jednak upewnij się, że listy rozwijane języków są dostatecznie szerokie, aby uniknąć ucięcia nazw języków.

## <a name="the-mainwindow-class"></a>Klasa MainWindow

Plik związany z kodem `MainWindow.xaml.cs` zawiera kod określający działanie programu. Operacje są wykonywane w dwóch momentach:

* Po uruchomieniu programu i utworzeniu klasy `MainWindow` pobiera ona listę języków przy użyciu interfejsów API usługi Translator i wypełnia na tej podstawie menu rozwijane. To zadanie jest wykonywane raz, na początku każdej sesji.

* Gdy użytkownik klika przycisk **Translate** (Przetłumacz), pobierane są języki wybrane przez użytkownika i wprowadzony przez niego tekst, a następnie jest wywoływany interfejs API `Translate` w celu wykonania tłumaczenia. Mogą być też wywoływane inne funkcje w celu ustalenia języka tekstu i poprawienia jego pisowni przed tłumaczeniem.

Przyjrzyj się początkowej części klasy:

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Zadeklarowane tutaj dwie zmienne składowe przechowują informacje o językach:

|||
|-|-|
|`languageCodes`<br>tablica ciągów|Buforuje kody języków. Usługa Translator identyfikuje języki za pomocą krótkich kodów, takich jak `en` dla języka angielskiego.|
|`languageCodesAndTitles`<br>SortedDictionary|Mapuje „przyjazne” nazwy w interfejsie użytkownika z powrotem na krótkie kody używane w interfejsie API. Jest posortowana alfabetycznie, bez uwzględniania wielkości liter.|

Pierwszym kodem wykonywanym przez aplikację jest konstruktor klasy `MainWindow`. Najpierw jest konfigurowana metoda `HandleExceptions` jako globalna procedura obsługi błędów. Zapewnia to wyświetlanie alertu o błędzie, gdy wyjątek nie jest obsługiwany.

Następnie jest wykonywane sprawdzenie, czy wszystkie klucze subskrypcji interfejsu API mają długość dokładnie 32 znaków. Jeśli tak nie jest, najprawdopodobniejszą przyczyną jest to, że *ktoś* nie wkleił kluczy interfejsu API. W takim przypadku jest wyświetlany komunikat o błędzie i program kończy działanie. (Pomyślny wynik tego testu oczywiście nie oznacza, że klucze są prawidłowe).

Jeśli istnieją klucze o poprawnej długości, wywołanie funkcji `InitializeComponent()` uruchamia interfejs użytkownika przez zlokalizowanie, załadowanie i utworzenie wystąpienia opisu XAML dla głównego okna aplikacji.

Na koniec są konfigurowane menu rozwijane języków. To zadanie wymaga trzech osobnych wywołań metod, które opisano szczegółowo w poniższych sekcjach.

## <a name="get-supported-languages"></a>Uzyskiwanie obsługiwanych języków

W czasie pisania tego dokumentu usługa Microsoft Translator obsługiwała łącznie 61 języków, a z czasem mogą być dodawane inne języki. Dlatego najlepiej jest nie umieszczać stałej listy obsługiwanych języków w programie. Zamiast tego należy wysłać do usługi Translator zapytanie o obsługiwane przez nią języki. Każdy obsługiwany język można tłumaczyć na dowolny inny obsługiwany język.

Wywołaj interfejs API `Languages`, aby uzyskać listę obsługiwanych języków.

Interfejs API `Languages` ma opcjonalny parametr zapytania GET: *scope*. Parametr *scope* może mieć jedną z trzech wartości: `translation`, `transliteration` i `dictionary`. Ten kod używa wartości `translation`.

Interfejs API `Languages` ma też opcjonalny parametr dotyczący nagłówka HTTP: `Accept-Language`. Wartość tego nagłówka określa język, w którym są zwracane nazwy obsługiwanych języków. Ta wartość powinna być poprawnie sformułowanym tagiem języka BCP 47. Ten kod używa wartości `en`, aby pobrać nazwy języków w języku angielskim.

Interfejs API `Languages` zwraca odpowiedź JSON, która wygląda podobnie do następującej.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Aby wyodrębnić kody języków (na przykład `af`) i nazwy języków (na przykład `Afrikaans`), ten kod używa metody pakietu NewtonSoft.Json o nazwie [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm).

Po zapoznaniu się z tą ogólną wiedzą utwórz następującą metodę w celu pobierania kodów i nazw języków.

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

Metoda `GetLanguagesForTranslate()` najpierw tworzy żądanie HTTP. Parametr ciągu zapytania `scope=translation` określa żądanie tylko języków obsługiwanych w tłumaczeniach tekstu. Klucz subskrypcji interfejsu API tłumaczenia tekstu jest dodawany do nagłówków żądań. Dodawany jest nagłówek `Accept-Language` z wartością `en`, aby obsługiwane języki były zwracane w języku angielskim.

Po zakończeniu żądania odpowiedź JSON jest analizowana i konwertowana na słownik, a następnie kody języków są dodawane do zmiennej składowej `languageCodes`. Pary klucz/wartość, które zawierają kody języków i przyjazne nazwy języków, są przetwarzane w pętli i dodawane do zmiennej składowej `languageCodesAndTitles`. (Menu rozwijane w formularzu zawierają przyjazne nazwy, ale do żądania tłumaczeń są potrzebne kody).

## <a name="populate-the-language-menus"></a>Wypełnianie menu języków

Większość interfejsu użytkownika jest zdefiniowana w kodzie XAML, dzięki czemu skonfigurowanie go nie wymaga wielu operacji, oprócz wywołania funkcji `InitializeComponent()`. Jedyną operacją, którą należy jeszcze wykonać, jest dodanie przyjaznych nazw języków do list rozwijanych **Translate from** (Tłumacz z) i **Translate to** (Tłumacz na), co jest wykonywane w funkcji `PopulateLanguageMenus()`.

```csharp
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Wypełnianie menu jest prostą operacją, polegającą na iteracyjnym przetworzeniu słownika `languageCodesAndTitles` i dodaniu każdego klucza (który jest „przyjazną” nazwą) do obu menu. Gdy menu zostaną wypełnione, domyślne języki docelowy i źródłowy są ustawiane na **Detect** (Wykryj) — w celu automatycznego wykrywania języka — oraz **English** (Angielski).

> [!TIP]
> Bez domyślnie wybranych opcji menu użytkownik mógłby kliknąć przycisk **Translate** bez uprzedniego wybrania języka docelowego lub źródłowego. Ustawienia domyślne eliminują konieczność obsługi tego problemu.

Po zainicjowaniu klasy `MainWindow` i utworzeniu interfejsu użytkownika kod czeka, aż użytkownik kliknie przycisk **Translate**.

## <a name="perform-translation"></a>Tłumaczenie

Kiedy użytkownik kliknie przycisk **Translate**, platforma WPF wywołuje program obsługi zdarzeń `TranslateButton_Click()`, pokazany poniżej.

```csharp
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Pierwszym krokiem jest pobranie z formularza języka docelowego i języka źródłowego wraz z tekstem wprowadzonym przez użytkownika.

Jeśli dla języka źródłowego jest ustawiona opcja **Detect**, wywoływana jest funkcja `DetectLanguage()` w celu ustalenia języka tekstu. Tekst może być w języku nieobsługiwanym przez interfejsy API usługi Translator (jest znacznie więcej wykrywanych języków niż tłumaczonych języków) albo interfejs API analizy tekstu może nie być w stanie go wykryć. W takim przypadku jest wyświetlany komunikat z informacją dla użytkownika i następuje powrót bez tłumaczenia.

Jeśli językiem źródłowym jest angielski (określony lub wykryty), sprawdzana jest pisownia tekstu za pomocą funkcji `CorrectSpelling()` i stosowane są wszelkie poprawki. Poprawiony tekst jest wstawiany z powrotem w polu wejściowym, aby użytkownik wiedział, że wprowadzono poprawki. (Użytkownik może poprzedzić tłumaczony tekst łącznikiem, aby wyłączyć korektę pisowni).

Jeśli użytkownik nie wprowadził tekstu albo jeśli języki docelowy i źródłowy są jednakowe, tłumaczenie nie jest konieczne i można pominąć żądanie.

Kod wykonujący żądanie tłumaczenia powinien wyglądać znajomo: utworzenie identyfikatora URI, utworzenie żądania, wysłanie go i przeanalizowanie odpowiedzi. W celu wyświetlenia tekst jest wysyłany do kontrolki `TranslatedTextLabel`.

Następnie tekst jest przekazywany do interfejsu API `Translate` w serializowanej tablicy JSON w treści żądania POST. Tablica JSON może zawierać wiele części tekstu do przetłumaczenia, ale tutaj jest wymagana tylko jedna.

Nagłówek HTTP o nazwie `X-ClientTraceId` jest opcjonalny. Wartość powinna być identyfikatorem GUID. Identyfikator śledzenia dostarczany przez klienta jest przydatny do śledzenia żądań, gdy coś nie działa zgodnie z oczekiwaniami. Jednak aby wartość X-ClientTraceID była użyteczna, musi być rejestrowana przez klienta. Identyfikator śledzenia klienta i data żądań mogą pomóc firmie Microsoft w diagnozowaniu potencjalnych problemów.

> [!NOTE]
> Ten samouczek jest poświęcony usłudze Microsoft Translator, więc nie opisano szczegółowo metod `DetectLanguage()` i `CorrectSpelling()`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
