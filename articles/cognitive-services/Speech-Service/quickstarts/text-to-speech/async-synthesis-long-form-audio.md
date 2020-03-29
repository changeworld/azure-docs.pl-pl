---
title: 'Szybki start: Synteza asynchroniza dla dźwięku o długiej formie (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API long audio, aby asynchronicznie konwertować tekst na mowę i pobierać dane wyjściowe audio z identyfikatora URI dostarczonego przez usługę. Ten interfejs API REST jest idealny dla dostawców zawartości, którzy muszą konwertować pliki tekstowe większe niż 10 000 znaków lub 50 akapitów na mowę syntetyzowaną.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331080"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Szybki start: Asynchroniowa synteza dla dźwięku o długiej formie w Pythonie (wersja zapoznawcza)

W tym przewodniku Szybki start użyjesz interfejsu API long audio do asynchronicznie konwertowania tekstu na mowę i pobierania danych wyjściowych audio z identyfikatora URI dostarczonego przez usługę. Ten interfejs API REST jest idealny dla dostawców zawartości, którzy muszą syntetyzować dźwięk z tekstu większego niż 5000 znaków (lub więcej niż 10 minut długości). Aby uzyskać więcej informacji, zobacz [Long Audio API](../../long-audio-api.md).

> [!NOTE]
> Synteza asynchronizalna dla dźwięku o długiej formie może być używana tylko z [niestandardowymi głosami neuronowym.](../../how-to-custom-voice.md#custom-neural-voices)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Python 2.7.x lub 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)lub ulubiony edytor tekstu.
* Subskrypcja platformy Azure i klucz subskrypcji usługi mowy. [Utwórz konto platformy Azure](../../get-started.md#new-resource) i [utwórz zasób mowy,](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) aby uzyskać klucz. Podczas tworzenia zasobu Mowy upewnij się, że warstwa cenowa jest ustawiona na **S0,** a lokalizacja jest [ustawiona](../../regions.md#standard-and-neural-voices)na obsługiwany region .

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment `voice_synthesis_client.py`kodu do pliku o nazwie .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Jeśli te moduły nie były używane, należy je zainstalować przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests urllib3`.

Moduły te są używane do analizowania argumentów, konstruowania żądania HTTP i wywoływania interfejsu API REST audio od strony tekstu na mowę.

## <a name="get-a-list-of-supported-voices"></a>Wyświetlanie listy obsługiwanych głosów

Ten kod zawiera listę dostępnych głosów, których można użyć do konwersji tekstu na mowę. Dodaj kod `voice_synthesis_client.py`do:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>testowanie kodu

Sprawdźmy, co zrobiłeś do tej pory. Musisz zaktualizować kilka rzeczy w poniższym żądaniu:

* Zamień `<your_key>` klucz subskrypcji usługi mowy. Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zamień `<region>` na region, w którym utworzono zasób mowy (na przykład: `eastus` lub `westus`). Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Zobaczysz dane wyjściowe, które wygląda następująco:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Przygotowywanie plików wejściowych

Przygotowanie wejściowego pliku tekstowego. Może to być zwykły tekst lub tekst SSML. Aby zapoznać się z wymaganiami dotyczącymi plików wejściowych, zobacz jak przygotować zawartość do [syntezy](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Konwertowanie tekstu na mowę

Po przygotowaniu wejściowego pliku tekstowego dodaj `voice_synthesis_client.py`ten kod do syntezy mowy do:

> [!NOTE]
> 'concatenateResult' jest parametrem opcjonalnym. Jeśli ten parametr nie jest ustawiony, wyjścia audio zostaną wygenerowane na akapit. Można również łączyć dźwięki z 1 wyjściem, ustawiając parametr. Domyślnie wyjście audio jest ustawione na riff-16khz-16bit-mono-pcm. Aby uzyskać więcej informacji na temat obsługiwanych wyjść audio, zobacz [Formaty wyjścia audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>testowanie kodu

Złóżmy żądanie syntezy tekstu przy użyciu pliku wejściowego jako źródła. Musisz zaktualizować kilka rzeczy w poniższym żądaniu:

* Zamień `<your_key>` klucz subskrypcji usługi mowy. Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zamień `<region>` na region, w którym utworzono zasób mowy (na przykład: `eastus` lub `westus`). Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zastąp `<input>` ścieżką do pliku tekstowego przygotowanego dla zamiany tekstu na mowę.
* Zamień `<locale>` na żądane ustawienia regionalne danych wyjściowych. Aby uzyskać więcej informacji, zobacz [obsługa języka](../../language-support.md#neural-voices).
* Wymień `<voice_guid>` na żądany głos wyjściowy. Użyj jednego z głosów zwróconych przez [Pobierz listę obsługiwanych głosów](#get-a-list-of-supported-voices).

Konwertuj tekst na mowę za pomocą tego polecenia:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Jeśli masz więcej niż 1 pliki wejściowe, musisz przesłać wiele żądań. Istnieją pewne ograniczenia, które muszą być świadome. 
> * Klient może przesłać do **serwera maksymalnie 5** żądań na sekundę dla każdego konta subskrypcji platformy Azure. Jeśli przekroczy ograniczenie, klient otrzyma kod błędu 429 (zbyt wiele żądań). Zmniejsz kwotę żądania na sekundę
> * Serwer może uruchamiać i kolejkować do **120** żądań dla każdego konta subskrypcji platformy Azure. Jeśli przekroczy ograniczenie, serwer zwróci kod błędu 429 (zbyt wiele żądań). Poczekaj i unikaj składania nowych wniosków, dopóki niektóre wnioski nie zostaną zakończone

Zobaczysz dane wyjściowe, które wygląda następująco:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Wynik zawiera tekst wejściowy i pliki wyjściowe audio, które są generowane przez usługę. Możesz pobrać te pliki w zip.

## <a name="remove-previous-requests"></a>Usuwanie poprzednich żądań

Serwer będzie przechowywać do **20 000** żądań dla każdego konta subskrypcji platformy Azure. Jeśli kwota żądania przekroczy to ograniczenie, usuń poprzednie wnioski przed dokonaniem nowych. Jeśli nie usuniesz istniejących żądań, otrzymasz powiadomienie o błędzie.

Dodaj kod `voice_synthesis_client.py`do:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>testowanie kodu

Teraz sprawdźmy, jakie żądania zostały wcześniej przesłane. Przed kontynuowaniem należy zaktualizować kilka czynności w tym żądaniu:

* Zamień `<your_key>` klucz subskrypcji usługi mowy. Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zamień `<region>` na region, w którym utworzono zasób mowy (na przykład: `eastus` lub `westus`). Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Spowoduje to zwrócenie listy żądań syntezy, które zostały wykonane. Zobaczysz dane wyjściowe w ten sposób:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Teraz usuńmy wcześniej przesłane żądanie. Musisz zaktualizować kilka rzeczy w poniższym kodzie:

* Zamień `<your_key>` klucz subskrypcji usługi mowy. Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zamień `<region>` na region, w którym utworzono zasób mowy (na przykład: `eastus` lub `westus`). Te informacje są dostępne na karcie **Przegląd** zasobu w [witrynie Azure portal](https://aka.ms/azureportal).
* Zastąp `<synthesis_id>` wartością zwróconą w poprzednim żądaniu.

> [!NOTE]
> Nie można usunąć ani usunąć żądań o stanie "Uruchomiony"/'Oczekiwanie'.

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Zobaczysz dane wyjściowe w ten sposób:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Zdobądź pełnego klienta

Wypełniony `voice_synthesis_client.py` jest dostępny do pobrania na [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o interfejsie API Long Audio](../../long-audio-api.md)
