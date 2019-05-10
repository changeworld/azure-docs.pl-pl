---
title: Włączanie wielu konwersacje
titleSuffix: Azure Cognitive Services
description: Umożliwia zarządzanie wielu włącza, znane jako wiele pozycji dla bota z jedno pytanie do innego w monity i kontekstu. Włączanie wielu jest możliwość kopii i do przodu konwersacji, gdzie poprzedniego pytania kontekstu ma wpływ na następny pytań i odpowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 24158d5949c0a31e5444c609cbfced970bdeca07
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471961"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Użyj kontynuacji monit o utworzenie wielu włącza konwersacji

Umożliwia zarządzanie wielu włącza, znane jako kolejnych monitów i kontekst _wielu Włącz_, dla bota z jedno pytanie do innego.

Ucz się od [pokaz wideo](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Co to jest konwersacji wielu Włącz?

Niektóre typy konwersacji nie można ukończyć jednej pozycji. Podczas projektowania konwersacji (czatbot) aplikacji klienta, użytkownik może poprosić pytanie, na które musi być filtrowana lub Elegancja, aby określić poprawną odpowiedź. Ten przepływ, za pomocą pytań jest możliwe poprzez przedstawienie użytkownikowi **monituje monitowania**.

Gdy użytkownik poleca pytanie, usługa QnA Maker zwraca odpowiedź _i_ żadnych kolejnych monitów. Dzięki temu można prezentować dalszych pytań jako opcje. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Przykład wielu Włącz komunikację z czatbot

Czatbot zarządza konwersacji, zapytania przez zapytania z użytkownikiem w celu ustalenia końcowego odpowiedzi.

![W ramach przepływu konwersacji Zarządzanie stan konwersacji w systemie wielu Włącz okno dialogowe, zapewniając monity w odpowiedzi przedstawione jako opcji, aby kontynuować rozmowę.](../media/conversational-context/conversation-in-bot.png)

Na wcześniejszej ilustracji pytanie użytkownika musi być Elegancja przed zwróceniem odpowiedzi. W bazie wiedzy pytanie (1) ma cztery kolejnych monitów, przedstawione czatbot jako czterech wyborów, (2). 

Po wybraniu opcji wyboru (3), będzie wyświetlana lista dalej rafinacja opcje (nr 4). To może być kontynuowane (5) do odpowiedzi prawidłowe i końcowego (6) jest określana.

Musisz zmienić Zarządzanie konwersacji kontekstowych aplikacji klienckiej.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Tworzenie konwersacji wielu Włącz na podstawie struktury dokumentu
Po utworzeniu bazy wiedzy, zobaczysz opcjonalne pole wyboru umożliwiające włączanie wielu wyodrębniania. Wybranie tej opcji podczas importowania dokumentu konwersacji Włączanie wielu może wynikać z ze struktury. Jeśli istnieje tej struktury, narzędzie QnA Maker tworzy monitowania monitu pary pytań i odpowiedzi. Struktura Włącz wielu można wywnioskować tylko na podstawie adresów URL, PDF lub DOCX plików. 

Poniższy obraz Microsoft Surface [plik PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) jest przeznaczona do użycia jako ręcznego. 

![! [Jeśli importujesz dokumentu, kontekstowe konwersacji może wynikać ze struktury. Istnienia tej struktury usługi QnA Maker tworzy monitowania monitu pary pytań i odpowiedzi, w ramach importowania dokumentu.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Podczas importowania pliku PDF, narzędzie QnA Maker określa kolejnych monitów ze struktury, aby utworzyć przepływ konwersacji. 

![! [Podczas importowania pliku PDF, narzędzie QnA Maker określa kolejnych monitów ze struktury, aby utworzyć przepływ konwersacji. ](.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="filter-questions-and-answers-by-context"></a>Filtruj pytań i odpowiedzi od kontekstu

1. Zmniejsz pary pytań i odpowiedzi, wyświetlane tylko dla osób z kontekstowych konwersacji. Wybierz **wyświetlić opcje**, a następnie wybierz **kontekstu Show (wersja ZAPOZNAWCZA)**. Lista jest pusta, do momentu dodania pierwszego pary pytań i odpowiedzi wraz z monitem o kolejnych. 

    ![Filtrowanie zapytania i odpowiedzi pary przez kontekstowych konwersacje](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Dodaj nową parę pytań i odpowiedzi jako wiersz monitowania

1. Wybierz **pary dodawanie pytań i odpowiedzi**. 
1. Wprowadź nowy tekst pytania `Give feedback.` z odpowiedzi na pytanie `What kind of feedback do you have?`.

1. W **odpowiedzi** kolumny na to pytanie, wybierz opcję **Dodaj wiersz monitowania**. 
1. **Kontynuacji wiersza** wyskakujące okno dialogowe umożliwia wyszukiwanie istniejących pytanie lub wprowadź nowe pytanie. W tej procedurze, wprowadź tekst `Feedback on an QnA Maker service`, aby uzyskać **wyświetlania tekstu**. 
1. Sprawdź **tylko do kontekstu**. **Tylko do kontekstu** opcja wskazuje tekst tego użytkownika będą rozumieć _tylko_ Jeśli podane w odpowiedzi na poprzednie pytanie. W tym scenariuszu tekst monitu nie ma sensu wszelkie jako autonomiczny pytanie, tylko sens w kontekście poprzedniego pytania.
1. W **Link, aby odpowiedzieć** tekstu odpowiedzi, wprowadź `How would you rate QnA Maker?`.
1. Wybierz **Utwórz nową** polecenie **Zapisz**. 

    ![Tworzenie nowych pytań i odpowiedzi monitu](../media/conversational-context/create-child-prompt-from-parent.png)

    Utworzyć nową parę pytań i odpowiedzi, a połączone wybranego pytania jako monitowania wiersza. **Kontekstu** kolumny na oba pytania wskazywać monitowania monitu. 

    ![! [Kolumnie kontekstu na oba pytania wskazuje relację monitu monitowania.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Wybierz **Dodaj wiersz monitowania** dla `Give feedback` pytanie, aby dodać innego wiersza monitowania. 
1. Utwórz nowe pytanie, wprowadzając `Feedback on an existing feature`, z odpowiedzią `Which feature would you like to give feedback on?`.  

1.  Sprawdź **tylko do kontekstu**. **Tylko do kontekstu** opcja wskazuje tekst tego użytkownika będą rozumieć _tylko_ Jeśli podane w odpowiedzi na poprzednie pytanie. W tym scenariuszu tekst monitu nie ma sensu wszelkie jako autonomiczny pytanie, tylko sens w kontekście poprzedniego pytania.
1.  Wybierz pozycję **Zapisz**. 

    To utworzone nowe pytanie i połączone pytanie jako monitowania pytanie monitu `Give feedback` pytanie.
    
    W tym momencie najważniejsze zapytania ma dwa monity monitowania łączone z poprzedniego pytania `Give feedback`.

    ![! [W tym momencie najważniejsze zapytania ma dwa monity monitowania łączone z poprzedniego pytania "Prześlij opinię"]. (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Wybierz **Zapisz i szkolenie** to w opracowywaniu wiedzy o nowe pytania. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Dodawanie istniejącej pary pytań i odpowiedzi jako wiersz monitowania

1. Jeśli chcesz połączyć istniejącej pary pytań i odpowiedzi jako monitowania wiersz, wybierz wiersz dotyczący pary pytań i odpowiedzi.
1. Wybierz **Dodaj wiersz monitowania** w tym wierszu.
1. W wyskakującym oknie dialogowym wprowadź tekst pytania w polu wyszukiwania. Zwracane są wszystkie dopasowania. Wybierz pytanie, co poprawia komunikację i sprawdź **tylko do kontekstu**, a następnie wybierz **Zapisz**. 

    Gdy jednostki organizacyjnej dodano kolejnego wiersza, należy pamiętać o zaznaczeniu **Zapisz i szkolenie**.
  
## <a name="add-metadata-to-follow-up-prompts"></a>Dodaj metadane do kolejnych monitów 

W bazie wiedzy knowledge base powiązane pary pytań i odpowiedzi wyświetli monitowania, filtrów metadanych są stosowane najpierw, a następnie przegapisz odpowiedzi członka są zwracane.

1. Dla dwóch kolejnych par pytań i odpowiedzi Dodaj metadane do każdego z nich:

    |Pytanie|Dodawanie metadanych|
    |--|--|
    |`Feedback on an QnA Maker service`|"Funkcja": "all"|
    |`Feedback on an existing feature`|"Funkcja": "jeden"|
    
    ![Dodaj metadane do kolejnego wiersza, dzięki czemu można je filtrować w konwersacji odpowiedź z usługi](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Zapisz i szkolenie. 

    Podczas wysyłania zapytania `Give feedback` z filtrem metadanych `Feature` o wartości `all`, zostaną zwrócone tylko pary pytań i odpowiedzi za pomocą tych metadanych. Pary pytań i odpowiedzi nie są zwracane, ponieważ oba te produkty nie są zgodne z filtrem. 

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Wyświetla monit o testów ustawione pytań i odpowiedzi w celu uzyskania wszystkich monitowania

Podczas testowania pytania za pomocą funkcji monitowania monity w **testu** okienku wybierz pozycję **włączyć wielu Włącz**, a następnie wprowadź swoje pytanie. Odpowiedź zawiera kolejnych monitów.

![Podczas testowania na pytanie, w okienku testu, odpowiedź zawiera kolejnych monitów.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Żądania JSON do zwrócenia odpowiedzi początkowej i kolejnych monitów

Użyj pustych `context` obiekt, aby żądania odpowiedzi na pytania użytkownika i obejmują kolejnych monitów. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Odpowiedź w formacie JSON do zwrócenia odpowiedzi początkowej i kolejnych monitów

Zażądano poprzedniej sekcji, odpowiedzi i wszystkie monity monitowania o `Accounts and signing in`. Odpowiedź zawiera informacje prompt, znajdujący się w `answers[0].context`, zawierają tekst do wyświetlenia dla użytkownika. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

`prompts` Tablicy zawiera tekst w `displayText` właściwości i `qnaId` wartości, dzięki czemu odpowiedzi na te pytania można wyświetlić jako dalej opcji wyświetlanych w usłudze flow konwersacji. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Żądania JSON do zwrócenia odpowiedzi bez początkowej i kolejnych monitów

Wypełnij `context` obiektu do uwzględnienia poprzedniego kontekstu.

W poniższych dokumentach JSON bieżące pytanie jest `Use Windows Hello to sign in` , ale z poprzedniego pytania `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Odpowiedź w formacie JSON do zwrócenia odpowiedzi bez początkowej i kolejnych monitów

Usługa QnA Maker _GenerateAnswer_ odpowiedź JSON zawiera kolejnych monitów w `context` właściwość pierwszy element `answers` obiektu:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Wyświetlanie monitów, a następnie wysyłając kontekstu w aplikacji klienckiej 

Aplikacja kliencka Wyświetla wszystkie pytania z opcją użytkownikowi monity są wyświetlane jako sugerowane akcje lub przyciski.
Następnie aplikacja kliencka przechowuje bieżącego zapytania pary i użytkownika pytań i odpowiedzi jako kontekst, które mogą być przekazane następne zapytanie użytkownika. 

Użyj tego [przykładowe platformy Bot Framework](https://aka.ms/qnamakermultiturnsample) Aby wyświetlić okno dialogowe Włączanie wielu pracy end-to-end w bot usługi QnA Maker.


## <a name="prompt-order-supported-in-api"></a>Porządek monitów, obsługiwane w interfejsie API

Porządek monitów, zwrócona w odpowiedzi JSON jest obsługiwana przez interfejs API tylko do edycji. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o kontekstowych rozmowy z [przykładowe okno dialogowe](https://aka.ms/qnamakermultiturnsample) lub Dowiedz się więcej [koncepcyjny bot projektowanie dla wielu Włącz konwersacji](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
