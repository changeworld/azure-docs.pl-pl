---
title: Poprawa wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Aktywna nauka można poprawić jakość bazy wiedzy w postaci sugerowanych alternatywnych pytania, oparte na użytkownika — wiadomości, do Twojego pary pytań i odpowiedzi. Albo dodanie ich do istniejącego pytania lub odrzucenia ich przejrzenie tych propozycji. Bazy wiedzy nie zmienia się automatycznie. Sugestie dotyczące wszelkie zmiany zaczęły obowiązywać, należy zaakceptować. Tego rodzaju sugestie dodać pytania, ale nie Zmień lub usuń istniejące pytania.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: 3e54e8dcb6efa9251262c651730376a0d04edcf9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144966"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Użyj aktywnej nauki usprawniających bazy wiedzy

Aktywna nauka można poprawić jakość bazy wiedzy w postaci sugerowanych alternatywnych pytania, oparte na użytkownika — wiadomości, do Twojego pary pytań i odpowiedzi. Albo dodanie ich do istniejącego pytania lub odrzucenia ich przejrzenie tych propozycji. 

Bazy wiedzy nie zmienia się automatycznie. Sugestie, aby zmiany zaczęły obowiązywać, należy zaakceptować. Tego rodzaju sugestie dodać pytania, ale nie Zmień lub usuń istniejące pytania.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka?

Usługa QnA Maker uzyskuje informacje o nowych zmian pytanie jawne i niejawne opinii.
 
* Niejawne opinia — tematycznie rozumie, jeśli pytanie użytkownika ma wiele odpowiedzi za pomocą wyniki, które są bardzo podobne i uznaje jako opinii. 
* Jawne opinie — gdy wiele odpowiedzi za pomocą niewielkie różnice w wyniki są zwracane z bazy wiedzy knowledge base, aplikacja kliencka prosi użytkownika zapytania, które jest poprawne pytanie. Jawne opinii użytkownika są wysyłane do usługi QnA Maker przy użyciu interfejsu API pociągu. 

Każda z tych metod zapewnia tematycznie przy użyciu podobnych zapytań, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak aktywne uczenie działa

Aktywne uczenie jest wyzwalana, oparte na wyniki najważniejsze odpowiedzi kilka zwrócone przez narzędzie QnA Maker dla dowolnej podanej kwerendy. Różnice wynik leży w obrębie małe, a następnie zapytanie jest traktowane jako potencjalnie _sugestii_ dla wszystkich możliwych odpowiedzi. 

Wszystkie sugestie są zgrupowane razem wg podobieństwa i najbardziej oczekiwanych alternatywne pytania są wyświetlane na podstawie częstotliwości określonej zapytań przez użytkowników końcowych. Aktywna nauka zapewnia najlepsze możliwe sugestie w przypadkach, gdzie punkty końcowe są objęte ilość uzasadnione i różne zapytania do użycia.

5 lub więcej podobne zapytań są klastrowane, co 30 minut narzędzie QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy knowledge base, aby zaakceptować lub odrzucić.

Po pytania są sugerowane w portalu narzędzia QnA Maker, należy przejrzeć i zaakceptować lub odrzucić te sugestie. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Uaktualnienie z wersji na korzystanie z aktywnej nauki

Aktywne uczenie jest obsługiwana w wersji środowiska uruchomieniowego 4.4.0 i nowszych. Jeśli wiedzy został utworzony we wcześniejszej wersji, [uaktualnić swoje środowisko uruchomieniowe](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) Aby użyć tej funkcji. 

## <a name="best-practices"></a>Najlepsze praktyki

