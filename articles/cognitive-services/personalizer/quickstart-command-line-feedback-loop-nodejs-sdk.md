---
title: 'Szybki start: Biblioteka kliencka programu personalizacji dla środowiska Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z biblioteką kliencką narzędzia Personalizacja dla środowiska Node. js przy użyciu pętli szkoleniowej.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 49fa2cd53ebefa811673ea25a1851081f8cfa415
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958962"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>Szybki start: Personalizowanie biblioteki klienckiej dla środowiska Node. js

Wyświetl spersonalizowaną zawartość w tym przewodniku szybki start Node. js za pomocą usługi personalizacji.

Rozpocznij pracę z biblioteką klienta personalizacji dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Ustalanie rangi listy akcji do personalizacji.
 * Ocenę nagrody raportu wskazującej na powodzenie najwyższej funkcjonalnej akcji.

[](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) pakietu kodu źródłowego biblioteki[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure dla programu personalizacji

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla narzędzia Personalizacja przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`dla klucza zasobu.
* `PERSONALIZER_ENDPOINT`dla punktu końcowego zasobu.

W Azure Portal wartości klucza i punktu końcowego są dostępne na stronie **Szybki Start** .


### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init -y` , aby `package.json` utworzyć plik. 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Zainstaluj bibliotekę środowiska Node. js dla programu Personalizacja

Zainstaluj bibliotekę kliencką programu Personalizuj dla środowiska Node. js za pomocą następującego polecenia:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Zainstaluj pozostałe pakiety NPM dla tego przewodnika Szybki Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W zasobie Personalizuj w Azure Portal Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Dzięki temu będzie można szybko przeszkolić usługę, co pozwoli zobaczyć, jak Górna akcja zmienia się dla każdej iteracji.

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

Utwórz nową aplikację Node. js w preferowanym edytorze lub środowisku IDE o `sample.js`nazwie. 

## <a name="add-the-dependencies"></a>Dodawanie zależności

Otwórz **przykładowy plik. js** w preferowanym edytorze lub w środowisku IDE. Dodaj następujące elementy `requires` , aby dodać pakiety npm:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource ściągnięte ze zmiennych środowiskowych o nazwie `PERSONALIZER_KEY` i `PERSONALIZER_ENDPOINT`. Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody ma `PERSONALIZER_RESOURCE_ENDPOINT` wartość, a ApiKey `PERSONALIZER_RESOURCE_KEY`jest.

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

## <a name="request-a-rank"></a>Żądaj rangi

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia opcji zawartości. Proces może utworzyć zawartość, która ma zostać wykluczona z rangi `excludeActions`, pokazana jako. Żądanie rangi wymaga [akcji](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeActions i unikatowego identyfikatora zdarzenia rangi (jako identyfikatora GUID), aby otrzymać żądaną odpowiedź. 

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
