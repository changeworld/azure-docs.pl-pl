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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479742"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Użyj kontynuacji monit o utworzenie wielu włącza konwersacji

Umożliwia zarządzanie wielu włącza, znane jako kolejnych monitów i kontekst _wielu Włącz_, dla bota z jedno pytanie do innego.

Obejrzyj poniższy klip wideo demonstracyjne, aby zobaczyć, jak to się robi.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Co to jest konwersacji wielu Włącz?

Nie można odpowiedzi na niektóre pytania, jednej pozycji. Podczas projektowania konwersacji (czatbot) aplikacji klienta, użytkownik może poprosić pytanie, na które musi być filtrowana lub Elegancja, aby określić poprawną odpowiedź. Ten przepływ, za pomocą pytań jest możliwe poprzez przedstawienie użytkownikowi **monituje monitowania**.

Gdy użytkownik poleca pytanie, usługa QnA Maker zwraca odpowiedź _i_ żadnych kolejnych monitów. Dzięki temu można prezentować dalszych pytań jako opcje. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Przykład wielu Włącz komunikację z czatbot

Czatbot zarządza konwersacji z użytkownikiem, pytanie za zapytania, aby określić ostateczny odpowiedzi.

![W ramach przepływu konwersacji Zarządzanie stan konwersacji w systemie wielu Włącz okno dialogowe, zapewniając monity w odpowiedzi przedstawione jako opcji, aby kontynuować rozmowę.](../media/conversational-context/conversation-in-bot.png)

Na wcześniejszej ilustracji, użytkownik wprowadzi `My account`. Baza wiedzy ma 3 połączone pary pytań i odpowiedzi. Użytkownik musi wybrać jeden z trzech opcji, aby doprecyzować odpowiedź. W bazie wiedzy knowledge base zapytania (1) ma trzech kolejnych monitów, przedstawione czatbot jako trzy opcje (2). 

Po wybraniu opcji wyboru (3), będzie wyświetlana lista dalej rafinacja opcje (nr 4). To może być kontynuowane (5) do odpowiedzi prawidłowe i końcowego (6) jest określana.

Ma na powyższej ilustracji **włączyć wielu Włącz** wybrano, aby można było wyświetlane monity. 

### <a name="using-multi-turn-in-a-bot"></a>Używanie wielu Włącz w robota

Musisz zmienić Zarządzanie konwersacji kontekstowych aplikacji klienckiej. Należy dodać [kod, aby Twój bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) się pojawiać się monity.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Tworzenie konwersacji wielu Włącz na podstawie struktury dokumentu

Po utworzeniu bazy wiedzy, zobaczysz opcjonalne pole wyboru umożliwiające włączanie wielu wyodrębniania. 

![Po utworzeniu bazy wiedzy, zobaczysz opcjonalne pole wyboru umożliwiające włączanie wielu wyodrębniania.](../media/conversational-context/enable-multi-turn.png)

Wybranie tej opcji podczas importowania dokumentu konwersacji Włączanie wielu może wynikać z ze struktury. Jeśli istnieje tej struktury, narzędzie QnA Maker tworzy monitowania monitu pary pytań i odpowiedzi. 

Struktura Włącz wielu można wywnioskować tylko na podstawie adresów URL, PDF lub DOCX plików. 

Poniższy obraz Microsoft Surface [plik PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) jest przeznaczona do użycia jako ręcznego. Ze względu na rozmiar tego pliku PDF, zasób usługi QnA Maker Azure wymaga wyszukiwania ceny warstwy B (indeksy 15) lub nowszej. 

