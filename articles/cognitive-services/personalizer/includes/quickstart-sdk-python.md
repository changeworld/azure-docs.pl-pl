---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122922"
---
[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [biblioteki](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki start


Istnieje kilka kroków, aby użyć tego przewodnika Szybki start:

* W witrynie Azure portal utwórz zasób Personalizer
* W witrynie Azure portal dla zasobu Personalizer na stronie **Konfiguracja** zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu zainstaluj pakiet SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instalowanie biblioteki Języka Python dla personalizatora

Zainstaluj bibliotekę klienta Personalizer dla języka Python za pomocą następującego polecenia:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Model obiektu

Klient Personalizer jest [personalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) obiektu, który uwierzytelnia się na platformie Azure przy użyciu microsoft.rest.ServiceClientCredentials, który zawiera klucz.

Aby poprosić o jeden najlepszy element zawartości, utwórz [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python), a następnie przekaż go do klienta. Metoda rangi. Rank Metoda zwraca RankResponse.

Aby wysłać wynik nagrody do Personalizer, ustaw identyfikator zdarzenia i wynik nagrody (wartość), aby wysłać go do metody [Nagrody](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) w klasie EventOperations.

Określenie nagrody, w tym szybkimrozpoczyniu jest banalne. W systemie produkcyjnym określenie, co wpływa na [wynik nagrody](../concept-rewards.md) i o ile może być złożonym procesem, możesz zdecydować się na zmianę w czasie. Powinno to być jedną z podstawowych decyzji projektowych w architekturze Personalizer.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta Personalizer dla języka Python:

* [Tworzenie klienta personalizatora](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Api nagród](#send-a-reward)

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji języka Python

Utwórz nową aplikację Języka Python w `sample.py`preferowanym edytorze lub identyfikatorze IDE o nazwie .

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz plik **sample.py** w preferowanym edytorze lub w programie IDE. Dodaj następujące elementy:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobie Personalizer

Tworzenie zmiennych dla klucza platformy Azure i punktu końcowego zasobu pobranych ze zmiennych środowiskowych o nazwie `PERSONALIZER_RESOURCE_KEY` i `PERSONALIZER_RESOURCE_ENDPOINT`. Jeśli utworzono zmienne środowiskowe po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki start.

Nazwa zasobu jest częścią adresu `https://<your-resource-name>.api.cognitive.microsoft.com/`URL punktu końcowego: .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta personalizatora

Następnie utwórz metodę zwracania klienta Personalizer. Parametrem metody jest `PERSONALIZER_RESOURCE_ENDPOINT` apiKey jest `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Przedstawianie opcji zawartości jako akcji

Akcje reprezentują wybory zawartości, z których chcesz Personalizer wybrać najlepszy element zawartości. Dodaj następujące metody do Klasy Program do reprezentowania zestawu akcji i ich funkcji.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia się

Pętla uczenia się Personalizer to cykl połączeń [rangi](#request-the-best-action) i [nagród.](#send-a-reward) W tym przewodniku Szybki start każde wywołanie rangi, aby spersonalizować zawartość, następuje wywołanie nagrodą, aby poinformować Personalizer, jak dobrze działała usługa.

Poniższy kod pętli przez cykl z prośbą użytkownika ich preferencje w wierszu polecenia, wysyłanie tych informacji do Personalizer, aby wybrać najlepszą akcję, przedstawiając wybór do wyboru z listy, a następnie wysyłanie nagrody do Personalizer sygnalizując, jak dobrze usługa zrobiła w swoim wyborze.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Przed uruchomieniem pliku kodu dodaj następujące metody, które [utrzymuje wybór zawartości:](#get-content-choices-represented-as-actions)

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Poproś o najlepszą akcję


Aby ukończyć żądanie rangi, program prosi preferencje `currentContent` użytkownika o utworzenie opcji zawartości. Proces może tworzyć zawartość, aby wykluczyć `excludeActions`z akcji, pokazane jako . Żądanie rangi wymaga akcji i ich funkcji, currentContext funkcje, excludeActions i unikatowy identyfikator zdarzenia, aby otrzymać odpowiedź.

Ten szybki start ma proste funkcje kontekstowe pory dnia i preferencji żywieniowych użytkowników. W systemach produkcyjnych określanie i [ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być kwestią nietrywialną.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Wyślij nagrodę


Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia i wynik nagrody jako wartość liczbową do interfejsu API nagrody.

Ten szybki start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co wysłać na [zaproszenie](../concept-rewards.md) do nagrody, może być nietrywialne, w zależności od konkretnych potrzeb.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację z python z katalogu aplikacji.

```console
python sample.py
```

![Program szybki start zadaje kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)