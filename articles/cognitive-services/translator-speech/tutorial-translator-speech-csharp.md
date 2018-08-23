---
title: Samouczek dotyczący mowy usługi Translator (C#) | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Dowiedz się, jak korzystać z usługi rozpoznawania mowy w usłudze Translator umożliwia tłumaczenie tekstu w czasie rzeczywistym.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 010ad8b5ceeaf046c8d361ff352e6058154a482d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "41987485"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Samouczek: Aplikacja WPF Microsoft Translator w języku C#

Niniejszy samouczek jest przewodnik po przykładzie narzędzie do tłumaczenia mowy interaktywne korzystającym z usługi tłumaczenia mowy w usłudze Translator firmy Microsoft, część usług Microsoft Cognitive Services na platformie Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Żądanie listę języków obsługiwanych przez usługę
> * Przechwyć audio i przekazuje je do usługi
> * Odbieranie i wyświetlić tłumaczenia mowy jako tekst
> * Opcjonalnie odtwarzania prowadzone (zamiany tekstu na mowę) wersję tłumaczenia

Plik rozwiązania Visual Studio dla tej aplikacji to [dostępne w serwisie GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku należy dowolnej wersji programu Visual Studio 2017, m.in. Community Edition. 

Rozwiązanie programu Visual Studio tworzy również Instalatora aplikacji. Potrzebujesz [zestaw narzędzi WiX](http://wixtoolset.org/) i [rozszerzenia Visual Studio zestaw narzędzi WiX](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) obsługuje tę funkcję.

Należy również klucz subskrypcji dla usługi mowy usługi Translator, który można uzyskać z poziomu pulpitu nawigacyjnego Microsoft Azure. Warstwa cenowa bezpłatna jest dostępna, pozwala na tłumaczenie mowy na miesiąc bezpłatnie do 10 godzin. Ta warstwa jest wystarczająca na potrzeby tego samouczka.

Innych firm [biblioteki na składnik JSON.Net](https://www.newtonsoft.com/json) (z Newtonsoft) jest również wymagany. Ten zestaw jest automatycznie instalowany przez NuGet, jeśli oba pola wyboru Przywracanie pakietów są włączone w opcjach programu Visual Studio.

## <a name="trying-the-translation-app"></a>Podjęcie próby aplikacja tłumaczenia

Po otwarciu rozwiązania Microsoft Translator mowy (`SpeechTranslator.sln`) w programie Visual STudio, naciśnij klawisz F5, aby skompilować i uruchomić aplikację.  Zostanie wyświetlone okno główne programu.

![[Mowy usługi Translator główne okno]](media/speech-translator-main-window.png)

Przy pierwszym uruchomieniu wybierz **ustawienia konta** z **ustawienia** menu, aby otworzyć okno, pokazano poniżej.

![[Mowy usługi Translator główne okno]](media/speech-translator-settings-window.png)

Wklej swój klucz subskrypcji mowy w usłudze Translator firmy Microsoft, w tym oknie, a następnie kliknij przycisk **Zapisz.** Klucz jest zapisywany między działa.

W oknie głównym wybierz wejścia audio i urządzeń danych wyjściowych, które chcesz i From i języków. Jeśli chcesz posłuchać audio tłumaczenia, upewnij się, **TTS** (zamiany tekstu na mowę) opcja jest zaznaczona. Jeśli chcesz zobaczyć spekulacyjnego tłumaczenia częściowe, jak mówić, należy włączyć **wyniki częściowe** opcji.

Na koniec kliknij **Start** umożliwiającą tłumaczenia. Załóżmy, że coś, co chcesz mieć translacji i obejrzyj rozpoznany tekst i tłumaczenie są wyświetlane w oknie. Jeśli włączono opcję TTS słyszysz także tłumaczenie.

## <a name="obtaining-supported-languages"></a>Uzyskiwanie obsługiwane języki

W tym usługa Microsoft Translator obsługuje więcej niż pięciu tuzina języków tłumaczenia tekstu. Mniejsza liczba języki są obsługiwane w przypadku tłumaczenia mowy. Takie języki wymagają obsługi dla obu transkrypcji (rozpoznawanie mowy) oraz dla danych wyjściowych zamiany tekstu na mowę, syntezy.

Innymi słowy tłumaczenia mowy, języka źródłowego musi być obsługiwany dla transkrypcji. Język dane wyjściowe mogą być jednym z języków obsługiwanych w tłumaczeniu tekstu, przy założeniu, że chcesz, aby wynik tekstu. Jeśli chcesz, aby mowie, tylko można tłumaczyć na język, obsługiwane w przypadku zamiany tekstu na mowę.

Firma Microsoft może od czasu do czasu dodać obsługę nowych języków. Z tego powodu należy nie kodować sprzętowo żadnej wiedzy obsługiwanych języków w aplikacji. Zamiast tego interfejsu API tłumaczenia mowy udostępnia języków punktu końcowego, który pozwala pobrać obsługiwanych języków w czasie wykonywania. Użytkownik może otrzymywać co najmniej jedną listę języków: 

| | |
|-|-|
|`speech`|Języki obsługiwane w przypadku transkrypcja mowy. Może być źródła języków tłumaczenia mowy.|
|`text`|Języki obsługiwane w przypadku tłumaczenia tekstu na tekst. Może być docelowej języków tłumaczenia mowy, gdy jest używany tekst wyjściowy.|
|`tts`|Głosy są obsługiwane dla synteza mowy, każdy powiązany z określonym językiem. Może być docelowej języków tłumaczenia mowy, gdy zamiany tekstu na mowę, jest używany. Danego języka mogą być obsługiwane przez więcej niż jeden głos.|

Punkt końcowy języków nie wymaga klucza subskrypcji, a jego użycie nie uwzględniane w limicie przydziału. Jego identyfikatora URI jest `https://dev.microsofttranslator.com/languages` i zwraca wyniki w formacie JSON.

Metoda `UpdateLanguageSettingsAsync()` w `MainWindow.xaml.cs`, jak pokazano poniżej, wywołuje punkt końcowy języków, aby uzyskać listę obsługiwanych języków. 

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

Metoda ta najpierw tworzy żądanie HTTP do punktu końcowego języków, żądanie wszystkie trzy listy języków (`text`, `speech`, i `tts`).

Punkt końcowy języków korzysta żądania `Accept-Languages` nagłówek, aby określić język, w których są reprezentowane nazwy języków. Na przykład język angielski znane "Niemieckiego" jest nazywane "Deutsch" w języku niemieckim i "Alemán" w języku hiszpańskim oraz listy języków odzwierciedla te różnice. Domyślny język systemu jest używany dla tego pliku nagłówkowego.

Po żądanie zostało wysłane i odebrane, odpowiedź JSON odpowiedzi jest przekształcany do wewnętrznych struktur danych. Te struktury są następnie używane do konstruowania menu w języku i na język. 

Ponieważ dostępne głosy są zależne od języka do wybranego przez użytkownika, nie można skonfigurować w menu głosu jeszcze. Zamiast tego dostępnych głosów dla każdego języka są przechowywane do późniejszego użycia. `ToLanguage_SelectionChanged` Obsługi (w tym samym pliku źródłowym) później aktualizuje menu głosowych przez wywołanie metody `UpdateVoiceComboBox()` po użytkownik zdecyduje się na język. 

Tylko dla zabawy, a na język jest wybierane losowo, jeśli użytkownik nie został uruchomiony z aplikacji przed. (Menu są przechowywane ustawienia między sesjami.)

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań

Do uwierzytelniania usługi mowy w usłudze Translator firmy Microsoft, musisz wysłać swój klucz subskrypcji platformy Azure w nagłówku jako wartość pozycji `Ocp-Apim-Subscription-Key` żądania połączenia.

## <a name="translation-overview"></a>Przegląd tłumaczenia

Interfejs API tłumaczenia (punkt końcowy gniazda Websocket `wss://dev.microsofttranslator.com/speech/translate`) akceptuje audio do tłumaczenia w monophonic, 16 kHz, 16-bitowe podpisane formacie WAVE. Usługa zwraca co najmniej jeden odpowiedziami JSON zawierający zarówno rozpoznane i przetłumaczonego tekstu. Jeśli zażądano zamiany tekstu na mowę, wysyłany jest plik audio.

Użytkownik wybierze źródła audio, za pomocą menu mikrofon/pliku wejściowego. Audio mogą pochodzić z urządzenia audio (takie jak mikrofon) lub `.WAV` pliku.

Metoda `StartListening_Click` jest wywoływane, gdy użytkownik kliknie przycisk Start. Ta procedura obsługi zdarzeń, z kolei wywołuje `Connect()` aby rozpocząć proces wysyłania audio z punktem końcowym interfejsu API usługi. `Connect()` Metoda wykonuje następujące zadania:


> [!div class="checklist"]
> * Trwa pobieranie ustawień użytkownika w głównym oknie, a następnie je zweryfikować
> * Inicjowanie wejścia audio i strumieni danych wyjściowych
> * Wywoływanie `ConnectAsync()` się całą resztą pracy

`ConnectAsync()`, z kolei obsługi następujących zadań:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu subskrypcji platformy Azure klucz w nagłówku `Ocp-Apim-Subscription-Key`
> * Tworzenie `SpeechClient` wystąpienia (znalezione w `SpeechClient.cs`) do komunikacji z usługą
> * Inicjowanie `TextMessageDecoder` i `BinaryMessageDecoder` wystąpienia (zobacz `SpeechResponseDecoder.cs`) do obsługi odpowiedzi
> * Wysyłanie dźwięku za pomocą `SpeechClient` wystąpienia usługi mowy usługi Translator
> * Odbieranie i przetwarzanie wyników tłumaczenia

Do obowiązków `SpeechClient` są mniej:

> [!div class="checklist"]
> * Podczas nawiązywania połączenia protokołu WebSocket, usługą mowy usługi Translator
> * Wysyłanie danych audio i odbierania odpowiedzi za pośrednictwem gniazda

## <a name="a-closer-look"></a>Widok szczegółowy

Powinna to być bardziej zrozumiały teraz jak części aplikacji współpracują ze sobą do wykonania żądania tłumaczenia. Spójrzmy na niektóre kod, koncentrując się na istotnych częściach.

Oto częściowa wersja `Connect()` ustawienie jest widoczny strumieni audio:

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

Znaczną część `Connect()` obejmuje tworzenie `SpeechClientOptions` wystąpienia (zobacz `SpeechClientOptions.cs`) do przechowywania Opcje tłumaczenia. Opcje obejmują informacje wymagane do połączenia z usługą (np. klucz uwierzytelniania i nazwy hosta) i funkcji do tłumaczenia. Tutaj pola mapowania pola nagłówka i parametry protokołu HTTP udostępnianych przez [interfejs API mowy usługi Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` również tworzy i inicjuje urządzenie wejściowe audio (zmienna `sampleProvider`) służy jako źródło mowy, który ma zostać poddany translacji. To urządzenie jest urządzenia wejściowego sprzętu, takie jak mikrofon lub plik zawierający dane audio WAVE.

Oto `ConnectAsync()` metody, która tworzy wystąpienie `speechClient` klasy i podłącza funkcji anonimowych do obsługi tekstowych i binarnych odpowiedzi z usługi.

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

Po uwierzytelnieniu, ta metoda tworzy `SpeechClient` wystąpienia. `SpeechClient` Klasy (w `SpeechClient.cs`) wywołuje procedury obsługi zdarzeń po otrzymaniu danych binarnych i tekstowych. Dodatkowe procedury obsługi są wywoływane, gdy połączenia nie powiedzie się lub rozłączeniu.

Dane binarne jest wysyłane przez usługę po włączeniu TTS audio (zamiany tekstu na mowę dane wyjściowe). Dane tekstowe jest częściowego lub pełne tłumaczenie tekstu mówionego. Dlatego po utworzeniu wystąpienia, metoda przechwytuje funkcji do obsługi komunikatów: audio dzięki przechowywaniu go w późniejszym odtwarzania i tekst, wyświetlając go w oknie.

## <a name="next-steps"></a>Kolejne kroki

Ten przykładowy kod jest aplikacją bogate ilustrujące użycie interfejsu API tłumaczenia mowy. Jako takie istnieje szereg uczciwe ruchomych części, aby zrozumieć. Użytkownik został przeprowadzony przez najważniejszych porcji. Dla pozostałych może być instruktażowy kilka punktów przerwania w programie Visual Studio i przeprowadzenie procesu tłumaczenia. Gdy już poznasz przykładowej aplikacji, jest przystosowany do korzystania z usługi mowy usługi Translator we własnych aplikacjach.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API mowy usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
