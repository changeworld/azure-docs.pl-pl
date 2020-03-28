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
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122880"
---
[Przykłady](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[pakietu kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | referencyjnej[(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja [node.js](https://nodejs.org) i NPM.

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki start


Istnieje kilka kroków, aby użyć tego przewodnika Szybki start:

* W witrynie Azure portal utwórz zasób Personalizer
* W witrynie Azure portal dla zasobu Personalizer na stronie **Konfiguracja** zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu zainstaluj pakiet SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby `package.json` utworzyć plik.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalowanie biblioteki Node.js dla personalizatora

Zainstaluj bibliotekę klienta personalizatora dla pliku Node.js za pomocą następującego polecenia:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Zainstaluj pozostałe pakiety NPM dla tego szybkiego startu:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Model obiektu

Klient Personalizer jest [personalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) obiektu, który uwierzytelnia się na platformie Azure przy użyciu microsoft.rest.ServiceClientCredentials, który zawiera klucz.

Aby poprosić o jeden najlepszy element zawartości, utwórz [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest), a następnie przekaż go [do klienta. Metoda rangi.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) Rank Metoda zwraca RankResponse.

Aby wysłać nagrodę do Personalizera, utwórz [nagrodęRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest), a następnie przekaż ją do metody [Nagrody](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) w klasie Wydarzenia.

Określenie nagrody, w tym szybkimrozpoczyniu jest banalne. W systemie produkcyjnym określenie, co wpływa na [wynik nagrody](../concept-rewards.md) i o ile może być złożonym procesem, możesz zdecydować się na zmianę w czasie. Powinno to być jedną z podstawowych decyzji projektowych w architekturze Personalizer.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta personalizatora dla pliku Node.js:

* [Tworzenie klienta personalizatora](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Api nagród](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Utwórz nową aplikację Node.js w preferowanym edytorze lub identyfikatorze IDE o nazwie `sample.js`.

## <a name="add-the-dependencies"></a>Dodawanie zależności

Otwórz plik **sample.js** w preferowanym edytorze lub IDE. Dodaj następujące `requires` elementy, aby dodać pakiety NPM:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobie Personalizer

Tworzenie zmiennych dla klucza platformy Azure i punktu końcowego zasobu pobranych ze zmiennych środowiskowych o nazwie `PERSONALIZER_KEY` i `PERSONALIZER_ENDPOINT`. Jeśli utworzono zmienne środowiskowe po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki start.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta personalizatora

Następnie utwórz metodę zwracania klienta Personalizer. Parametrem metody jest `PERSONALIZER_RESOURCE_ENDPOINT` apiKey jest `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Przedstawianie opcji zawartości jako akcji

Akcje reprezentują wybory zawartości, z których chcesz Personalizer wybrać najlepszy element zawartości. Dodaj następujące metody do Klasy Program do reprezentowania zestawu akcji i ich funkcji.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia się

Pętla uczenia się Personalizer to cykl połączeń [rangi](#request-the-best-action) i [nagród.](#send-a-reward) W tym przewodniku Szybki start każde wywołanie rangi, aby spersonalizować zawartość, następuje wywołanie nagrodą, aby poinformować Personalizatora, jak dobrze działała usługa.

Poniższy kod pętli przez cykl z prośbą użytkownika ich preferencje w wierszu polecenia, wysyłanie tych informacji do Personalizer, aby wybrać najlepszą akcję, przedstawiając wybór do wyboru z listy, a następnie wysyłanie nagrody do Personalizer sygnalizując, jak dobrze usługa zrobiła w swoim wyborze.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Przyjrzyj się bliżej wyzwać zaproszenia do rangi i nagrody w poniższych sekcjach.

Przed uruchomieniem pliku kodu dodaj następujące metody, które [utrzymuje wybór zawartości:](#get-content-choices-represented-as-actions)

* lista getActionsList
* lista właściwości niekładu getContextFeatures

## <a name="request-the-best-action"></a>Poproś o najlepszą akcję

Aby ukończyć żądanie rangi, program pyta preferencje użytkownika o tworzenie opcji zawartości. Proces może tworzyć zawartość, aby wykluczyć `excludeActions`z akcji, pokazane jako . Żądanie rangi wymaga [akcji](../concepts-features.md#actions-represent-a-list-of-options) i ich funkcji, currentContext funkcje, excludeActions i unikatowy identyfikator zdarzenia rangi, aby otrzymać odpowiedź rankingową.

Ten szybki start ma proste funkcje kontekstowe pory dnia i preferencji żywieniowych użytkowników. W systemach produkcyjnych określanie i [ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być kwestią nietrywialną.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Wyślij nagrodę


Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia i wynik nagrody jako wartość liczbową do interfejsu API nagrody.

Ten szybki start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co wysłać na [zaproszenie](../concept-rewards.md) do nagrody, może być nietrywialne, w zależności od konkretnych potrzeb.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację z node.js z katalogu aplikacji.

```console
node sample.js
```

![Program szybki start zadaje kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
