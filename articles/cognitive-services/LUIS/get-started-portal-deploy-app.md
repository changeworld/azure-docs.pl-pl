---
title: 'Szybki Start: wdrażanie aplikacji za pomocą portalu LUIS'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć aplikację LUIS w punkcie końcowym przewidywania, gdy aplikacja jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej, takich jak rozmowa bot. Ten przewodnik Szybki Start przeprowadzi Cię przez proces wdrażania aplikacji, tworząc zasób punktu końcowego przewidywania, przypisując zasób do aplikacji, szkoleń aplikacji i publikowania aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f640921e6f48559db3f1414551d6ed974df15e4f
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703222"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki Start: wdrażanie aplikacji w portalu LUIS

Gdy aplikacja LUIS jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej (na przykład bot rozmowy), należy wdrożyć aplikację w punkcie końcowym przewidywania.

W tym przewodniku szybki start dowiesz się, jak wdrożyć aplikację. Tworzysz zasób punktu końcowego przewidywania, przypiszesz zasób do aplikacji, nauczysz aplikację i opublikujesz aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Uzyskaj [subskrypcję platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedni Przewodnik Szybki Start](get-started-portal-build-app.md) lub [Pobierz i zaimportuj aplikację](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Tworzysz zasób punktu końcowego przewidywania w Azure Portal. Tego zasobu należy używać tylko w przypadku zapytań prognozowania punktów końcowych. Nie używaj tego zasobu do tworzenia zmian w aplikacji.

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/).

1. Wybierz zielony znak **+** w lewym górnym rogu. Wyszukaj `Cognitive Services` w witrynie Marketplace i wybierz ją.

1. Skonfiguruj subskrypcję przy użyciu następujących ustawień:

   |Ustawienie|Wartość|Cel|
   |--|--|--|
   |Nazwa|`my-cognitive-service-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest potrzebna podczas przypisywania zasobu do aplikacji w portalu LUIS.|
   |Ramach|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
   |Lokalizacja|**Zachodnie stany USA**|Region świadczenia usługi Azure dla tego zasobu.|
   |Warstwa cenowa|**S0**|Domyślna warstwa cenowa dla tego zasobu.|
   |Grupa zasobów|`my-cognitive-service-resource-group`|Utwórz nową grupę zasobów dla wszystkich zasobów usługi poznawczej. Po zakończeniu pracy z zasobami możesz usunąć grupę zasobów, aby oczyścić subskrypcję. |
   | | | |

   ![Wybór interfejsu API platformy Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć zasób platformy Azure.

   W następnej sekcji dowiesz się, jak połączyć ten nowy zasób z aplikacją LUIS w portalu LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisywanie klucza zasobu do aplikacji LUIS w portalu LUIS

Za każdym razem, gdy tworzysz nowy zasób dla LUIS, musisz przypisać zasób do aplikacji LUIS. Po jego przypisaniu nie trzeba wykonać tego kroku ponownie, chyba że zostanie utworzony nowy zasób. Możesz utworzyć nowy zasób, aby rozszerzyć regiony aplikacji lub obsłużyć większą liczbę zapowiadanych zapytań.

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i wybierz aplikację **myEnglishApp** z listy aplikacji.

1. Wybierz pozycję **Zarządzaj** w prawym górnym menu, a następnie wybierz pozycję **zasoby platformy Azure**.

1. Aby dodać LUIS, wybierz pozycję **Dodaj zasób predykcyjny**.

    <!-- TBD: get screenshot-->

1. Wybierz dzierżawę, subskrypcję i nazwę zasobu. Wybierz pozycję **Przypisz zasób**.

   ![Przypisywanie zasobu do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Znajdź nowy wiersz w tabeli i skopiuj adres URL punktu końcowego. Jest prawidłowo skonstruowany, aby wykonać żądanie `HTTP GET` do punktu końcowego interfejsu API LUIS w celu przewidywania.

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji

Przeszkol aplikację, gdy wszystko będzie gotowe do jej przetestowania. Opublikuj aplikację, jeśli chcesz, aby aktualnie przeszkolony wersja była dostępna dla aplikacji klienckich z poziomu środowiska uruchomieniowego punktu końcowego przewidywania zapytań.

1. Jeśli aplikacja jest wyszkola, wybierz pozycję **pouczenie** z menu w prawym górnym rogu.

1. Wybierz pozycję **Publikuj** z górnego menu. Zaakceptuj domyślne ustawienia środowiska, a następnie wybierz pozycję **Publikuj**.

1. Gdy w górnej części okna przeglądarki pojawi się zielony pasek powiadomień o powodzeniu, wybierz pozycję **odwołaj się do listy punktów końcowych**.

   ![Pomyślnie opublikowano pasek powiadomień aplikacji w przeglądarce](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na stronie **Ustawienia kluczy i punktu końcowego** Znajdź na dole listę przypisanych zasobów i odpowiednich adresów URL punktów końcowych.

1. Wybierz adres URL punktu końcowego skojarzony z nową nazwą zasobu. Ta akcja spowoduje otwarcie przeglądarki sieci Web z poprawnie skonstruowanym adresem URL w celu wykonania żądania `GET` do środowiska uruchomieniowego punktu końcowego przewidywania.

## <a name="prediction-endpoint-request"></a>Żądanie punktu końcowego przewidywania

<!-- V3FIX -->

@No__t-0 na końcu adresu URL jest krótkie dla **kwerendy** i polega na tym, że wypowiedź użytkownika jest dołączany do żądania GET. Po @no__t wartość 0 wprowadź tę samą wypowiedź użytkownika, która została użyta na końcu poprzedniego przewodnika Szybki Start:

```Is there a form named hrf-234098```

W przeglądarce zostanie wyświetlona odpowiedź, która jest tym samym formatem JSON, który zostanie odebrany przez aplikację kliencką:

```JSON
{
"query": "Is there a form named hrf-234098",
"topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9768753
},
"intents": [
    {
    "intent": "FindForm",
    "score": 0.9768753
    },
    {
    "intent": "None",
    "score": 0.0216071066
    }
],
"entities": [
    {
    "entity": "hrf-234098",
    "type": "Human Resources Form Number",
    "startIndex": 22,
    "endIndex": 31
    }
    ]
}
```

Ta odpowiedź zawiera więcej informacji niż domyślne okienko testów w poprzednim samouczku. Aby wyświetlić ten sam poziom informacji w okienku testów, należy opublikować aplikację. Po opublikowaniu aplikacji wybierz pozycję **Porównaj z opublikowanymi** w okienku testów. Aby wyświetlić ten sam kod JSON w poprzednim kroku, użyj **widoku Pokaż kod JSON** w okienku opublikowany test. W ten sposób można porównać bieżącą aplikację, nad którą pracujesz z aplikacją opublikowaną w punkcie końcowym.

[![Compare obecnie edytowane w porównaniu do opublikowanej wersji aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)




## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi kontekstowego nad listą.

[Aplikacja ![Delete z listy Moje aplikacje](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Identyfikowanie typowych intencji i jednostek](luis-tutorial-prebuilt-intents-entities.md)
