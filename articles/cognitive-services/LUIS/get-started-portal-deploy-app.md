---
title: 'Szybki start: wdrażanie aplikacji za pomocą portalu usługi LUIS'
titleSuffix: Azure Cognitive Services
description: Ten szybki start pokazuje, jak wdrożyć aplikację, tworząc zasób punktu końcowego prognozowania, przypisując zasób, szkolenia i publikując aplikację.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 0ee2b33aa3388b3cb99aa42c338ded800c9679a4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79241780"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki start: wdrażanie aplikacji w portalu usługi LUIS

Gdy aplikacja usługi LUIS jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej (na przykład bot czatu), należy wdrożyć aplikację do punktu końcowego przewidywania.

W tym przewodniku Szybki start nauczysz się wdrażać aplikację. Tworzenie zasobu punktu końcowego przewidywania, przypisać zasób do aplikacji, szkolić aplikację i publikować aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Uzyskaj [subskrypcję platformy Azure](https://azure.microsoft.com/free).
* Ukończ [poprzednią aplikację Szybki start portalu](get-started-portal-build-app.md) lub pobierz i [zaimportuj aplikację](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Jeśli masz aplikacje, które wstępnie datują uwierzytelnianie zasobów platformy Azure, [przemiń do zasobu platformy Azure](luis-migration-authoring.md). Niektóre strony portalu wyglądają inaczej, gdy obowiązuje uwierzytelnianie za pośrednictwem poczty e-mail.

## <a name="create-the-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Tworzenie zasobu punktu końcowego prognozowania w witrynie Azure portal. Ten zasób powinien być używany tylko dla kwerend przewidywania punktu końcowego. Nie należy używać tego zasobu do tworzenia zmian w aplikacji.

1. Zaloguj się i utwórz zasób w [witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Skonfiguruj subskrypcję przy następujących ustawieniach:

   |Ustawienie|Wartość|Przeznaczenie|
   |--|--|--|
   |Nazwa|`my-luis-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest potrzebna podczas przypisywania zasobu do aplikacji w portalu usługi LUIS.|
   |Subskrypcja|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
   |Grupa zasobów|`my-resource-group`|Utwórz nową grupę zasobów dla wszystkich zasobów usług poznawczych. Po zakończeniu pracy z zasobami można usunąć grupę zasobów, aby wyczyścić subskrypcję. |
   |Lokalizacja tworzenia|**Zachodnie stany USA**|Region platformy Azure do tworzenia.|
   |Warstwa cenowa tworzenia|**F0**|Domyślna warstwa cenowa do tworzenia.|
   |Lokalizacja środowiska uruchomieniowego|**Zachodnie stany USA**|Region platformy Azure do przewidywania zapytań o punkt końcowy.|
   |Warstwa cenowa środowiska uruchomieniowego|**S0**|Ta warstwa cenowa zawiera witryny sieci Web o dużym natężeniu ruchu.|
   | | | |


   ![Wybór interfejsu API platformy Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć zasób platformy Azure.

   W następnej sekcji dowiesz się, jak połączyć ten nowy zasób z aplikacją usługi LUIS w portalu usługi LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisywanie klucza zasobu do aplikacji usługi LUIS w portalu usługi LUIS

Za każdym razem, gdy tworzysz nowy zasób dla usługi LUIS, należy przypisać zasób do aplikacji usługi LUIS. Po jego przypisaniu nie trzeba będzie wykonać tego kroku ponownie, chyba że utworzysz nowy zasób. Można utworzyć nowy zasób, aby rozwinąć regiony aplikacji lub obsługiwać większą liczbę zapytań przewidywania.

1. Zaloguj się do portalu usługi LUIS w [wersji zapoznawczej](https://preview.luis.ai) i wybierz aplikację **myEnglishApp** z listy aplikacji.

1. Wybierz **pozycję Zarządzaj** w prawym górnym menu, a następnie wybierz pozycję **Zasoby platformy Azure**.

1. Aby dodać usługę LUIS, wybierz pozycję **Dodaj zasób prognozowania**.

    ![Aby dodać zasób przewidywania usługi LUIS, wybierz pozycję Dodaj zasób przewidywania](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Wybierz dzierżawę, subskrypcję i nazwę zasobu. Wybierz **pozycję Przypisz zasób**.

   ![Przypisywanie zasobu do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Wykonaj te same czynności, aby dodać klucz autora do aplikacji.

1. Znajdź nowy wiersz w tabeli dla nowego zasobu prognozowania i skopiuj adres URL punktu końcowego. Jest poprawnie skonstruowany, aby `HTTP GET` złożyć żądanie do punktu końcowego interfejsu API usługi LUIS dla przewidywania.

> [!TIP]
> Jeśli zamierzasz użyć aktywnej nauki do ulepszenia aplikacji usługi LUIS, wybierz pozycję **Zmień parametry zapytania** i wybierz pozycję Zapisz **dzienniki**. Ta akcja zmienia przykładowy `log=true` adres URL, dodając parametr querystring. Kopiowanie i używanie zmienionego przykładowego adresu URL kwerendy podczas wykonywania kwerend prognozowania do punktu końcowego środowiska wykonawczego.

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikowanie aplikacji w punkcie końcowym prognozowania

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Żądanie punktu końcowego prognozowania

W portalu w `query=` wersji zapoznawczej na końcu adresu URL jest, gdzie wypowiedź użytkownika jest dołączana do żądania GET. Po `query=`wprowadzeniu tej samej wypowiedź użytkownika, która została użyta na końcu poprzedniego przewodnika Szybki start:

```Is there a form named hrf-234098```

Upewnij się, że ciąg zapytania zawiera następujące pary:

* `show-all-intents=true`
* `verbose=true`

Przeglądarka pokazuje odpowiedź:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Aby wyświetlić ten sam poziom informacji w okienku testowym, należy opublikować aplikację. Po opublikowaniu aplikacji wybierz **pozycję Porównaj z opublikowaną** w okienku testowym. Użyj **pokaż widoku JSON** w opublikowanym okienku testowym, aby wyświetlić ten sam JSON, co w poprzednim kroku. W ten sposób można porównać zmiany do bieżącej aplikacji, nad którą pracujesz z aplikacją, która jest publikowana w punkcie końcowym.

[![Porównaj aktualnie edytowane z opublikowaną wersją aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki start wybierz pozycję **Moje aplikacje** z górnego menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi kontekstu nad listą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [identyfikowanie typowych intencji i jednostek](luis-tutorial-prebuilt-intents-entities.md)
