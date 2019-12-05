---
title: 'Szybki Start: wyświetlanie list głosów zamiany tekstu na mowę, usługi Python-Speech'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu języka Python. Lista jest zwracana w formacie JSON i dostępność głosu różni się w zależności od regionu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b20a8aabbd41c09efb6818cac2999a8c84b669fc
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816427"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Szybki Start: Pobieranie listy głosów zamiany tekstu na mowę przy użyciu języka Python

W tym przewodniku szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu języka Python. Lista jest zwracana w formacie JSON i dostępność głosu różni się w zależności od regionu. Aby zapoznać się z listą obsługiwanych regionów, zobacz [regiony](regions.md).

Ten przewodnik Szybki Start wymaga [konta Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usługi mowy

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests`.

Żądania są używane dla żądań HTTP do usługi zamiany tekstu na mowę.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ustaw klucz subskrypcji i Utwórz monit dla TTS

W kolejnych sekcjach utworzysz metody obsługi autoryzacji, wywołania interfejsu API zamiany tekstu na mowę i sprawdzania poprawności odpowiedzi. Zacznijmy od utworzenia klasy. Jest to, gdzie umieścimy naszych metody wymiany tokenu i wywołania interfejsu API zamiany tekstu na mowę.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` Jest klucz unikatowy w witrynie Azure portal.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Ten punkt końcowy wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. Ten przykład umożliwia wymianę klucza subskrypcji usługi mowy dla tokenu dostępu przy użyciu punktu końcowego `issueToken`.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `fetch_token_url`. Aby uzyskać pełną listę, zobacz [regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do `GetVoices` klasy:

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
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Tworzenie żądania i zapisać odpowiedź

W tym miejscu zamierzasz skompilować żądanie i zapisać listę zwróconych głosów. Najpierw musisz ustawić `base_url` i `path`. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, pamiętaj o zaktualizowaniu `base_url`. Aby uzyskać więcej informacji, zobacz [regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie Dodaj wymagane nagłówki dla żądania. Na koniec wprowadzisz żądania do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź jest zapisywana w pliku.

Skopiuj ten kod do `GetVoices` klasy:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest do utworzenia wystąpienia klasy i wywołania funkcji.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Jest to gotowe do uruchomienia przykładu. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
* [Próbki głosu rekord do utworzenia niestandardowych voice](record-custom-voice-samples.md)
