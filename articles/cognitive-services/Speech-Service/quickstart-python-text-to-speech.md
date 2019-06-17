---
title: 'Szybki start: Konwertuj tekst na mowę, językiem Python — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się, jak konwertować zamiany tekstu na mowę przy użyciu języka Python i interfejsu API REST zamiany tekstu na mowę. Przykładowy tekst uwzględnione w tym przewodniku mają strukturę jako język znaczników synteza mowy (SSML). Dzięki temu możliwe jest wybranie głos i język odpowiedzi mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: c71d76539a4486527d2c8954c62db82a52ca3a4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056819"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Szybki start: Konwertowanie tekstu na mowę, przy użyciu języka Python

W tym przewodniku Szybki Start dowiesz się, jak konwertować zamiany tekstu na mowę przy użyciu języka Python i zamiany tekstu na mowę interfejsu API REST. Ma strukturę treści żądania, w tym przewodniku [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md), co pozwala wybrać głos i język w odpowiedzi.

Ten przewodnik Szybki Start wymaga [konta usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usług przetwarzania mowy

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.py`.

```python
import os, requests, time
from xml.etree import ElementTree
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests`.

Te moduły są używane do zapisu odpowiedzi mowy do pliku z sygnaturą czasową konstruowania żądania HTTP i wywołania interfejsu API zamiany tekstu na mowę.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ustaw klucz subskrypcji i Utwórz monit dla TTS

W kolejnych sekcjach utworzysz metody obsługi autoryzacji, wywołania interfejsu API zamiany tekstu na mowę i sprawdzania poprawności odpowiedzi. Zacznijmy od dodania kodu, który zapewnia, że w tym przykładzie będzie działał z językiem Python 2.7.x i 3.x.

```python
try: input = raw_input
except NameError: pass
```

Następnie Utwórz klasę. Jest to, gdzie umieścimy naszych metody wymiany tokenu i wywołania interfejsu API zamiany tekstu na mowę.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` Jest klucz unikatowy w witrynie Azure portal. `tts` monituje użytkownika o wprowadzenie tekstu, który zostanie przekonwertowany na mowę w celu. Te dane wejściowe to literału ciągu, dzięki czemu nie muszą być poprzedzone znakiem zmiany znaczenia znaków. Na koniec `timestr` pobiera bieżącą godzinę, użyjemy nazwy pliku.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Zamiany tekstu na mowę interfejsu API REST wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. W tym przykładzie wymienia klucz subskrypcji usług przetwarzania mowy, aby uzyskać dostęp do tokenu przy użyciu `issueToken` punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcji usług przetwarzania mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `fetch_token_url`. Aby uzyskać pełną listę, zobacz [regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do `TextToSpeech` klasy:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnienia przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Tworzenie żądania i zapisać odpowiedź

W tym miejscu możesz zacząć tworzyć żądania i zapisać odpowiedź mowy. Najpierw musisz ustawić `base_url` i `path`. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, pamiętaj o zaktualizowaniu `base_url`. Aby uzyskać więcej informacji, zobacz [regionów usług przetwarzania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie należy dodać wymagane nagłówki żądania. Upewnij się, że aktualizujesz `User-Agent` nazwą zasobu (znajdujący się w witrynie Azure portal) i ustaw `X-Microsoft-OutputFormat` do preferowanego danych wyjściowych audio. Aby uzyskać pełną listę formatów danych wyjściowych, zobacz [danych wyjściowych Audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie konstruować treści żądania, za pomocą mowy syntezy Markup Language (SSML). W tym przykładzie definiuje strukturę i używa `tts` dane wejściowe została utworzona wcześniej.

>[!NOTE]
> W tym przykładzie użyto `ZiraRUS` czcionka głosowa. Aby uzyskać pełną listę Microsoft podany głosów/języków, zobacz [języki](language-support.md).
> Jeśli interesuje Cię tworzenie unikatowy, rozpoznawalny głos na marki, zobacz [tworzenia czcionki głosowe niestandardowe](how-to-customize-voice-font.md).

Na koniec wprowadzisz żądania do usługi. Jeśli żądanie zakończy się pomyślnie, zwracany jest kod stanu 200 odpowiedzi mowy jest zapisywany do pliku oznaczony sygnaturą czasową.

Skopiuj ten kod do `TextToSpeech` klasy:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest do utworzenia wystąpienia klasy i wywołania funkcji.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, wszystko jest gotowe do uruchomienia zamiany tekstu na mowę przykładowej aplikacji. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
python tts.py
```

Po wyświetleniu monitu wpisz dowolną chcesz przekonwertować z zamiany tekstu na mowę. Jeśli to się powiedzie, plik mowy znajduje się w folderze projektu. Odtwarzać je za pomocą odtwarzacza media ulubionych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
* [Próbki głosu rekord do utworzenia niestandardowych voice](record-custom-voice-samples.md)
