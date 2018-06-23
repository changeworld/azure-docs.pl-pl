---
title: Jak napisać aplikację Microsoft Translator WPF, języku C#, kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi tekst Translator tłumaczenie tekstu, Pobierz zlokalizowanych listę obsługiwanych języków i inne.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347685"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Jak napisać aplikację Microsoft Translator WPF w języku C#

W tym samouczku, firma Microsoft będzie interakcyjne tekstu tłumaczenia narzędzie kompilacji przy użyciu Microsoft Translator tekst interfejsu API (V3), część kognitywnych usług Microsoft Azure. Dowiesz się, jak:

> [!div class="checklist"]
> * Żądanie listy krótkich kodów języków obsługiwanych przez usługę
> * Pobieranie listy nazw zlokalizowanego języka odpowiadający tych kodów języków
> * Uzyskać wersję przetłumaczonego tekstu wprowadzonych przez użytkownika z jednego języka do innego

Program ten zawiera również integrację z dwóch innych usług firmy Microsoft kognitywnych.

|||
|-|-|
|[Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Opcjonalnie automatycznie wykrywać języka źródłowego tekstu do tłumaczenia|
|[Moduł sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Dla angielskiej wersji językowej źródła pole tekstowe, Popraw pisownię, więc tłumaczenia jest dokładniejsze

![[Samouczek program uruchomiony]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku, należy dowolnej wersji programu Visual Studio 2017 r, w tym Community Edition.

Należy również klucze subskrypcji dla trzech usług Azure w programie. Na pulpicie nawigacyjnym platformy Azure można uzyskać klucza dla usługi Translator tekstu. Bezpłatne warstwa cenowa jest dostępna co umożliwia tłumaczenie do milionów dwa znaki miesięcznie bez dodatkowych opłat.

Analiza tekstu i sprawdzania pisowni usługi Bing usług oferty bezpłatnych wersji próbnych, które możesz zarejestrować się na [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/). Można również utworzyć subskrypcji dla każdej usługi za pośrednictwem pulpitu nawigacyjnego platformy Azure. Analiza tekstu ma warstwę bezpłatna.

Kod źródłowy dla tego samouczka jest dostępne poniżej. Klucze subskrypcji musi być skopiowany do kodu źródłowego jako zmienne `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`i tak dalej w `MainWindow.xaml.cs`.

> [!IMPORTANT]
> W wielu regionach jest dostępna usługa Analiza tekstu. Identyfikator URI w naszym kodzie źródłowym samouczka trwa `westus` regionu, który jest region używane bezpłatnej wersji próbnych. Jeśli masz subskrypcję w innym regionie, w związku z tym aktualizacji tego identyfikatora URI.

## <a name="source-code"></a>Kod źródłowy

To jest kod źródłowy dla tekstu Microsoft Translator interfejsu API. Aby uruchomić aplikację, skopiuj kod źródłowy do odpowiedniego pliku w nowym projekcie WPF w programie Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Jest to plik CodeBehind, który udostępnia funkcjonalność aplikacji.

```cs
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
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
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
            // at least show an error dialog when we get an unexpected error
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

            // read and and parse JSON response
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

Ten plik definiuje interfejs użytkownika dla aplikacji, formularz WPF. Chcąc projektowania wersji formularza, nie trzeba tego XAML.

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

Usługa Microsoft Translator ma wiele punktów końcowych, które udostępniają różne funkcje tłumaczenia. Te używanego w tym samouczku są:

|||
|-|-|
|`Languages`|Zwraca zestaw języków, które są obecnie obsługiwane przez inne operacje interfejsu API tekst translatora.|
|`Translate`|Podany tekst źródłowy, kod języka źródła i kodu języka docelowego, zwraca tłumaczenie tekstu źródłowego języka docelowego.|

## <a name="the-translation-app"></a>Aplikacja tłumaczenia

Interfejs użytkownika w naszej aplikacji translator jest utworzony przy użyciu systemu Windows Presentation Foundation (WPF). Utwórz nowy projekt WPF w programie Visual Studio, wykonując następujące kroki.

* Z **pliku** menu, wybierz **nowy > Projekt**.
* Otwórz okno nowy projekt **zainstalowana > Szablony > Visual C#**. W Centrum w oknie dialogowym zostanie wyświetlona lista szablonów dostępnych projektów.
* Upewnij się, że **.NET Framework 4.5.2** wybrano opcję w menu rozwijanym powyżej listy szablonów projektu.
* Kliknij przycisk **WPF aplikacji (.NET Framework)** na liście szablonów projektu.
* Za pomocą pola w dolnej części okna dialogowego, nazwę nowego projektu i rozwiązania, który go zawiera.
* Kliknij przycisk **OK** do tworzenia nowego projektu i rozwiązania.

![[Tworzenie nowej aplikacji WPF w programie Visual Studio]](media/translator-text-csharp-new-project.png)

Dodaj odwołania do następujących zestawów platformy .NET do projektu.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Ponadto zainstaluj pakiet NuGet `Newtonsoft.Json` do projektu.

Teraz znaleźć `MainWindow.xaml` pliku w Eksploratorze rozwiązań i otwórz go. Początkowo jest ona pusta. Oto, co interfejsu użytkownika końcowego powinna wyglądać, adnotacjami nazw kontrolek w kolorze niebieskim. Użyj tych samych nazw kontrolek w interfejsie użytkownika, ponieważ pojawią się również w kodzie.

![[Adnotacjami widoku głównego okna w projektancie programu Visual Studio]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Kod źródłowy dla tego samouczka zawiera źródła XAML dla tego formularza. Może wklej go do projektu zamiast tworzenia formularzy w programie Visual Studio.

* `FromLanguageComboBox` *(ComboBox)*  -Wyświetla listę języków obsługiwanych przez Microsoft Translator dla tłumaczenie tekstu. Gdy użytkownik wybierze język, w którym są one tłumaczenia z.
* `ToLanguageComboBox` *(ComboBox)*  -Wyświetla tę samą listę języków jako `FromComboBox`, ale jest używana do wybierania użytkownika jest tłumaczenia na język.
* `TextToTranslate` *(Pole tekstowe)*  — Użytkownik wprowadza tekst, który można przekształcić w tym miejscu.
* `TranslateButton` *(Przycisk)*  — Użytkownik kliknie przycisk (lub naciśnie klawisz Enter) umożliwia tłumaczenie tekstu.
* `TranslatedTextLabel` *(Etykieta)*  -Translacja tekst użytkownika pojawia się tutaj.

Jeśli wprowadzasz wersji tego formularza, nie jest niezbędne do jego *dokładnie* , takich jak nasza. Ale upewnij się, że listy rozwijane języka są dostatecznie szerokie, aby uniknąć Wycinanie poza nazwę języka.

## <a name="the-mainwindow-class"></a>Klasy okna głównego

Plik CodeBehind `MainWindow.xaml.cs` jest, gdzie kod przechodzi dzięki temu program wykonaj jego działania. Praca odbywa się na dwa razy:

* Po uruchomieniu programu. Podczas `MainWindow` jest uruchomiony, pobieranie listy języka przy użyciu translatora `GetLanguagesForTranslate` i `GetLanguageNames` interfejsów API i wypełnić naszych menu rozwijane z nimi. To zadanie jest wykonywane raz, na początku każdej sesji.

* Po kliknięciu przez użytkownika **Przetłumacz** przycisku Pobieramy wybór języka użytkownika i ich wprowadzony tekst. Następnie nazywamy `Translate` interfejsu API do wykonania tłumaczenia. Nazywamy może także inne funkcje do określania język tekstu i jego sprawdzanie pisowni przed tłumaczenia.

Zobaczmy, jak Zaczniemy naszej klasy:

```cs
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
        // at least show an error dialog when we get an unexpected error
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

Dwie zmienne Członkowskie zadeklarowany w tym miejscu przechowywania informacji o naszych języki:

|||
|-|-|
|`languageCodes`<br>Tablica ciągów|Buforuje kodów języków. Usługa Translator używa krótkich kodów, takich jak `en` dla języka angielskiego, aby zidentyfikować języków.|
|`languageCodesAndTitles`<br>Sorteddictionary —|Mapowania nazwy "przyjaznym" w interfejsie użytkownika z powrotem do krótkich kodów używany w interfejsie API. Przechowywane posortowane alfabetycznie bez względu na case.|

Jest pierwszy kod wykonywany przez naszą aplikację `MainWindow` konstruktora. Po pierwsze, skonfigurowanie metody `HandleExceptions` jako program obsługi błędów ogólnych. Dzięki temu, co najmniej uzyskujemy alert o błędzie Jeśli wyjątku nie jest obsługiwane.

Następnie sprawdzamy upewnij się, że klucze interfejsu API w subskrypcji są wszystkie dokładnie 32 znaków. Jeśli nie są najbardziej prawdopodobną przyczyną jest to, że *ktoś* nie wkleić w ich kluczy interfejsu API (tsk). W takim przypadku nasz wyświetlony komunikat o błędzie i poręczenie. (Przekazywanie tego testu nie oznacza klucze są prawidłowe, oczywiście.)

Jeśli mamy kluczy, które są co najmniej prawa długość `InitializeComponent()` wywołanie pobiera interfejs użytkownika stopniowych lokalizowanie, ładowania i tworzenie wystąpień opis XAML w głównym oknie aplikacji.

Na koniec skonfigurujemy menu języka. To zadanie wymaga trzech oddzielnych metodach wywołań. Rozszerzana za pośrednictwem tych metod szczegółowo w poniższych sekcjach.

## <a name="get-supported-languages"></a>Pobierz obsługiwane języki

Usługa Microsoft Translator obsługuje łącznie 61 języków, w tym i więcej mogą być dodawane od czasu do czasu. Dlatego go ma najlepiej nie kodowane obsługiwanych języków w programie. Zamiast tego poproś usługi translatora jakich języków go obsługuje. Dowolnego obsługiwanego języka mogą być przekonwertowana na stronę dowolnego obsługiwanego języka.

Wywołanie `Languages` interfejsu API, aby uzyskać listę obsługiwanych języków.

`Languages` Interfejsu API przyjmuje opcjonalny parametr zapytania GET *zakres*. *zakres* może mieć jedną z trzech wartości: `translation`, `transliteration`, i `dictionary`. Używamy wartość `translation`.

`Languages` Interfejsu API przyjmie również opcjonalne nagłówki HTTP, `Accept-Language`. Wartość tego nagłówka określa język, w którym są zwracane nazwy obsługiwanych języków. Wartość powinna być poprawnie sformułowanym znacznik języka BCP 47. Używamy wartość `en` można pobrać nazwy języka w języku angielskim.

`Languages` Interfejsu API zwraca odpowiedź w formacie JSON, który wygląda podobnie do następującej.

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

Chcemy wyodrębnić kodów języków (na przykład `af`) i nazwy języka (na przykład `Afrikaans`). Możemy użyć metody NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) w tym celu.

Z tego wiedzy tła utworzymy następującą metodę do pobrania kodów języków i ich nazw.

```cs
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

`GetLanguagesForTranslate()` najpierw tworzy żądanie HTTP. `scope=translation` Ciąg parametr określa, że firma Microsoft języki obsługiwane w przypadku tłumaczenie tekstu zapytania. Klucz interfejsu API tłumaczenie tekstu subskrypcji dodania do naszej nagłówki żądania. Możemy również dodać `Accept-Language` nagłówka o wartości `en` wskazująca chcemy obsługiwanych języków zwrócony w języku angielskim.

Po zakończeniu żądania, możemy przeanalizować odpowiedzi JSON i przekonwertować go do słownika. Dodamy kodów języka aby `languageCodes` zmiennej członkowskiej. Następnie pętla za pomocą pary klucz wartość, które zawierają kodów języków i języka przyjaznej nazwy i dodaj je na `languageCodesAndTitles` zmiennej członkowskiej. (Menu rozwijane w naszym formularza wyświetlania przyjaznych nazw, ale potrzebujemy kody do żądania tłumaczenia).

## <a name="populate-the-language-menus"></a>Wypełnij menu Język

Większość naszego interfejsu użytkownika jest zdefiniowany w języku XAML, dlatego firma Microsoft nie musisz znacznie ją skonfigurować oprócz wywołania `InitializeComponent()`. Tylko innych operacją, konieczne jest dodać języka przyjaznej nazwy z listy rozwijane i do której odbywa się `PopulateLanguageMenus()`.

```cs
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

Wypełnianie menu jest to prosta kwestia Iterowanie po `languageCodesAndTitles` słownika i dodawanie każdego klucza, który jest nazwą "przyjaznym", do obu menu. Po wypełnieniu menu, możemy ustawić domyślne do i z języki do wykrywania (w celu automatycznego wykrywania języka) i angielskiej wersji językowej.

> [!TIP]
> Jeśli firma Microsoft nie jest ustawiana domyślny wybór dla naszych menu, użytkownik może kliknąć **Przetłumacz** bez wybierania do lub z języka. Wartości domyślne, eliminując konieczność Aby rozwiązać ten problem.

Teraz `MainWindow` został zainicjowany, Tworzenie interfejsu użytkownika. Nie otrzymujemy ponownie kontroli dopóki użytkownik klika polecenie **Przetłumacz** przycisku.

## <a name="perform-translation"></a>Wykonaj translację

Po kliknięciu przez użytkownika **Przetłumacz**, wywołuje WPF `TranslateButton_Click()` program obsługi zdarzeń, pokazano poniżej.

```cs
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

W tym miejscu najpierw pobrać do i z języków, wraz z tekstem użytkownik wprowadził, z formularza.

Jeśli język źródłowego Wykryj nazywamy `DetectLanguage()` ustalenie język tekstu. Tekst może znajdować się w języku, który nie interfejsów API translatora pomocy technicznej (mogą być wykrywane większej liczbie języków, nie można przetłumaczyć) lub interfejsu API z analizy tekstu może nie być go wykryć. W takim przypadku wyświetli wiadomość w celu poinformowania użytkownika i zwracają bez tłumaczenia.

Jeśli języka źródłowego jest angielski (czy wykrycia lub określić), firma Microsoft sprawdzanie tekst z `CorrectSpelling()` i stosować wszelkie poprawki. Poprawiony tekst jest nadziewany do pola wejściowego, więc użytkownik wie, że korekty. (Użytkownik może poprzedzać tekst tłumaczonym łącznikiem do pomijania poprawkę pisowni).

Jeśli użytkownik nie wprowadził tekst lub do i z języków są takie same, niezbędne jest nie tłumaczenia. W takim przypadku możemy uniknąć zgłoszenia żądania.

Kod do wykonania żądania tłumaczenia powinna wyglądać znajomo. Firma Microsoft kompilacji identyfikator URI, Utwórz żądanie wysłania i przeanalizować odpowiedzi. Do wyświetlania tekstu, przechowujemy w `TranslatedTextLabel` formantu.

Tekst, który jest przekazywana `Translate` interfejsu API w serializowanym tablicy JSON w treści żądania POST. Tablicy JSON może zawierać wielu fragmentów tekstu do tłumaczenia, ale w tym miejscu możemy właśnie obejmują jeden.

Nagłówek HTTP o nazwie `X-ClientTraceId` jest opcjonalna. Wartość powinna być identyfikatorem GUID. Identyfikator śledzenia podany przez klienta jest żądań śledzenia podczas czynności nie działają zgodnie z oczekiwaniami. Jednak powinna być użyteczna, wartość X ClientTraceID musi być rejestrowane przez klienta. Identyfikator śledzenia klienta i Data żądań, które mogą pomóc firmie Microsoft diagnozowanie problemów, które mogą wystąpić.

> [!NOTE]
> Ten samouczek koncentruje się na usługę Microsoft Translator, więc nie Wyjaśniliśmy `DetectLanguage()` i `CorrectSpelling()` metodom dokładniej. Usługi analizy tekstu i sprawdzania pisowni usługi Bing zapewniają odpowiedzi w formacie JSON, a nie XML i analiza tekstu wymaga, aby żądania być w formacie JSON oraz. Konta te właściwości dla większości różnice kodu z metod, które firma Microsoft już w tym samouczku.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API programu Microsoft Translator tekstu](http://docs.microsofttranslator.com/text-translate.html)
