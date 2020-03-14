---
title: 'Szybki Start: wdrażanie aplikacji za pomocą portalu LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak wdrożyć aplikację, tworząc zasób punktu końcowego przewidywania, przypisując zasób, szkolenie i publikację aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 0ee2b33aa3388b3cb99aa42c338ded800c9679a4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241780"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki Start: wdrażanie aplikacji w portalu LUIS

Gdy aplikacja LUIS jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej (na przykład bot rozmowy), należy wdrożyć aplikację w punkcie końcowym przewidywania.

W tym przewodniku szybki start dowiesz się, jak wdrożyć aplikację. Tworzysz zasób punktu końcowego przewidywania, przypiszesz zasób do aplikacji, nauczysz aplikację i opublikujesz aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Uzyskaj [subskrypcję platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedni Przewodnik Szybki Start](get-started-portal-build-app.md) lub [Pobierz i zaimportuj aplikację](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Jeśli masz aplikacje, które mają przestarzałe uwierzytelnianie zasobów platformy Azure, [Migruj do zasobu platformy Azure](luis-migration-authoring.md). Niektóre strony portalu wyglądają inaczej, gdy obowiązuje uwierzytelnianie poczty e-mail.

## <a name="create-the-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Tworzysz zasób punktu końcowego przewidywania w Azure Portal. Tego zasobu należy używać tylko w przypadku zapytań prognozowania punktów końcowych. Nie używaj tego zasobu do tworzenia zmian w aplikacji.

1. Zaloguj się i utwórz zasób w [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Skonfiguruj subskrypcję przy użyciu następujących ustawień:

   |Ustawienie|Wartość|Przeznaczenie|
   |--|--|--|
   |Name (Nazwa)|`my-luis-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest potrzebna podczas przypisywania zasobu do aplikacji w portalu LUIS.|
   |Subskrypcja|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
   |Grupa zasobów|`my-resource-group`|Utwórz nową grupę zasobów dla wszystkich zasobów usługi poznawczej. Po zakończeniu pracy z zasobami możesz usunąć grupę zasobów, aby oczyścić subskrypcję. |
   |Lokalizacja autorstwa|**Zachodnie stany USA**|Region świadczenia usługi Azure na potrzeby tworzenia.|
   |Tworzenie warstwy cenowej|**F0**|Domyślna warstwa cenowa do tworzenia.|
   |Lokalizacja środowiska uruchomieniowego|**Zachodnie stany USA**|Region świadczenia usługi Azure dla zapytań dotyczących punktu końcowego przewidywania.|
   |Warstwa cenowa środowiska uruchomieniowego|**S0**|Ta warstwa cenowa zapewnia obsługę witryn sieci Web o dużym ruchu.|
   | | | |


   ![Wybór interfejsu API platformy Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć zasób platformy Azure.

   W następnej sekcji dowiesz się, jak połączyć ten nowy zasób z aplikacją LUIS w portalu LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisywanie klucza zasobu do aplikacji LUIS w portalu LUIS

Za każdym razem, gdy tworzysz nowy zasób dla LUIS, musisz przypisać zasób do aplikacji LUIS. Po jego przypisaniu nie trzeba wykonać tego kroku ponownie, chyba że zostanie utworzony nowy zasób. Możesz utworzyć nowy zasób, aby rozszerzyć regiony aplikacji lub obsłużyć większą liczbę zapowiadanych zapytań.

1. Zaloguj się do [portalu Luis w wersji zapoznawczej](https://preview.luis.ai) i wybierz aplikację **myEnglishApp** z listy aplikacji.

1. Wybierz pozycję **Zarządzaj** w prawym górnym menu, a następnie wybierz pozycję **zasoby platformy Azure**.

1. Aby dodać LUIS, wybierz pozycję **Dodaj zasób predykcyjny**.

    ![Aby dodać zasób przewidywania LUIS, wybierz pozycję Dodaj zasób predykcyjny](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Wybierz dzierżawę, subskrypcję i nazwę zasobu. Wybierz pozycję **Przypisz zasób**.

   ![Przydziel zasób do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Wykonaj te same czynności, aby dodać klucz tworzenia do aplikacji.

1. Znajdź nowy wiersz w tabeli dla nowego zasobu predykcyjnego i skopiuj adres URL punktu końcowego. Jest prawidłowo skonstruowany, aby `HTTP GET` żądanie do punktu końcowego interfejsu API LUIS w celu przewidywania.

> [!TIP]
> Jeśli zamierzasz używać usługi Active Learning do ulepszania aplikacji LUIS, wybierz pozycję **Zmień parametry zapytania** i wybierz pozycję **Zapisz dzienniki**. Ta akcja powoduje zmianę przykładowego adresu URL przez dodanie parametru `log=true` QueryString. Skopiuj i użyj zmienionego przykładowego adresu URL zapytania podczas tworzenia zapytań prognozowania do punktu końcowego środowiska uruchomieniowego.

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikowanie aplikacji w punkcie końcowym przewidywania

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Żądanie punktu końcowego przewidywania

W portalu w wersji zapoznawczej `query=` na końcu adresu URL jest dołączany do żądania GET. Po `query=`wprowadź tę samą wypowiedź użytkownika, która została użyta na końcu poprzedniego przewodnika Szybki Start:

```Is there a form named hrf-234098```

Upewnij się, że ciąg zapytania zawiera następujące pary:

* `show-all-intents=true`
* `verbose=true`

W przeglądarce zostanie wyświetlona odpowiedź:

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

Aby wyświetlić ten sam poziom informacji w okienku testów, należy opublikować aplikację. Po opublikowaniu aplikacji wybierz pozycję **Porównaj z opublikowanymi** w okienku testów. Aby wyświetlić ten sam kod JSON w poprzednim kroku, użyj **widoku Pokaż kod JSON** w okienku opublikowany test. W ten sposób można porównać zmiany bieżącej aplikacji, nad którą pracujesz z aplikacją opublikowaną w punkcie końcowym.

[![porównać obecnie edycję i opublikowaną wersję aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi kontekstowego nad listą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Identyfikowanie typowych intencji i jednostek](luis-tutorial-prebuilt-intents-entities.md)
