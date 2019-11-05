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
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488716"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki Start: wdrażanie aplikacji w portalu LUIS

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


Gdy aplikacja LUIS jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej (na przykład bot rozmowy), należy wdrożyć aplikację w punkcie końcowym przewidywania.

W tym przewodniku szybki start dowiesz się, jak wdrożyć aplikację. Tworzysz zasób punktu końcowego przewidywania, przypiszesz zasób do aplikacji, nauczysz aplikację i opublikujesz aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Uzyskaj [subskrypcję platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedni Przewodnik Szybki Start](get-started-portal-build-app.md) lub [Pobierz i zaimportuj aplikację](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Tworzenie zasobu punktu końcowego

Tworzysz zasób punktu końcowego przewidywania w Azure Portal. Tego zasobu należy używać tylko w przypadku zapytań prognozowania punktów końcowych. Nie używaj tego zasobu do tworzenia zmian w aplikacji.

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).

1. Wybierz zielony znak **+** w lewym górnym rogu. Wyszukaj `Cognitive Services` w portalu Marketplace i wybierz ją.

1. Skonfiguruj subskrypcję przy użyciu następujących ustawień:

   |Ustawienie|Wartość|Przeznaczenie|
   |--|--|--|
   |Nazwa|`my-cognitive-service-resource`|Nazwa zasobu platformy Azure. Ta nazwa jest potrzebna podczas przypisywania zasobu do aplikacji w portalu LUIS.|
   |Subskrypcja|Twoja subskrypcja|Wybierz jedną z subskrypcji skojarzonych z Twoim kontem.|
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

1. Znajdź nowy wiersz w tabeli i skopiuj adres URL punktu końcowego. Jest prawidłowo skonstruowany, aby `HTTP GET` żądanie do punktu końcowego interfejsu API LUIS w celu przewidywania.

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji

Przeszkol aplikację, gdy wszystko będzie gotowe do jej przetestowania. Opublikuj aplikację, jeśli chcesz, aby aktualnie przeszkolony wersja była dostępna dla aplikacji klienckich z poziomu środowiska uruchomieniowego punktu końcowego przewidywania zapytań.

1. Jeśli aplikacja jest wyszkola, wybierz pozycję **pouczenie** z menu w prawym górnym rogu.

1. Wybierz pozycję **Publikuj** z górnego menu. Wybierz miejsce produkcyjne i Opublikuj.

1. Gdy zostanie wyświetlony pasek powiadomień, publikowanie zostało zakończone.

1. Na stronie Zarządzaj **zasobami platformy Azure** w sekcji Zarządzanie Znajdź listę przypisanych zasobów i odpowiednich adresów URL punktów końcowych.

1. Skopiuj przykładowe zapytanie do okna przeglądarki i Dodaj wypowiedź użytkownika jako parametr `query`.

## <a name="prediction-endpoint-request"></a>Żądanie punktu końcowego przewidywania

`query=` na końcu adresu URL jest krótki dla **kwerendy** i jest dołączany do żądania GET wypowiedź użytkownika. Po `query=`wprowadź tę samą wypowiedź użytkownika, która została użyta na końcu poprzedniego przewodnika Szybki Start:

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

Aby wyświetlić ten sam poziom informacji w okienku testów, należy opublikować aplikację. Po opublikowaniu aplikacji wybierz pozycję **Porównaj z opublikowanymi** w okienku testów. Aby wyświetlić ten sam kod JSON w poprzednim kroku, użyj **widoku Pokaż kod JSON** w okienku opublikowany test. W ten sposób można porównać bieżącą aplikację, nad którą pracujesz z aplikacją opublikowaną w punkcie końcowym.

[![porównać obecnie edycję i opublikowaną wersję aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi kontekstowego nad listą.

[![usunąć aplikacji z listy Moje aplikacje](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Identyfikowanie typowych intencji i jednostek](luis-tutorial-prebuilt-intents-entities.md)
