---
title: 'Szybki start: Biblioteka kliencka programu Personalizacja dla języka Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z biblioteką klienta narzędzia Personalizacja dla języka Python za pomocą pętli szkoleniowej.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 877a28e5f672bbd61bad2b4c5c9175c7dafa71ab
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345334"
---
# <a name="quickstart-personalize-client-library-for-python"></a>Szybki start: Personalizowanie biblioteki klienckiej dla języka Python

Wyświetl spersonalizowaną zawartość w tym przewodniku szybki start dla języka Python za pomocą usługi personalizacji.

Rozpocznij pracę z biblioteką klienta personalizacji dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Ustalanie rangi listy akcji do personalizacji.
 * Ocenę nagrody raportu wskazującej na powodzenie najwyższej funkcjonalnej akcji.

[Przykłady pakietów (PyPi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start


Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Ustawienia** Zmień częstotliwość aktualizacji modelu
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu


## <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure dla programu personalizacji

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla narzędzia Personalizacja przy użyciu [Azure Portal](https://portal.azure.com/) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Aby uzyskać więcej informacji, zapoznaj się z [tematem jak utworzyć zasób Cognitive Services przy użyciu Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) . Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`dla klucza zasobu.
* `PERSONALIZER_ENDPOINT`dla punktu końcowego zasobu.

W Azure Portal wartości klucza i punktu końcowego są dostępne na stronie **Szybki Start** .


## <a name="install-the-python-library-for-personalizer"></a>Zainstaluj bibliotekę języka Python dla programu Personalizacja

Zainstaluj bibliotekę klienta programu Personalizacja dla języka Python za pomocą następującego polecenia:

```console
pip install azure-cognitiveservices-personalizer
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Ustawienia** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Dzięki temu będzie można szybko przeszkolić usługę, co pozwoli zobaczyć, jak Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](./media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem PersonalizerClient, który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby poprosił o rangę zawartości, Utwórz element RankRequest, a następnie Przekaż go do klienta. Ranga metody. Metoda rangi zwraca RankResponse, zawierający sklasyfikowaną zawartość. 

Aby wysłać wynagrodzenie do programu Personalizacja, Utwórz element RewardRequest, a następnie Przekaż go do klienta. Metoda nagradzania. 

Ustalenie nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Powinna to być jedna z podstawowych decyzji projektowych w architekturze personalizacji. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej personalizacji dla języka Python:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Żądaj rangi](#request-a-rank)
* [Wyślij wynagrodzenie](#send-a-reward)

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python przy użyciu preferowanego edytora lub `sample.py`środowiska IDE o nazwie. 

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik **Sample.py** w preferowanym edytorze lub w środowisku IDE. Dodaj następujące elementy:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

W klasie **program** Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource pobrane ze zmiennych środowiskowych o nazwie `PERSONALIZER_RESOURCE_KEY` i. `PERSONALIZER_RESOURCE_ENDPOINT` Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody ma `PERSONALIZER_RESOURCE_ENDPOINT` wartość, a ApiKey `PERSONALIZER_RESOURCE_KEY`jest.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Pobierz opcje zawartości reprezentowane jako akcje

Akcje reprezentują opcje zawartości, które chcesz spersonalizować. Dodaj następujące metody do klasy program, aby uzyskać dane wejściowe użytkownika z wiersza polecenia dla pory dnia i bieżącego preferencji żywności.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań [rangi](#request-a-rank) i [nagrody](#send-a-reward) . W tym przewodniku szybki start każdy wywołania rangi, aby spersonalizować zawartość, nastąpi wywołanie zarobkowe, aby poinformować program Personalizuj, jak dobrze zaklasyfikował zawartość usługi. 

Poniższy kod w `main` metodzie jest pętlą przez cykl, który prosi użytkownika o ich preferencje w wierszu polecenia, wysyłając te informacje do narzędzia Personalizowanie do rangi, prezentując wybór rangi do klienta, aby wybrać spośród , a następnie wysłanie nagrody do programu Personalizowanie sygnalizujące, jak dobrze ta usługa w klasyfikacji zaznaczenia.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Zwróć uwagę na wywołania rangi i nagrody w poniższych sekcjach.

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-content-choices-represented-as-actions), przed uruchomieniem pliku kodu:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Żądaj rangi

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia `currentContent` opcji zawartości. Proces może utworzyć zawartość, która ma zostać wykluczona z rangi `excludeActions`, pokazana jako. Żądanie rangi wymaga akcji, currentContext, excludeActions i unikatowego identyfikatora zdarzenia rangi (jako identyfikatora GUID), aby otrzymać żądaną odpowiedź. 

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](concept-feature-evaluation.md) [działań i funkcji](concepts-features.md) może być nieuproszczona.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie

Aby zakończyć żądanie pozyskania, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do każdego zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia rangi i wartość liczbową do metody nagrody.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynagrodzenie, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację przy użyciu języka Python z katalogu aplikacji.

```console
python sample.py
```

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Jak działa Personalizacja](how-personalizer-works.md)

* [Co to jest Personalizacja?](what-is-personalizer.md)
* [Gdzie można używać personalizacji?](where-can-you-use-personalizer.md)
* [Rozwiązywanie problemów](troubleshooting.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
