---
title: 'Samouczek: interfejs API tłumaczenia mowy w usłudze Translator dla języka C#'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API tłumaczenia mowy w usłudze Translator, aby przetłumaczyć tekst w czasie rzeczywistym.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 68457367987305b6926f8f885a226cfcedd23873
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860317"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Samouczek: Aplikacja z użyciem interfejsu API tłumaczenia mowy w usłudze Translator w języku C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ten samouczek to przewodnik po interaktywnym narzędziu do tłumaczenia mowy, które korzysta z interfejsu API tłumaczenia mowy w usłudze Translator będącego częścią usług Azure Cognitive Services. Poznasz następujące czynności:

> [!div class="checklist"]
> * Wysyłanie żądania listy języków obsługiwanych przez usługę
> * Przechwytywanie treści dźwiękowych i przesyłanie ich do usługi
> * Odbieranie i wyświetlanie tłumaczeń mowy w formie tekstu
> * Opcjonalne odtwarzanie mówionej wersji tłumaczenia (zamiana tekstu na mowę)

Plik rozwiązania Visual Studio dla tej aplikacji jest [dostępny w witrynie GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku należy dowolnej wersji programu Visual Studio 2019 r, w tym Community Edition.

Rozwiązanie programu Visual Studio kompiluje również instalator aplikacji. Do obsługi tej funkcji potrzebne są również [zestaw narzędzi programu WiX](http://wixtoolset.org/) i [rozszerzenie programu Visual Studio zestawu narzędzi programu WiX](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension).

Potrzebny jest również klucz subskrypcji usługi tłumaczenia mowy w usłudze Translator, który możesz uzyskać z poziomu pulpitu nawigacyjnego platformy Microsoft Azure. Narzędzie dostępne jest w bezpłatnej warstwie cenowej, która umożliwia przetłumaczenie maksymalnie 10 godzin mowy miesięcznie bez żadnych opłat. Do celów tego samouczka ta warstwa jest wystarczająca.

Innych firm [biblioteki na składnik JSON.NET](https://www.newtonsoft.com/json) (z Newtonsoft) jest również wymagany. Ten zestaw zostanie automatycznie zainstalowany przez narzędzie NuGet w przypadku zaznaczenia obu pól wyboru Package restore (Przywracanie pakietów) w opcjach programu Visual Studio.

## <a name="trying-the-translation-app"></a>Aplikacja do tłumaczenia — pierwsze kroki

Po otwarciu rozwiązania do tłumaczenia mowy usługi Translator (`SpeechTranslator.sln`) w programie Visual Studio naciśnij klawisz F5, aby skompilować i uruchomić aplikację.  Zostanie wyświetlone okno główne programu.

![[Okno główne aplikacji korzystającej z interfejsu API tłumaczenia mowy usługi Translator]](media/speech-translator-main-window.png)

Podczas pierwszego uruchomienia wybierz opcję **Account Settings** (Ustawienia konta) z menu **Settings** (Ustawienia), aby otworzyć okno, które jest pokazane poniżej.

![[Okno główne aplikacji korzystającej z interfejsu API tłumaczenia mowy usługi Translator]](media/speech-translator-settings-window.png)

Wklej klucz subskrypcji usługi tłumaczenia mowy w usłudze Translator w tym oknie, a następnie kliknij przycisk **Save** (Zapisz). Klucz jest zachowywany pomiędzy uruchomieniami programu.

Wróć do okna głównego, wybierz wejściowe i wyjściowe urządzenia audio, których chcesz użyć, oraz język źródłowy oraz docelowy. Jeśli chcesz usłyszeć wersję audio tłumaczenia, upewnij się, że zaznaczona jest opcja **TTS** (zamiana tekstu na mowę). Jeśli chcesz wyświetlić spekulatywne tłumaczenia częściowe w trakcie mówienia, włącz opcję **Partial Results** (Wyniki częściowe).

Na koniec kliknij przycisk **Start**, aby rozpocząć tłumaczenie. Powiedz coś, co chcesz przetłumaczyć, a rozpoznany tekst i jego tłumaczenie pojawią się na ekranie. Jeśli włączono opcję TTS, również usłyszysz tłumaczenie.

## <a name="obtaining-supported-languages"></a>Uzyskiwanie obsługiwanych języków

W momencie pisania tego artykułu interfejs API tłumaczenia mowy w usłudze Translator obsługuje ponad 60 języków tłumaczenia tekstu. Mniej języków jest obsługiwanych w przypadku tłumaczenia mowy. Te języki wymagają obsługi zarówno transkrypcji (rozpoznawania mowy), jak i syntezy (zamiany tekstu na mowę).

Innymi słowy, w przypadku tłumaczenia mowy wymagana jest obsługa transkrypcji w języku źródłowym. Językiem wyjściowym może być dowolny język, w przypadku którego jest obsługiwane tłumaczenie — pod warunkiem, że chcesz uzyskać wynik w formie tekstu. Jeśli chcesz uzyskać wynik w formie mówionej, możesz wybrać wyłącznie język, w przypadku którego jest obsługiwana zamiana tekstu na mowę.

Od czasu do czasu firma Microsoft może dodawać obsługę nowych języków. Z tego powodu nie należy kodować w aplikacji żadnych informacji na temat obsługiwanych języków. Interfejs API tłumaczenia mowy w usłudze Translator udostępnia punkt końcowy Languages (Języki), który umożliwia pobieranie obsługiwanych języków w środowisku uruchomieniowym. Możesz wybrać co najmniej jedną listę języków:

| | |
|-|-|
|`speech`|Języki, w przypadku których jest obsługiwana transkrypcja mowy. Mogą być to języki źródłowe do tłumaczenia mowy.|
|`text`|Języki, w przypadku których jest obsługiwane tłumaczenie tekstu na tekst. Mogą być to języki docelowe w przypadku tłumaczenia mowy, jeśli dane wyjściowe są w formie tekstu.|
|`tts`|Głosy do syntezy mowy, z których każdy jest skojarzony z konkretnym językiem. Mogą być to języki docelowe w przypadku tłumaczenia mowy, gdy używana jest funkcja zamiany tekstu na mowę. Dany język może być obsługiwany przez więcej niż jeden głos.|

Punkt końcowy Languages nie wymaga podawania klucza subskrypcji, a jego użycie nie jest wliczane do Twojego limitu przydziału. Jego identyfikator URI to `https://dev.microsofttranslator.com/languages` i zwraca on wyniki w formacie JSON.

Pokazana tutaj metoda `UpdateLanguageSettingsAsync()` w pliku `MainWindow.xaml.cs` wywołuje punkt końcowy Languages, aby uzyskać listę obsługiwanych języków.

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

Ta metoda najpierw tworzy żądanie HTTP do punktu końcowego Languages, żądając wszystkich trzech list języków (`text`, `speech` i `tts`).

Punkt końcowy Languages korzysta z nagłówka żądania `Accept-Languages`, aby określić język, w którym przedstawione zostaną nazwy języków. Na przykład język znany osobom posługującym się językiem angielskim jako „German” jest nazywany „Deutsch” w języku niemieckim i „Alemán” w języku hiszpańskim, a lista języków odzwierciedla te różnice. W tym nagłówku używany jest domyślny język systemu.

Po przesłaniu żądania i otrzymaniu odpowiedzi w formacie JSON odpowiedź jest analizowana pod kątem wewnętrznych struktur danych. Te struktury są następnie używane do konstruowania menu From Language (Z języka) i To Language (Na język).

Ponieważ dostępne głosy są uzależnione od języka docelowego wybranego przez użytkownika, skonfigurowanie menu Voice (Głos) nie jest jeszcze możliwe. Zamiast tego dostępne głosy dla każdego języka są przechowywane do późniejszego użycia. Procedura obsługi `ToLanguage_SelectionChanged` (w tym samym pliku źródłowym) aktualizuje menu Voice poprzez wywołanie metody `UpdateVoiceComboBox()` po wybraniu przez użytkownika języka docelowego.

Jeśli użytkownik wcześniej nie uruchamiał aplikacji, język docelowy jest wybierany losowo. (Ustawienia menu są zachowywane między sesjami).

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań

Aby uwierzytelnić się w usłudze tłumaczenia mowy w usłudze Translator firmy Microsoft, należy wysłać klucz subskrypcji platformy Azure w nagłówku jako wartość `Ocp-Apim-Subscription-Key` w żądaniu połączenia.

## <a name="translation-overview"></a>Omówienie tłumaczenia

Interfejs API tłumaczenia (punkt końcowy protokołów Websocket `wss://dev.microsofttranslator.com/speech/translate`) przyjmuje dźwięk do tłumaczenia w formacie WAVE: monofonicznym, 16 kHz, 16-bitowym ze znakiem. Usługa zwraca co najmniej jedną odpowiedź w formacie JSON zawierającą zarówno rozpoznany, jak i przetłumaczony tekst. Jeśli zażądano zamiany tekstu na mowę, wysyłany jest plik audio.

Użytkownik może wybrać źródło dźwięku za pomocą menu Microphone/File Input (Mikrofon/Dane wejściowe z pliku). Dźwięk może pochodzić z urządzenia audio (na przykład mikrofonu) lub z pliku `.WAV`.

Metoda `StartListening_Click` jest wywoływana, gdy użytkownik kliknie przycisk Start. Ta procedura obsługi zdarzeń wywołuje następnie metodę `Connect()` w celu rozpoczęcia procesu wysyłania dźwięku do punktu końcowego interfejsu API usługi. Metoda `Connect()` wykonuje następujące zadania:


> [!div class="checklist"]
> * Uzyskiwanie ustawień użytkownika z okna głównego i walidowanie ich
> * Inicjowanie strumieni danych wejściowych i wyjściowych audio
> * Wywoływanie metody `ConnectAsync()` w celu obsługi dalszego przetwarzania

Następnie metoda `ConnectAsync()` obsługuje następujące zadania:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu klucza subskrypcji platformy Azure w nagłówku `Ocp-Apim-Subscription-Key`
> * Tworzenie wystąpienia `SpeechClient` (w pliku `SpeechClient.cs`) w celu nawiązania komunikacji z usługą
> * Inicjowanie wystąpień `TextMessageDecoder` i `BinaryMessageDecoder` (zobacz `SpeechResponseDecoder.cs`) do obsługi odpowiedzi
> * Wysyłanie dźwięku za pośrednictwem wystąpienia `SpeechClient` do usługi tłumaczenia mowy usługi Translator
> * Odbieranie i przetwarzanie wyników tłumaczenia

Wystąpienie `SpeechClient` pełni następujące funkcje:

> [!div class="checklist"]
> * Nawiązywanie połączenia pomiędzy protokołem WebSocket a usługą tłumaczenia mowy usługi Translator
> * Wysyłanie danych audio i otrzymywanie odpowiedzi za pośrednictwem gniazda

## <a name="a-closer-look"></a>Bliższe spojrzenie

Teraz powinno być jasne, w jaki sposób części aplikacji współpracują, aby zrealizować żądanie tłumaczenia. Przyjrzyjmy się teraz kodowi, skupiając się na istotnych częściach.

Oto częściowa wersja metody `Connect()`, która pokazuje konfigurowanie strumieni audio:

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

Znaczna część metody `Connect()` dotyczy tworzenia wystąpienia `SpeechClientOptions` (zobacz `SpeechClientOptions.cs`) w celu przechowywania opcji tłumaczenia. Opcje obejmują informacje wymagane do nawiązania połączenia z usługą (np. klucz uwierzytelniania i nazwa hosta) oraz funkcje używane do tłumaczenia. Te pola są mapowane na pola nagłówka i parametry protokołu HTTP uwidocznione przez [interfejs API tłumaczenia mowy w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

Metoda `Connect()` tworzy i inicjuje wejściowe urządzenie audio (zmienna `sampleProvider`), które służy jako źródło mowy do przetłumaczenia. Może to być urządzenie sprzętowe, na przykład mikrofon, lub plik zawierający dane audio w formacie WAVE.

Oto metoda `ConnectAsync()`, która inicjuje klasę `speechClient` i podłącza anonimowe funkcje obsługi tekstu i binarnych odpowiedzi z usługi.

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

Po uwierzytelnieniu ta metoda tworzy wystąpienie klasy `SpeechClient`. Klasa `SpeechClient` (w pliku `SpeechClient.cs`) wywołuje procedury obsługi zdarzeń po otrzymaniu danych binarnych i tekstowych. Dodatkowe procedury obsługi są wywoływane, gdy połączenie nie powiedzie się lub zostanie przerwane.

Dane binarne to dane audio (dane wyjściowe po zamianie tekstu na mowę) wysyłane przez usługę, gdy włączona jest opcja TTS. Dane tekstowe to częściowe lub pełne tłumaczenie tekstu mówionego. Po zainicjowaniu metoda podłącza funkcje umożliwiające obsługę tych komunikatów: dźwiękowych poprzez przechowywanie ich do późniejszego odtworzenia oraz tekstowych poprzez wyświetlenie ich w oknie.

## <a name="next-steps"></a>Kolejne kroki

Ten kod przykładowy to aplikacja bogata w funkcje, która pokazuje, jak korzystać z interfejsu API tłumaczenia mowy w usłudze Translator. Dlatego zawiera wiele elementów, które należy rozumieć. Tutaj pokazane zostały najważniejsze z nich. Odnośnie do reszty warto ustawić kilka punktów przerwania w programie Visual Studio i przejść przez proces tłumaczenia. Gdy zapoznasz się już z działaniem aplikacji przykładowej, możesz rozpocząć korzystanie z funkcji tłumaczenia mowy w usłudze Translator we własnych aplikacjach.

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API reference (Dokumentacja interfejsu API tłumaczenia mowy w usłudze Translator firmy Microsoft)](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
