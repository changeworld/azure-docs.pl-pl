---
title: Samouczek mowy Translator (C#) | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Dowiedz się, jak korzystać z usługi rozpoznawania mowy Translator umożliwia tłumaczenie tekstu w czasie rzeczywistym.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: e82c5c5ccfa6b7de8a9ec111140dad1a40ad44f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347476"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Samouczek: Aplikacji Microsoft Translator WPF w języku C#

W tym samouczku jest samouczek mowy interaktywne narzędzia tłumaczenia, korzystającą z usługi tłumaczenia Microsoft Translator mowy część kognitywnych usług Microsoft Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Żądanie listę języków obsługiwanych przez usługę
> * Przechwyć audio i przekazuje je do usługi
> * Odbieranie i wyświetlić tłumaczenia mowy jako tekst
> * Opcjonalnie odtwarzania rozmowy wersji (tekst na mowę) tłumaczenia

Plik rozwiązania Visual Studio dla tej aplikacji jest [dostępne w witrynie GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku należy dowolnej wersji programu Visual Studio 2017 r, w tym Community Edition. 

Rozwiązanie programu Visual Studio tworzy również Instalatora aplikacji. Należy [zestaw narzędzi](http://wixtoolset.org/) i [WiX zestaw narzędzi Visual Studio rozszerzenia](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) obsługuje tę funkcję.

Należy również klucza subskrypcji dla usługi mowy Translator, który można uzyskać z poziomu pulpitu nawigacyjnego Microsoft Azure. Bezpłatne warstwa cenowa jest dostępna co umożliwia tłumaczenie mowy miesięcznie bezpłatnie do 10 godzin. Ta warstwa jest wystarczająca dla tego samouczka.

Innych firm [biblioteki JSON.Net](https://www.newtonsoft.com/json) (od Newtonsoft) jest również wymagany. Ten zestaw jest automatycznie instalowany przez narzędzie NuGet, jeśli oba pola wyboru Przywracanie pakietu są włączone w opcjach programu Visual Studio.

## <a name="trying-the-translation-app"></a>W trakcie aplikacji tłumaczenia

Po otwarciu rozwiązania Microsoft mowy Translator (`SpeechTranslator.sln`) w programie Visual STudio, naciśnij klawisz F5, aby skompilować i uruchomić aplikację.  Zostanie wyświetlone okno główne programu.

![[Mowy Translator głównego okna]](media/speech-translator-main-window.png)

Przy pierwszym uruchomieniu wybierz **ustawienia konta** z **ustawienia** menu, aby otworzyć okno pokazano poniżej.

![[Mowy Translator głównego okna]](media/speech-translator-settings-window.png)

Wklej klucz Microsoft Translator mowy subskrypcji w tym oknie, a następnie kliknij przycisk **zapisać.** Klucz jest zapisywany między działa.

W oknie głównym wybierz wejście audio i urządzenia wyjściowe, i od a do języków. Jeśli chcesz posłuchać audio tłumaczenia, upewnij się, **TTS** (tekst na mowę) opcja jest zaznaczona. Jeśli chcesz zobaczyć rozważana tłumaczeń częściowe, jak mowy, Włącz **wyniki częściowe** opcji.

Na koniec kliknij **Start** zacząć tłumaczenia. Na przykład element, który chcesz mieć translacji i obserwować rozpoznany i translację są wyświetlane w oknie. Włączenie opcji TTS słyszysz również tłumaczenia.

## <a name="obtaining-supported-languages"></a>Uzyskiwanie obsługiwane języki

W tym usługi Microsoft Translator obsługuje języki więcej niż pięciu dwanaście tłumaczenie tekstu. Mniejszej liczby języki są obsługiwane w przypadku tłumaczenia mowy. Takie języki wymagają obsługi dla obu zapisu (rozpoznawanie mowy) i dla danych wyjściowych tekst na mowę, syntezy.

Innymi słowy do tłumaczenia mowy języka źródłowego musi być obsługiwany dla zapisu. Język danych wyjściowych może być dowolne języki obsługiwane w przypadku tłumaczenie tekstu, przy założeniu, że ma wynik tekstu. Jeśli chcesz mowie tylko może przełożyć na język obsługiwany tekstu na mowę.

Firma Microsoft może od czasu do czasu dodać obsługę nowych języków. Z tego powodu należy nie kodowane znać obsługiwanych języków w aplikacji. Zamiast tego interfejsu API mowy Translator zapewnia języków punktu końcowego, który pozwala pobrać obsługiwanych języków w czasie wykonywania. Użytkownik może odbierać co najmniej jedną listę języków: 

| | |
|-|-|
|`speech`|Języki obsługiwane w przypadku przekształcania mowy. Może być języki źródła do tłumaczenia mowy.|
|`text`|Języki obsługiwane w celu przetłumaczenia na tekst. Może być języków docelowym translacji mowy, gdy jest używany tekst wyjściowy.|
|`tts`|Głosy są obsługiwane dla syntezy mowy, każdy powiązany z określonym językiem. Może być języków docelowym translacji mowy, gdy tekst na mowę, jest używany. Danego języka, może być obsługiwana przez więcej niż jeden głos.|

Punkt końcowy języków nie wymagają klucza subskrypcji, a jego użycie nie odliczona limitu przydziału. Jego identyfikatora URI jest `https://dev.microsofttranslator.com/languages` i zwraca wyniki w formacie JSON.

Metoda `UpdateLanguageSettingsAsync()` w `MainWindow.xaml.cs`, pokazano poniżej, wymaga punktu końcowego języków, aby uzyskać listę obsługiwanych języków. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Ta metoda tworzy najpierw żądania HTTP do punktu końcowego języków, żądanie wszystkie trzy listy języków (`text`, `speech`, i `tts`).

Punkt końcowy języków używa żądania `Accept-Languages` nagłówka można ustalić języka, w którym są reprezentowane nazwy języków. Na przykład język angielski znane jako "Niemieckim" nosi nazwę "Deutsch" w języku niemieckim i "Alemán" w języku hiszpańskim oraz listy języków odzwierciedla tych różnic. Język domyślny systemu jest używany dla tego nagłówka.

Po wysłaniu żądania i odebraniu odpowiedzi JSON odpowiedzi jest analizowana w strukturach danych wewnętrznych. Te struktury są następnie używane do tworzenia menu języka z i do języka. 

Ponieważ dostępne głosy są zależne od języka aby wybierany przez użytkownika, nie można jeszcze skonfigurowane menu głosu. Zamiast tego dostępne głosy dla każdego języka są przechowywane do późniejszego użycia. `ToLanguage_SelectionChanged` Obsługi (w tym samym pliku źródłowego) później aktualizacji menu głosowych przez wywołanie metody `UpdateVoiceComboBox()` po wybraniu języka aby. 

Dla zabawy języka aby jest wybierane losowo, jeśli użytkownik nie ma uruchomić aplikację przed. (Menu ustawienia są przechowywane między sesjami).

## <a name="authenticating-requests"></a>Uwierzytelniania żądania

Do uwierzytelniania w usłudze Microsoft Translator mowy, musisz wysłać klucz subskrypcji platformy Azure w nagłówku jako wartość `Ocp-Apim-Subscription-Key` w żądaniu połączenia.

## <a name="translation-overview"></a>Przegląd tłumaczenia

Tłumaczenie interfejsu API (punkt końcowy protokołu WebSockets `wss://dev.microsofttranslator.com/speech/translate`) akceptuje audio ma zostać poddany translacji w monophonic, 16 kHz, 16-bitowych podpisany WAVE format. Usługa zwraca co najmniej jeden odpowiedzi JSON zawierający zarówno rozpoznany i przetłumaczony tekst. Jeśli zażądano zamiany tekstu na mowę, jest wysyłane pliku audio.

Użytkownik wybierze źródła audio przy użyciu menu mikrofon/plik danych wejściowych. Dźwięk może pochodzić z urządzenia audio (takie jak mikrofon) lub `.WAV` pliku.

Metoda `StartListening_Click` jest wywoływane, gdy użytkownik kliknie przycisk Start. Ten program obsługi zdarzeń z kolei wywołuje `Connect()` aby rozpocząć proces wysyłania audio z punktem końcowym interfejsu API usługi. `Connect()` Metoda wykonuje następujące zadania:


> [!div class="checklist"]
> * Pobieranie ustawień użytkownika w oknie głównym, a następnie sprawdź ich
> * Inicjowanie wejście audio i strumienie wyjściowe
> * Wywoływanie `ConnectAsync()` do obsługi pozostałej pracy

`ConnectAsync()`, z kolei obsługi następujących zadań:

> [!div class="checklist"]
> * Uwierzytelnianie z kluczem subskrypcji platformy Azure w nagłówku `Ocp-Apim-Subscription-Key`
> * Tworzenie `SpeechClient` wystąpienia (znalezione w `SpeechClient.cs`) do komunikacji z usługą
> * Inicjowanie `TextMessageDecoder` i `BinaryMessageDecoder` wystąpień (zobacz `SpeechResponseDecoder.cs`) do obsługi odpowiedzi
> * Wysyłanie audio za pośrednictwem `SpeechClient` wystąpienia usługi Translator mowy
> * Odbieranie i przetwarzania wyników tłumaczenia

Obowiązki `SpeechClient` są mniej:

> [!div class="checklist"]
> * Ustanawiania połączenia obiektu WebSocket do usługi mowy translatora
> * Wysyłanie danych audio i odbierania odpowiedzi za pośrednictwem gniazda

## <a name="a-closer-look"></a>Widok szczegółowy

Powinno się jaśniejszy teraz części aplikacji współdziałania do wykonania żądania tłumaczenia. Spójrzmy na kod, koncentrujących się na odpowiednich części.

W tym miejscu jest wersja częściowe `Connect()` który zostaną wyświetlone ustawienia strumieni audio:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Znacznej części `Connect()` obejmuje tworzenie `SpeechClientOptions` wystąpienia (zobacz `SpeechClientOptions.cs`) do przechowywania Opcje tłumaczenia. Obejmują one informacje wymagane do nawiązania połączenia usługi (takie jak klucz uwierzytelniania i nazwy hosta) i funkcje używane do tłumaczenia. Tutaj pola mapowania pól nagłówka i udostępniane przez parametry HTTP [API mowy Translator](http://docs.microsofttranslator.com/speech-translate.html).

`Connect()` również tworzy i inicjuje urządzenia wejściowego audio (zmienna `sampleProvider`) służy jako źródło mowy ma zostać poddany translacji. To urządzenie jest urządzeniu wprowadzania sprzętu, takie jak mikrofon lub plik zawierający dane audio WAVE.

Oto `ConnectAsync()` metodę, która tworzy `speechClient` klasy i punkty zaczepienia się funkcje anonimowe do obsługi tekstu i binarne odpowiedzi z usługi.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Po uwierzytelnieniu, ta metoda tworzy `SpeechClient` wystąpienia. `SpeechClient` Klasy (w `SpeechClient.cs`) wywołuje procedury obsługi zdarzeń po otrzymaniu danych pliku binarnego i tekstu. Dodatkowe procedury obsługi są wywoływane, gdy połączenie nie powiedzie się lub rozłączeniu.

Dane binarne jest wysyłane przez usługę, gdy jest włączona TTS audio (tekst na mowę dane wyjściowe). Dane tekstowe jest częściowym lub pełnego tłumaczenia rozmowy tekstu. Dlatego po tworzenia wystąpienia, metoda przechwytuje się funkcje do obsługi tych wiadomości: audio przez zapisanie jej nowszych odtwarzania i tekst, wyświetlając go w oknie.

## <a name="next-steps"></a>Kolejne kroki

Ten przykładowy kod jest aplikacją bogate zastosowanie Translator API mowy. Tak istnieje wiele odpowiedniej części ruchu do zrozumienia. Udał udało się za pomocą najważniejsze usługi bits. Dla pozostałych może być istotne kilka punktów przerwania w Visual Studio i przeprowadzenie proces translacji. Po zapoznaniu przykładowej aplikacji jest przystosowany do korzystania z usługi rozpoznawania mowy Translator w aplikacjach.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API mowy Translator firmy Microsoft](http://docs.microsofttranslator.com/speech-translate.html)
