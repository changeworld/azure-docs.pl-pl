---
title: 'Szybki Start: Biblioteka kliencka programu Personalizacja dla środowiska Node. js'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką klienta narzędzia Personalizacja dla środowiska Node. js przy użyciu pętli szkoleniowej.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 91aee7f4a110490495a3cf840e6b3ef3282c91c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446368"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>Szybki Start: Biblioteka kliencka programu Personalizacja dla środowiska Node. js

Wyświetl spersonalizowaną zawartość w tym przewodniku szybki start Node. js za pomocą usługi personalizacji.

Rozpocznij pracę z biblioteką klienta personalizacji dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Ustalanie rangi listy akcji do personalizacji.
 * Ocenę nagrody raportu wskazującej na powodzenie najwyższej funkcjonalnej akcji.

[Przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) | pakietu | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org) i npm.

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start


Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Konfiguracja** Zmień częstotliwość aktualizacji modelu
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu


## <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure dla programu personalizacji

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla narzędzia Personalizacja przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` klucza zasobu.
* `PERSONALIZER_ENDPOINT` dla punktu końcowego zasobu.

W Azure Portal wartości klucza i punktu końcowego są dostępne na stronie **Szybki Start** .


## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby utworzyć plik `package.json`. 

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Zainstaluj bibliotekę środowiska Node. js dla programu Personalizacja

Zainstaluj bibliotekę kliencką programu Personalizuj dla środowiska Node. js za pomocą następującego polecenia:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Zainstaluj pozostałe pakiety NPM dla tego przewodnika Szybki Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Konfiguracja** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Dzięki temu będzie można szybko przeszkolić usługę, co pozwoli zobaczyć, jak Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](./media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem PersonalizerClient, który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby poprosił o rangę zawartości, Utwórz element RankRequest, a następnie Przekaż go do klienta. Ranga metody. Metoda rangi zwraca RankResponse, zawierający sklasyfikowaną zawartość. 

Aby wysłać wynagrodzenie do programu Personalizacja, Utwórz element RewardRequest, a następnie Przekaż go do klienta. Metoda nagradzania. 

Ustalenie nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Powinna to być jedna z podstawowych decyzji projektowych w architekturze personalizacji. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej personalizacji dla środowiska Node. js:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Żądaj rangi](#request-a-rank)
* [Wyślij wynagrodzenie](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Utwórz nową aplikację Node. js w preferowanym edytorze lub środowisku IDE o nazwie `sample.js`. 

## <a name="add-the-dependencies"></a>Dodawanie zależności

Otwórz **przykładowy plik. js** w preferowanym edytorze lub w środowisku IDE. Aby dodać pakiety NPM, Dodaj następujące `requires`:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource ściągnięte ze zmiennych środowiskowych o nazwie `PERSONALIZER_KEY` i `PERSONALIZER_ENDPOINT`. Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody jest `PERSONALIZER_RESOURCE_ENDPOINT`, a ApiKey jest `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Pobierz opcje zawartości reprezentowane jako akcje

Akcje reprezentują opcje zawartości, które chcesz spersonalizować. Dodaj następujące metody do klasy program, aby uzyskać dane wejściowe użytkownika z wiersza polecenia dla pory dnia i bieżącego preferencji żywności.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań [rangi](#request-a-rank) i [nagrody](#send-a-reward) . W tym przewodniku szybki start każdy wywołania rangi, aby spersonalizować zawartość, nastąpi wywołanie zarobkowe, aby poinformować program Personalizuj, jak dobrze zaklasyfikował zawartość usługi. 

Poniższy pętla kodu pętle za pośrednictwem cyklu z prośbą użytkownika o ich preferencje w wierszu polecenia, wysyłając te informacje do narzędzia Personalizacja w celu zaprezentowania na liście rangi, a następnie wysłania nagrody do Personalizowanie sygnalizujące, jak dobrze usługa przeszedł wybór.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Zwróć uwagę na wywołania rangi i nagrody w poniższych sekcjach.

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-content-choices-represented-as-actions), przed uruchomieniem pliku kodu:

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>Żądaj rangi

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia opcji zawartości. Proces może utworzyć zawartość, która ma zostać wykluczona z rangi, pokazana jako `excludeActions`. Żądanie rangi wymaga [akcji](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeActions i unikatowego identyfikatora zdarzenia rangi (jako identyfikatora GUID), aby otrzymać żądaną odpowiedź. 

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](concept-feature-evaluation.md) [działań i funkcji](concepts-features.md) może być nieuproszczona.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie

Aby zakończyć żądanie pozyskania, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do każdego zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia rangi i wartość liczbową do metody nagrody.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynagrodzenie, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację za pomocą środowiska Node. js z katalogu aplikacji.

```console
node sample.js
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
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