Aby uzyskać najlepsze rozwiązania związane z używaniem aktywne uczenie, zobacz [najlepsze praktyki](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Ocena odległości między wiedzy pytań

Gdy wynik zapytania jest wysoce pewność, takie jak 80%, zakres wyniki, które są uważane za dla aktywne uczenie są szerokie w przybliżeniu w ciągu 10%. Jak zmniejsza współczynnik ufności, takich jak 40%, zakres wyniki zmniejsza to także około w 4%. 

Algorytm ustalania odległości między elementami nie jest proste obliczenia. Zakresy w powyższych przykładach nie są przeznaczone do rozwiązany, ale powinny służyć jako przewodnik Aby zrozumieć wpływ tylko algorytmu.

## <a name="turn-on-active-learning"></a>Włącz aktywne uczenie

Aktywna nauka jest domyślnie wyłączona. Włącz ją wyświetlić sugerowane pytania. 

1. Wybierz **Publikuj** publikowanie bazy wiedzy knowledge base. Aktywna nauka zapytania są zbierane z tylko endpoint prognoz GenerateAnswer interfejsu API. Zapytania, które okienko testowania, w portalu narzędzia Qna Maker nie wpływa na aktywne uczenie.

1. Aby włączyć aktywne uczenie się na, kliknij pozycję Twoje **nazwa**, przejdź do [ **ustawienia usługi** ](https://www.qnamaker.ai/UserSettings) w prawym górnym rogu portalu narzędzia QnA Maker.  

    ![Włącz aktywne uczenie alternatywy sugerowanych pytań, na stronie ustawień usługi. Wybierz swoją nazwę użytkownika w prawym górnym menu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker, a następnie przełącz **aktywne uczenie**. 

    [![Na stronie Ustawienia usługi Włącz opcję Aktywne uczenie funkcji. Jeśli nie jest możliwe włączyć tę funkcję, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Gdy **aktywne uczenie** jest włączona, wiedza sugeruje nowe pytania w regularnych odstępach czasu, w oparciu o przesłane przez użytkownika pytań. Możesz wyłączyć **aktywne uczenie** przełączając ustawienia ponownie.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Dodaj sugestię aktywne uczenie do bazy wiedzy

1. Aby można było wyświetlić sugerowane pytania na **Edytuj** bazy wiedzy knowledge base, wybierz **opcje wyświetlania**, a następnie wybierz **Pokaż sugestie dotyczące aktywne uczenie**. 

    [![W sekcji edycji w portalu wybierz Pokaż sugestie, aby można było wyświetlić aktywne uczenie nowe pytanie alternatyw.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtruj wiedzy za pomocą pary pytań i odpowiedzi, aby pokazać tylko sugestie, wybierając **Filtruj według sugestii**.

    [![Użyj filtru za pomocą przełącznika sugestie, aby wyświetlić tylko aktywne uczenie sugerowanych pytań alternatyw.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Każda sekcja zapytania z sugestiami pokazuje nowe pytania ze znacznikiem wyboru `✔` , aby zaakceptować pytanie lub `x` do odrzucenia sugestie. Kliknij znacznik wyboru, aby dodać pytanie. 

    [![Wybierz lub Odrzuć alternatywy sugerowanych pytań aktywne uczenie, wybierając zielony znacznik wyboru lub usuń czerwony znak.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Można dodawać lub usuwać _wszystkie sugestie_ , wybierając **Dodaj wszystkie** lub **Odrzuć wszystkie**.

1. Wybierz **Zapisz i szkolenie** można zapisać zmian w bazie wiedzy knowledge base.

1. Wybierz **Publikuj** do umożliwienia wprowadzania zmian ma być dostępny z poziomu interfejsu API GenerateAnswer.

    5 lub więcej podobne zapytań są klastrowane, co 30 minut narzędzie QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy knowledge base, aby zaakceptować lub odrzucić.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Określenia najlepszym wyborem w przypadku, gdy kilka pytań mają podobne wyniki

Gdy pytanie jest zbyt najbliżej w wyniku inne pytania, Deweloper aplikacji klienckiej można poprosić o wyjaśnienie.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Najważniejsze właściwości należy użyć żądania GenerateAnswer

Podczas przesyłania zapytania do usługi QnA Maker odpowiedź, część treści JSON umożliwia zwracanie więcej niż jedną odpowiedź najważniejsze:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Gdy aplikacja kliencka (takie jak czatbot) odbiera odpowiedź, zwracane są najczęściej zadawane pytania z 3:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Monitowania aplikacji klienta, gdy wyniki podobne pytania

Aplikacja kliencka Wyświetla wszystkie pytania przy użyciu opcji użytkownikowi na wybranie pytanie najlepiej odpowiadającej reprezentuje zamiar. 

Po użytkownik wybiera jeden z istniejących pytań, aplikacja kliencka wysyła wybrany przez użytkownika jako opinii przy użyciu interfejsu API Train usługi QnA Maker. Ta opinia zakończeniu aktywnej nauki sprzężenia zwrotnego. 

Użyj [próbka Azure Bot](https://aka.ms/activelearningsamplebot) Aby wyświetlić aktywne uczenie w scenariuszu end-to-end.

## <a name="train-api"></a>Szkolenie interfejsu API

Aktywne uczenie opinie są wysyłane do usługi QnA Maker żądania POST API pociągu. Podpis interfejsu API jest:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Właściwości żądania HTTP|Name (Nazwa)|Type|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|string|Identyfikator GUID bazy wiedzy.|
|Host poddomeny|Nazwa zasobu QnAMaker|string|Nazwa hosta usługi QnA Maker w subskrypcji platformy Azure. Jest on dostępny na stronie ustawień, po opublikowaniu w bazie wiedzy knowledge base. |
|Nagłówek|Content-Type|string|Typ nośnika treści wysłanej do interfejsu API. Wartość domyślna to: `application/json`|
|Nagłówek|Autoryzacja|string|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Treść wpisu|Obiekt JSON|JSON|Opinie szkolenia|

Treść kodu JSON ma kilka ustawień:

|Właściwość treść JSON|Type|Przeznaczenie|
|--|--|--|--|
|`feedbackRecords`|tablica|Lista opinii.|
|`userId`|string|Identyfikator użytkownika osoby, akceptując sugerowane pytania. Format Identyfikatora użytkownika zależy od użytkownika. Na przykład adres e-mail może być prawidłowy identyfikator użytkownika w ramach architektury. Opcjonalny.|
|`userQuestion`|string|Dokładne tekst pytania. Wymagany.|
|`qnaID`|numer|Identyfikator zapytania, znaleziono w [odpowiedzi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Przykładowy kod JSON wygląda następująco:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Stan 204 i nie treści odpowiedzi JSON zwrócą pomyślne odpowiedzi. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktywna nauka jest zapisywany w bazie wiedzy wyeksportowanego

Gdy Twoja aplikacja ma aktywne uczenie włączone i wyeksportować aplikację, `SuggestedQuestions` kolumna w pliku tsv przechowuje dane aktywne uczenie. 

`SuggestedQuestions` Kolumny jest obiektem JSON informacji niejawne (`autosuggested`) i jawne (`usersuggested`) opinii. Przykładem obiektu JSON do pojedynczego zapytania przesłane przez użytkownika z `help` jest:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Gdy możesz ponownie zaimportować tej aplikacji, aktywne uczenie nadal zbierać informacje i zaleca się sugestie dotyczące wiedzy. 

## <a name="next-steps"></a>Kolejne kroki
 
> [!div class="nextstepaction"]
> [Metadane za pomocą interfejsu API GenerateAnswer](metadata-generateanswer-usage.md)