![! [Jeśli importujesz dokumentu, kontekstowe konwersacji może wynikać ze struktury. Istnienia tej struktury usługi QnA Maker tworzy monitowania monitu pary pytań i odpowiedzi, w ramach importowania dokumentu.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Podczas importowania pliku PDF, narzędzie QnA Maker określa kolejnych monitów ze struktury, aby utworzyć przepływ konwersacji. 

1. W **kroku 1**, wybierz opcję **tworzenie bazy wiedzy** w górnym menu nawigacyjnym.
1. W **kroku 2**, tworzenia lub użyć istniejącej usługi pytań i odpowiedzi. Upewnij się, że usługa pytań i odpowiedzi z usługą wyszukiwania b (indeksy 15) lub nowszej, ponieważ powierzchni podręcznika plik PDF jest zbyt duży dla mniejszych warstwy.
1. W **kroku 3**, wprowadź nazwę bazy wiedzy, takie jak `Surface manual`.
1. W **kroku 4**, wybierz opcję **włączyć wielu Włącz wyodrębniania z adresów URL, pliki PDF lub docx.** Wybierz adres URL dla powierzchni podręcznika

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Wybierz **tworzenie wiedzy** przycisku. 

    Po utworzeniu wiedzy Wyświetla widok pary pytań i odpowiedzi.

## <a name="show-questions-and-answers-with-context"></a>Pokaż pytań i odpowiedzi z kontekstem

Zmniejsz pary pytań i odpowiedzi, wyświetlane tylko dla osób z kontekstowych konwersacji. 

1. Wybierz **wyświetlić opcje**, a następnie wybierz **kontekstu Show (wersja ZAPOZNAWCZA)** . Lista zawiera pary pytań i odpowiedzi, zawierający kolejnych monitów. 

    ![Filtrowanie zapytania i odpowiedzi pary przez kontekstowych konwersacje](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Kontekst wielu Włącz wyświetla się w pierwszej kolumnie.

    ![! [Podczas importowania pliku PDF, narzędzie QnA Maker określa kolejnych monitów ze struktury, aby utworzyć przepływ konwersacji. ](.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Na wcześniejszej ilustracji #1 wskazuje pogrubioną czcionką w kolumnie, który oznacza bieżącego zapytania. Pytanie nadrzędne jest pierwszy element na wiersz. Poniższe pytania są połączone pary pytań i odpowiedzi. Te elementy są można wybierać, dzięki czemu możesz od razu przejść do innych elementów w kontekście. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Dodawanie istniejącej pary pytań i odpowiedzi jako wiersz monitowania

Pytanie, oryginalnym `My account` zawiera kolejnych monitów, takie jak `Accounts and signing in`. 

![Oryginalne pytanie zadane na moim koncie poprawnie zwraca "Kont i logowania" odpowiedzi i ma już kolejnych monitów, połączone.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Dodaj wiersz monitowania do istniejącej pary pytań i odpowiedzi, która nie jest aktualnie połączony. Ponieważ pytanie nie jest połączony z każdej pary pytań i odpowiedzi, bieżące ustawienie widoku wymaga wprowadzenia zmian.

1. Aby połączyć istniejącej pary pytań i odpowiedzi jako wiersz monitowania, wybierz wiersz dotyczący pary pytań i odpowiedzi. Dla powierzchni podręcznika, wyszukaj `Sign out` zmniejszyć na liście.
1. W wierszu `Signout`, wybierz opcję **Dodaj wiersz monitowania** z **odpowiedzi** kolumny.
1. W **kontynuacji wiersza (wersja ZAPOZNAWCZA)** oknie podręcznym wprowadź następujące:

    |Pole|Wartość|
    |--|--|
    |Wyświetlanie tekstu|`Turn off the device`. Jest to niestandardowy tekst, który powoduje wyświetlenie kolejnych wiersza.|
    |Tylko do kontekstu|Wybrany. Ta odpowiedź tylko zostanie zwrócona, jeśli pytanie Określa kontekst.|
    |Link do odpowiedzi|Wprowadź `Use the sign-in screen` można znaleźć istniejącej pary pytań i odpowiedzi.|


1.  Zwracany jest jedno dopasowanie. Wybierz odpowiedź jako kontynuacji, a następnie wybierz **Zapisz**. 

    ![Wyszukaj wiersz monitowania łącza do okna dialogowego odpowiedzi, odpowiedź, za pomocą tekst odpowiedzi.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po dodaniu kolejnych wiersza, należy pamiętać o zaznaczeniu **Zapisz i szkolenie** w górnym menu nawigacyjnym.
  
### <a name="edit-the-display-text"></a>Edytowanie tekstu 

Podczas kolejnych wiersza jest tworzony i istniejącej pary pytań i odpowiedzi jest wybrany jako **łącze do odpowiedzi**, można wprowadzić nowy **wyświetlania tekstu**. Ten tekst nie zastępuje istniejące zapytania, a nie dodaje nowe pytanie alternatywne. Jest oddzielony od tych wartości. 

1. Aby edytować tekst wyświetlany, wyszukaj i wybierz pytanie w **kontekstu** pola.
1. W wierszu to pytanie należy zaznaczyć monitowania wierszu w kolumnie odpowiedzi. 
1. Zaznacz tekst wyświetlaną chcesz edytować, a następnie wybierz **Edytuj**.

    ![Wybierz wyświetlany tekst, który chcesz edytować, a następnie wybierz pozycję Edytuj.](../media/conversational-context/edit-existing-display-text.png)

1. **Kontynuacji wiersza** okno podręczne umożliwia zmianę istniejących wyświetlany tekst. 
1. Po zakończeniu edycji tekstu wyświetlanego, wybierz **Zapisz**. 
1. Należy pamiętać o zaznaczeniu **Zapisz i szkolenie** w górnym menu nawigacyjnym.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Dodaj nową parę pytań i odpowiedzi jako wiersz monitowania

Dodaj nową parę pytań i odpowiedzi w bazie wiedzy. Pary pytań i odpowiedzi powinna być połączona istniejącego zapytania jako monitowania wiersza.

1. Z poziomu paska narzędzi w bazie wiedzy, wyszukiwanie i Wybieranie istniejącej pary pytań i odpowiedzi dla `Accounts and Signing In`. 

1. W **odpowiedzi** kolumny na to pytanie, wybierz opcję **Dodaj wiersz monitowania**. 
1. **Kontynuacji wiersza (wersja ZAPOZNAWCZA)** , Utwórz nowy wiersz monitowania, wprowadzając następujące wartości: 

    |Pole tekstowe|Wartość|
    |--|--|
    |**Wyświetlanie tekstu**|`Create a Windows Account`. Jest to niestandardowy tekst, który powoduje wyświetlenie kolejnych wiersza.|
    |**Tylko do kontekstu**|Wybrany. Ta odpowiedź tylko zostanie zwrócona, jeśli pytanie Określa kontekst.|
    |**Link do odpowiedzi**|Wprowadź następujący tekst, jako odpowiedź na pytanie:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Po zapisaniu i uczenie bazy danych, ten tekst zostanie przekształcony na |
    |||

    ![Tworzenie nowych pytań i odpowiedzi monitu](../media/conversational-context/create-child-prompt-from-parent.png)


1. Wybierz **Utwórz nową** polecenie **Zapisz**. 

    Utworzyć nową parę pytań i odpowiedzi, a połączone wybranego pytania jako monitowania wiersza. **Kontekstu** kolumny na oba pytania wskazuje monitowania relacji monitu. 

1. Zmiana **wyświetlić opcje** do [Pokaż kontekstu](#show-questions-and-answers-with-context).

    Nowe pytanie pokazuje, jak jest połączony.

    ![Utwórz nowy wiersz monitowania ](../media/conversational-context/new-qna-follow-up-prompt.png)

    Pytanie nadrzędne pokazuje nowe pytanie jako jeden z jego możliwości.

    ![! [Kolumnie kontekstu na oba pytania wskazuje relację monitu monitowania.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po dodaniu kolejnych wiersza, należy pamiętać o zaznaczeniu **Zapisz i szkolenie** w górnym menu nawigacyjnym.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Włączanie wielu Włącz podczas testowania monitowania monity

Podczas testowania pytania za pomocą funkcji monitowania monity w **testu** okienku wybierz pozycję **włączyć wielu Włącz**, a następnie wprowadź swoje pytanie. Odpowiedź zawiera kolejnych monitów.

![Podczas testowania na pytanie, w okienku testu, odpowiedź zawiera kolejnych monitów.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Jeśli nie włączysz Włącz wielu, zostanie zwrócona odpowiedź, ale kolejnych monitów nie są zwracane.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Żądania JSON do zwrócenia odpowiedzi początkowej i kolejnych monitów

Użyj pustych `context` obiekt, aby żądania odpowiedzi na pytania użytkownika i obejmują kolejnych monitów. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` Tablicy zawiera tekst w `displayText` właściwości i `qnaId` wartości, więc odpowiedzi na te pytania można wyświetlić jako dalej opcji wyświetlanych w komunikacji usługi flow, następnie wyślij wybranej wartości, aby usługa QnA Maker w poniższych dokumentach. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Żądania JSON do zwrócenia odpowiedzi bez początkowej i kolejnych monitów

Wypełnij `context` obiektu do uwzględnienia poprzedniego kontekstu.

W poniższych dokumentach JSON bieżące pytanie jest `Use Windows Hello to sign in` , ale z poprzedniego pytania `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Zapytania w bazie wiedzy o identyfikatorze pytań i odpowiedzi

Odpowiedź na pytanie początkowej, wszystkie monity monitowania i związanych z nią `qnaId` jest zwracana. Teraz, gdy masz identyfikator, możesz przekazać ten wiersz monitowania treści żądania. Jeśli treść żądania zawiera `qnaId`i obiektu kontekstu (który zawiera poprzednie właściwości pytań i odpowiedzi), a następnie GenerateAnswer zwróci dokładnie zapytania według identyfikatorów, zamiast przy użyciu algorytmu klasyfikacji, aby znaleźć odpowiedzi przez tekst pytania. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Wyświetlanie monitów, a następnie wysyłając kontekstu w aplikacji klienckiej 

Dodano monity w Twojej bazie wiedzy knowledge base i badać przepływ w okienko testowania. Teraz należy użyć tych monitów w aplikacji klienckiej. Dla platformy Bot Framework monity automatycznie rozpocznie się pojawią się w aplikacjach klienckich. Możesz ukazywać monity jako sugerowane akcje lub przyciski jako część odpowiedzi na zapytania użytkownika w kliencie aplikacji umieszczając to [przykładowe platformy Bot Framework](https://aka.ms/qnamakermultiturnsample) w kodzie. Aplikacja kliencka przechowywania bieżący identyfikator pytań i odpowiedzi oraz kwerenda użytkownika i przekazywać je w [obiektu kontekstu interfejsu API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) dalej zapytania użytkownika. 

## <a name="display-order-supported-in-api"></a>Kolejność wyświetlania obsługiwane w interfejsie API

[Wyświetlania tekstu i kolejność wyświetlania](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), zwracany w odpowiedzi JSON, jest obsługiwana w przypadku edycji przez [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o kontekstowych rozmowy z [przykładowe okno dialogowe](https://aka.ms/qnamakermultiturnsample) lub Dowiedz się więcej [koncepcyjny bot projektowanie dla wielu Włącz konwersacji](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
