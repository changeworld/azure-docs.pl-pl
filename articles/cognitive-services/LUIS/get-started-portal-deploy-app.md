---
title: 'Szybki start: Wdrażanie aplikacji za pomocą portalu usługi LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Gdy aplikacja jest gotowa do zwrócenia wypowiedź prognoz do aplikacji klienta, takich jak czatbot, należy wdrożyć aplikację do endpoint prognoz. W tym przewodniku Szybki Start przedstawiono sposób wdrażania aplikacji przez tworzenie zasobu endpoint prognoz, przypisywania zasobów do aplikacji, szkolenie aplikacji i publikowania aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259724"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki start: Wdrażanie aplikacji w portalu usługi LUIS

Gdy aplikacja jest gotowa do zwrócenia wypowiedź prognoz do aplikacji klienta, takich jak czatbot, należy wdrożyć aplikację do endpoint prognoz. 

W tym przewodniku Szybki Start przedstawiono sposób wdrażania aplikacji przez tworzenie zasobu endpoint prognoz, przypisywania zasobów do aplikacji, szkolenie aplikacji i publikowania aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Subskrypcja platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedniej opcji szybkiego startu portalu](get-started-portal-build-app.md) lub [pobieranie i importowanie jej](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Zasób endpoint prognoz jest tworzony w witrynie Azure portal. Ten zasób powinien być użyty tylko dla zapytania prediction punktu końcowego. Nie należy używać tego zasobu dla tworzenia zmian do aplikacji. 

1. Zaloguj się do  **[witryny Azure portal](https://ms.portal.azure.com/)**. 

1. Zaznacz zielony **+** Zaloguj się w prawym górnym panelu po lewej stronie i wyszukaj `Cognitive Services` w witrynie marketplace, wybierz ją. 

1. Konfigurowanie subskrypcji przy użyciu następujących ustawień:

    |Ustawienie|Wartość|Przeznaczenie|
    |--|--|--|
    |Name (Nazwa)|`my-cognitive-service-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest konieczne po przypisaniu zasobu do aplikacji w portalu usługi LUIS.|
    |Subskrypcja|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
    |Lokalizacja|**Zachodnie stany USA**|Region platformy azure dla tego zasobu.|
    |Warstwa cenowa|**S0**|Wartość domyślna ceny warstwy dla tego zasobu.|
    |Grupa zasobów|`my-cognitive-service-resource-group`|Utwórz nową grupę zasobów dla wszystkich zasobów usługi cognitive Services. Po zakończeniu z zasobami, można usunąć grupy zasobów, aby wyczyścić swoją subskrypcję. | 

    ![Usługa Azure wybranego interfejsu API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Wybierz **Utwórz** do tworzenia zasobów platformy Azure. 

    W następnej sekcji dowiesz się, jak połączyć tego nowego zasobu z aplikacją usługi LUIS w portalu usługi LUIS. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisz klucz zasobu do aplikacji usługi LUIS w portalu usługi LUIS

Za każdym razem, gdy tworzysz nowy zasób dla usługi LUIS, musisz przypisać zasób aplikacji usługi LUIS. Po przypisaniu, nie trzeba wykonać ten krok ponownie, chyba że utworzysz nowy zasób. Można utworzyć nowy zasób, aby rozwinąć regionów aplikacji lub do obsługi większej liczby zapytań prognozowania. 

1. Zaloguj się do [portal usługi LUIS](https://www.luis.ai), wybierz **myEnglishApp** aplikacji z listy aplikacji.

1. Wybierz **Zarządzaj** w menu w prawym górnym rogu, a następnie zaznacz **kluczy i punktów końcowych**.

1. Aby dodać usługi LUIS, wybrać **przydzielania zasobów +**.

    [![Przydziel zasób do aplikacji](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Wybierz nazwę dzierżawy, subskrypcji i zasobów, a następnie wybierz pozycję **przypisany zasób**. 

    ![Przydziel zasób do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Znajdź nowy wiersz w tabeli i skopiuj adres URL punktu końcowego. Jest poprawnie zbudowane się żądanie HTTP GET do punktu końcowego interfejsu API usługi LUIS do przewidywania. 

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji 

Możesz później, należy zawsze, gdy wszystko będzie gotowe do testowania. Aplikację należy opublikować zawsze wtedy, gdy mają obecnie uczonego wersji była dostępna dla aplikacji klienckich ze środowiska wykonawczego endpoint prognoz. 

1. W przypadku aplikacji nieprzeszkolonych wybierz **Train** z góry, kliknij prawym przyciskiem myszy menu.

1. Wybierz **Publikuj** z góry, kliknij prawym przyciskiem myszy menu. Zaakceptuj domyślne ustawienia środowiska, a następnie wybierz pozycję **Publikuj**.

1. W przypadku powodzenia zielony pasek powiadomień zostanie wyświetlone w górnej części okna przeglądarki, zaznacz **można znaleźć na liście punktów końcowych** łącza. 

    ![Pasek powiadomień pomyślnie opublikowano aplikację w przeglądarce](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Spowoduje to przejście do **kluczy i punktu końcowego ustawienia** strony. Lista przypisanych zasobów i odpowiedni punkt końcowy adresów URL jest w dolnej części strony. 

1. Wybierz adres URL punktu końcowego skojarzone z nazwą nowego zasobu. To spowoduje otwarcie przeglądarki sieci web za pomocą poprawnie skonstruowany adresu URL, aby upewnić **UZYSKAĆ** żądania do środowiska uruchomieniowego endpoint prognoz. 

1. `q=` Na końcu adresu URL jest krótka dla **zapytania** i gdzie wypowiedź użytkownika jest dołączana do żądania GET. Po `q=`, wprowadź ten sam wypowiedź użytkownika używany na końcu tego przewodnika Szybki Start w poprzednim:

    ```Is there a form named hrf-234098```

    Odpowiedź przeglądarki jest te same dane JSON, Twoja aplikacja kliencka otrzyma:

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

    Ta odpowiedź daje więcej informacji, niż domyślna **testu** okienko w poprzednim samouczku. Jeśli chcesz wyświetlić ten sam poziom informacji w okienku testu, można opublikować aplikacji. Następnie w okienku testu wybierz **Porównaj z opublikowanych**. Użyj **JSON Pokaż widok** w okienku testu opublikowany, aby zobaczyć te same dane JSON, co w poprzednim kroku. Dzięki temu można porównać bieżącą aplikację, którą pracujesz nad i aplikację, która została opublikowana do punktu końcowego.

    [![Porównanie obecnie edycji w porównaniu z opublikowanej wersji aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu tego przewodnika Szybki Start wybierz **Moje aplikacje** w górnym menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie aplikacji z listy i wybierz **Usuń** z kontekstu paska narzędzi powyżej listy. 

[![Usunąć aplikację z listy aplikacji](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Identyfikowanie wspólnych intencje i podmioty](luis-tutorial-prebuilt-intents-entities.md)