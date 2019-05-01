---
title: 'Szybki start: Wdrażanie aplikacji za pomocą portalu usługi LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dowiedz się, jak wdrożyć aplikacją usługi LUIS do endpoint prognoz, gdy aplikacja będzie gotowa do zwrócenia wypowiedź prognoz do aplikacji klienta, takich jak czatbot. Ten przewodnik Szybki Start przedstawiono sposób wdrażania aplikacji przez tworzenie zasobu endpoint prognoz, przypisywania zasobów do aplikacji, szkolenie aplikacji i publikowania aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9a54cad9212bdb514f7742121909221863ba75e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713523"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki start: Wdrażanie aplikacji w portalu usługi LUIS

Gdy aplikacją usługi LUIS jest gotowy do zwrócenia wypowiedź prognoz do aplikacji klienta (na przykład czatbot), należy wdrożyć aplikację do endpoint prognoz.

W tym przewodniku Szybki Start dowiesz się wdrożyć aplikację. Utwórz zasób endpoint prognoz, przypisywania zasobów do aplikacji, uczenie aplikacji i Opublikuj aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz [subskrypcji platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedniej opcji szybkiego startu portalu](get-started-portal-build-app.md) lub [pobieranie i importowanie jej](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Utworzysz zasób endpoint prognoz w witrynie Azure portal. Ten zasób powinien być użyty tylko dla zapytania prediction punktu końcowego. Nie należy używać tego zasobu dla tworzenia zmian do aplikacji.

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).

1. Zaznacz zielony **+** Zaloguj się w lewym górnym panelu. Wyszukaj `Cognitive Services` w portalu marketplace i wybierz ją.

1. Konfigurowanie subskrypcji przy użyciu następujących ustawień:

   |Ustawienie|Wartość|Przeznaczenie|
   |--|--|--|
   |Name (Nazwa)|`my-cognitive-service-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest konieczne po przypisaniu zasobu do aplikacji w portalu usługi LUIS.|
   |Subskrypcja|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
   |Lokalizacja|**Zachodnie stany USA**|Region platformy Azure dla tego zasobu.|
   |Warstwa cenowa|**S0**|Wartość domyślna ceny warstwy dla tego zasobu.|
   |Grupa zasobów|`my-cognitive-service-resource-group`|Utwórz nową grupę zasobów dla wszystkich zasobów usługi cognitive Services. Po zakończeniu korzystania z zasobami, możesz usunąć grupę zasobów, aby wyczyścić swoją subskrypcję. |
   | | | |

   ![Usługa Azure wybranego interfejsu API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Wybierz **Utwórz** do tworzenia zasobów platformy Azure.

   W następnej sekcji dowiesz się, jak połączyć tego nowego zasobu z aplikacją usługi LUIS w portalu usługi LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisz klucz zasobu do aplikacji usługi LUIS w portalu usługi LUIS

Za każdym razem, gdy tworzysz nowy zasób dla usługi LUIS, musisz przypisać zasób aplikacji usługi LUIS. Po przypisaniu, nie będzie trzeba ponownie wykonaj ten krok, jeśli nie możesz utworzyć nowy zasób. Można utworzyć nowy zasób, aby rozwinąć regionów aplikacji lub do obsługi większej liczby zapytań prognozowania.

1. Zaloguj się do [portal usługi LUIS](https://www.luis.ai) i wybierz polecenie **myEnglishApp** aplikacji z listy aplikacji.

1. Wybierz **Zarządzaj** w menu w prawym górnym rogu, a następnie wybierz **kluczy i punktów końcowych**.

1. Aby dodać usługi LUIS, wybierz **przydzielania zasobów +**.

   [![Przydziel zasób do aplikacji](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Wybierz nazwę dzierżawy, subskrypcji i zasobów. Wybierz **przypisany zasób**.

   ![Przydziel zasób do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Znajdź nowy wiersz w tabeli i skopiuj adres URL punktu końcowego. Jest poprawnie skonstruowany się `HTTP GET` żądanie do punktu końcowego interfejsu API usługi LUIS w celu prognozowania.

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji

Uczenie aplikacji, gdy wszystko będzie gotowe ją przetestować. Publikowanie aplikacji można obecnie uczonego wersji była dostępna dla aplikacji klienckich ze środowiska wykonawczego endpoint prognoz.

1. W przypadku aplikacji nieprzeszkolonych wybierz **Train** z menu w prawym górnym rogu.

1. Wybierz **Publikuj** z menu. Zaakceptuj domyślne ustawienia środowiska, a następnie wybierz pozycję **Publikuj**.

1. W przypadku powodzenia zielony pasek powiadomień zostanie wyświetlone w górnej części okna przeglądarki, zaznacz **można znaleźć na liście punktów końcowych**.

   ![Pasek powiadomień pomyślnie opublikowano aplikację w przeglądarce](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na **kluczy i punktu końcowego ustawienia** strony, znajdź listę przypisanych zasobów i odpowiednie adresy URL punktów końcowych w dolnej części.

1. Wybierz adres URL punktu końcowego skojarzone z nazwą nowego zasobu. Ta akcja powoduje otwarcie przeglądarki sieci web za pomocą poprawnie skonstruowany adresu URL, aby `GET` żądania do środowiska uruchomieniowego endpoint prognoz.

1. `q=` Na końcu adresu URL jest krótka dla **zapytania** i gdzie wypowiedź użytkownika jest dołączana do żądania GET. Po `q=`, wprowadź ten sam wypowiedź użytkownika używany na końcu tego przewodnika Szybki Start w poprzednim:

    ```Is there a form named hrf-234098```

    Przeglądarka pokazuje odpowiedzi, czyli te same dane JSON, Twoja aplikacja kliencka otrzyma:

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

    Ta odpowiedź zawiera więcej informacji, niż domyślna okienko testowania w poprzednim samouczku. Aby wyświetlić ten sam poziom informacji w okienku testu, możesz opublikować aplikację. Po opublikowaniu aplikacji, wybierz **Porównaj z opublikowanych** w okienko testowania. Użyj **JSON Pokaż widok** w okienku badań opublikowanych, aby zobaczyć te same dane JSON, co w poprzednim kroku. W ten sposób możesz porównać bieżącą aplikację, którą pracujesz z aplikacją, która została opublikowana do punktu końcowego.

    [![Porównanie obecnie edycji w porównaniu z opublikowanej wersji aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z tego przewodnika Szybki Start, wybierz **Moje aplikacje** w górnym menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz **Usuń** z kontekstu paska narzędzi powyżej listy.

[![Usunąć aplikację z listy aplikacji](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Identyfikowanie wspólnych intencje i podmioty](luis-tutorial-prebuilt-intents-entities.md)
