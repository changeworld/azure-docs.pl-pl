---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371616"
---
Użyj biblioteki klienta (Language Understanding( LANGUAGE Understanding) (LUIS) dla języka Python, aby:

* Utwórz aplikację.
* Dodaj intencje, jednostki i wypowiedzi przykład.
* Dodawanie funkcji, takich jak lista fraz.
* Trenuj i publikuj aplikację.

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [referencyjna Przykłady pakietu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [źródłowego kodu źródłowego biblioteki (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto portalu language understanding (LUIS): [Utwórz je bezpłatnie](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-starter-key"></a>Pobierz klucz startowy rozumienia języka (LUIS)

Pobierz [klucz startowy,](../luis-how-to-azure-subscription.md#starter-key) tworząc zasób tworzenia usługi LUIS. Zachowaj klucz i region klucza do następnego kroku.

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

Korzystając z klucza i regionu klucza, utwórz dwie zmienne środowiskowe do uwierzytelniania:

* `LUIS_AUTHORING_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `LUIS_REGION`- Region skojarzony z kluczem. Na przykład: `westus`.

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="install-the-python-library-for-luis"></a>Instalowanie biblioteki języka Python dla usługi LUIS

W katalogu aplikacji zainstaluj bibliotekę klienta tworzenia języka (LUIS) dla języka Python za pomocą następującego polecenia:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Model obiektu

Klient tworzenia języka (LUIS) jest obiektem [LUISAuthoringClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) który uwierzytelnia się na platformie Azure, który zawiera klucz autora.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Aplikacje - [tworzenie,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) [usuwanie,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-) [publikowanie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Przykładowe wypowiedzi — [dodawanie przez partię](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [usuwanie według identyfikatora](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funkcje — zarządzanie [listami fraz](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Model — zarządzanie [intencjami](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) i encjami
* Wzorzec - zarządzanie [wzorami](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Pociąg - [trenuj](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) aplikację i ankietę na [temat statusu szkolenia](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Wersje](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) — zarządzanie za pomocą klonowania, eksportowania i usuwania


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta tworzenia języka (LUIS) dla języka Python:

* [Tworzenie aplikacji](#create-a-luis-app)
* [Dodawanie jednostek](#create-entities-for-the-app)
* [Dodawanie intencji](#create-intent-for-the-app)
* [Dodawanie przykładowych wypowiedzi](#add-example-utterance-to-intent)
* [Trenuj aplikację](#train-the-app)
* [Publikowanie aplikacji](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji języka Python

Utwórz nową aplikację Języka Python w preferowanym edytorze lub IDE. Następnie zaimportuj następujące biblioteki.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu [LUISAuthoringClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Utwórz aplikację usługi LUIS, która będzie zawierała model przetwarzania języka naturalnego (NLP), zawierający intencje, jednostki i wypowiedzi przykładowe.

1. Utwórz metodę [dodawania](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) obiektu [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) w celu utworzenia aplikacji. Nazwa i kultura języka są wymagane właściwości.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Tworzenie intencji dla aplikacji
Obiekt podstawowy w modelu aplikacji usługi LUIS jest intencją. Intencji jest wyrównane z grupowania _intencji_wypowiedzi użytkownika . Użytkownik może zadać pytanie lub złożyć oświadczenie w poszukiwaniu konkretnej _zamierzonej_ odpowiedzi od bota (lub innej aplikacji klienckiej). Przykładami intencji są rezerwacja lotu, pytanie o pogodę w mieście docelowym i pytanie o informacje kontaktowe dotyczące obsługi klienta.

Użyj [metody model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) o nazwie unikatowej intencji, a następnie przekaż identyfikator aplikacji, identyfikator wersji i nową nazwę intencji.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Tworzenie encji dla aplikacji

Chociaż jednostki nie są wymagane, znajdują się one w większości aplikacji. Jednostka wyodrębnia informacje z wypowiedzi użytkownika, niezbędne do fullfil intencji użytkownika. Istnieje kilka typów [wstępnie utworzonych](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) i niestandardowych jednostek, z których każdy ma własne modele obiektu transformacji danych (DTO).  Typowe wstępnie utworzone encje do dodania do aplikacji obejmują [numer](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [liczba porządkowa](../luis-reference-prebuilt-ordinal.md).

Ta **metoda add_entities** stworzyła `Location` prostą jednostkę `Class` z dwiema `Flight` rolami, prostą jednostką, jednostką złożoną i dodaje kilka wstępnie utworzonych jednostek.

Ważne jest, aby wiedzieć, że jednostki nie są oznaczone z zamiarem. Mogą i zwykle mają zastosowanie do wielu intencji. Tylko przykład wypowiedzi użytkownika są oznaczone dla określonego, pojedynczego zamiaru.

Metody tworzenia dla jednostek są częścią [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) klasy. Każdy typ jednostki ma swój własny model obiektu przekształcania danych (DTO).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Dodawanie przykładu wypowiedź do intencji

W celu określenia intencji wypowiedź i wyodrębnić jednostki, aplikacja potrzebuje przykładów wypowiedzi. Przykłady należy kierować określonego, pojedynczy zamiar i należy oznaczyć wszystkie encje niestandardowe. Wstępnie utworzone jednostki nie muszą być oznaczane.

Dodaj wypowiedzi przykład, tworząc listę [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) obiektów, jeden obiekt dla każdego wypowiedź przykład. Każdy przykład należy oznaczyć wszystkie jednostki słownikiem pary nazwy/wartości nazwy jednostki i wartości jednostki. Wartość jednostki powinna być dokładnie tak, jak pojawia się w tekście wypowiedź przykład.

Wywołanie [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) z identyfikatorem aplikacji, identyfikatorem wersji i listą przykładów. Połączenie odpowiada z listą wyników. Należy sprawdzić wynik każdego przykładu, aby upewnić się, że został pomyślnie dodany do modelu.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Uczenie aplikacji

Po utworzeniu modelu aplikacja usługi LUIS musi zostać przeszkolona dla tej wersji modelu. Przeszkolony model może być używany w [kontenerze](../luis-container-howto.md)lub [opublikowany](../luis-how-to-publish-app.md) w szczelinach przejściowych lub produktowych.

Metoda [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) wymaga identyfikatora aplikacji i identyfikatora wersji.

Bardzo mały model, taki jak ten szybki start, będzie trenował bardzo szybko. W przypadku aplikacji na poziomie produkcji szkolenie aplikacji powinno zawierać wywołanie sondowania do [metody get_status,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) aby określić, kiedy lub jeśli szkolenie zakończyło się pomyślnie. Odpowiedź jest lista [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) obiektów o osobnym stanie dla każdego obiektu. Wszystkie obiekty muszą być skuteczne, aby szkolenie było uważane za kompletne.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publikowanie aplikacji do rozumienia języka

Opublikuj aplikację usługi LUIS przy użyciu metody [app.publish.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) Spowoduje to opublikowanie bieżącej uczonej wersji do określonego gniazda w punkcie końcowym. Aplikacja kliencka używa tego punktu końcowego do wysyłania wypowiedzi użytkownika do przewidywania intencji i wyodrębniania jednostek.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python` pomocą polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z prognoz, oczyścić pracę z tego przewodnika Szybki start, usuwając plik i jego podkatalogów.