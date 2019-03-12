---
title: Poprawa wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/05/2019
ms.author: diberry
ms.openlocfilehash: 76005b153d7a7feabdc1b335a23c6aa1f1fa99f3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537902"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Ulepszanie bazy wiedzy za pomocą uczenia aktywnego

Aktywna nauka można poprawić jakość bazy wiedzy w postaci sugerowanych alternatywnych pytania, oparte na użytkownika — wiadomości, do Twojego pary pytań i odpowiedzi. Albo dodanie ich do istniejącego pytania lub odrzucenia ich przejrzenie tych propozycji. 

Bazy wiedzy nie zmienia się automatycznie. Sugestie, aby zmiany zaczęły obowiązywać, należy zaakceptować. Tego rodzaju sugestie dodać pytania, ale nie Zmień lub usuń istniejące pytania.

## <a name="active-learning"></a>Aktywne uczenie

Usługa QnA Maker uzyskuje informacje o nowych zmian pytanie jawne i niejawne opinii.
 
* Niejawne opinia — tematycznie rozumie, jeśli pytanie użytkownika ma wiele odpowiedzi za pomocą wyniki, które są bardzo podobne i uznaje jako opinii. 
* Jawne opinie — gdy wiele odpowiedzi za pomocą niewielkie różnice w wyniki są zwracane z bazy wiedzy knowledge base, aplikacja kliencka prosi użytkownika zapytania, które jest poprawne pytanie. Jawne opinii użytkownika są wysyłane do usługi QnA Maker przy użyciu interfejsu API pociągu. 

Każda z tych metod zapewnia tematycznie przy użyciu podobnych zapytań, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak aktywne uczenie działa

Aktywne uczenie jest wyzwalana, oparte na wyniki najważniejsze odpowiedzi kilka zwrócone przez narzędzie QnA Maker dla dowolnej podanej kwerendy. Różnice wynik leży w obrębie małe, a następnie zapytanie jest traktowane jako potencjalnie _sugestii_ dla wszystkich możliwych odpowiedzi. 

Wszystkie sugestie są zgrupowane razem wg podobieństwa i najbardziej oczekiwanych alternatywne pytania są wyświetlane na podstawie częstotliwości określonej zapytań przez użytkowników końcowych. Aktywna nauka zapewnia najlepsze możliwe sugestie w przypadkach, gdzie punkty końcowe są objęte ilość uzasadnione i różne zapytania do użycia.

5 lub więcej podobne zapytań są klastrowane, co 30 minut narzędzie QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy knowledge base, aby zaakceptować lub odrzucić.

Po pytania są sugerowane w portalu narzędzia QnA Maker, należy przejrzeć i zaakceptować lub odrzucić te sugestie. 

## <a name="upgrade-version-to-use-active-learning"></a>Uaktualnij wersję na korzystanie z aktywnej nauki

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

    ![Na stronie Ustawienia usługi Włącz opcję Aktywne uczenie](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker, a następnie przełącz **aktywne uczenie**. 

    [![Na stronie Ustawienia usługi Włącz opcję Aktywne uczenie](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Gdy **aktywne uczenie** jest włączona, wiedza sugeruje nowe pytania w regularnych odstępach czasu, w oparciu o przesłane przez użytkownika pytań. Możesz wyłączyć **aktywne uczenie** przełączając ustawienia ponownie.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Dodaj sugestię aktywne uczenie do bazy wiedzy

1. Aby można było wyświetlić sugerowane pytania na **Edytuj** bazy wiedzy knowledge base, wybierz **Pokaż sugestie**. 

    [![Na stronie Ustawienia usługi przycisk Pokaż sugestie dotyczące przełączania](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtruj wiedzy za pomocą pary pytań i odpowiedzi, aby pokazać tylko sugestie, wybierając **Filtruj według sugestii**.

    [![Na stronie Ustawienia usługi filtrowanie według sugestii, aby wyświetlić tylko te pytania/odpowiedzi par](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Każda sekcja zapytania z sugestiami pokazuje nowe pytania ze znacznikiem wyboru `✔` , aby zaakceptować pytanie lub `x` do odrzucenia sugestie. Kliknij znacznik wyboru, aby dodać pytanie. 

    [![Na stronie Ustawienia usługi Włącz opcję Aktywne uczenie](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

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

Gdy użytkownik wybierze jeden z istniejących pytań. Opinie użytkowników są wysyłane do usługi QnA Maker [Train](https://www.aka.ms/activelearningsamplebot) interfejsu API, aby kontynuować opinii aktywne uczenie pętli. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Dowiedz się więcej o sposobie używania aktywne uczenie z [Azure Bot C# przykład](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>Aktywna nauka są zapisywane w pliku tsv Wyeksportowano aplikację

Gdy Twoja aplikacja ma aktywne uczenie włączone i wyeksportować aplikację, `SuggestedQuestions` kolumna w pliku tsv przechowuje dane aktywne uczenie. 

`SuggestedQuestions` Kolumny jest obiektem JSON informacji niejawne (`autosuggested`) i jawne (`usersuggested`) [opinii](#active-learning). Przykładem obiektu JSON do pojedynczego zapytania przesłane przez użytkownika z `help` jest:

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
> [Użyj usługi QnA Maker API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
