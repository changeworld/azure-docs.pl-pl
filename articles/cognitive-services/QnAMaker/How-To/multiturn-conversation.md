---
title: Włączanie wielu konwersacje
titleSuffix: Azure Cognitive Services
description: Umożliwia zarządzanie wielu włącza, znane jako wiele pozycji dla bota z jedno pytanie do innego w monity i kontekstu. Włączanie wielu jest możliwość dysponowania konwersacji Wstecz i w przód, gdzie poprzedniego pytania kontekstu ma wpływ na następny pytań i odpowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508141"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Użyj kontynuacji monit o utworzenie wielu włącza konwersacji

Umożliwia zarządzanie wielu włącza, znane jako kolejnych monitów i kontekst _wielu Włącz_, dla bota z jedno pytanie do innego.

Aby zobaczyć, jak działa wiele Włącz, Wyświetl poniższy klip wideo demonstracyjne:

[![Włączanie wielu konwersacji usługi QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co to jest konwersacji wielu Włącz?

Nie można odpowiedzi na niektóre pytania, jednej pozycji. Podczas projektowania konwersacji (czatbot) aplikacji klienta, użytkownik może poprosić pytanie, na które ma być filtrowana lub, aby określić poprawną odpowiedź. Umożliwia to flow za pośrednictwem pytań poprzez przedstawienie użytkownika za pomocą *monituje monitowania*.

Gdy użytkownik zadaje pytanie, usługa QnA Maker zwraca odpowiedź _i_ żadnych kolejnych monitów. Ta odpowiedź umożliwia prezentowanie dalszych pytań jako opcje. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Przykład wielu Włącz komunikację z czatbot

Włączanie wielu czatbot zarządza konwersacji z użytkownikiem w celu ustalenia końcowego odpowiedzi, jak pokazano na poniższej ilustracji:

![Okno dialogowe Włączanie wielu monitowania, które prowadzą użytkownika przez konwersacji](../media/conversational-context/conversation-in-bot.png)

Na wcześniejszej ilustracji, użytkownik rozpoczął konwersację, wprowadzając **Moje konto**. Baza wiedzy ma trzy połączone pary pytań i odpowiedzi. Aby zawęzić odpowiedź, gdy użytkownik wybierze jeden z trzech opcji w bazie wiedzy knowledge base. Pytanie (1) ma trzy monity monitowania, które są widoczne w czatbot jako trzy opcje (2). 

Gdy użytkownik wybierze opcję (3), będzie wyświetlana lista dalej rafinacja opcje (nr 4). Ta sekwencja będzie kontynuowane (5), dopóki użytkownik określa poprawny, ostateczne odpowiedzi (6).

> [!NOTE]
> Na wcześniejszej ilustracji **włączyć wielu Włącz** zostało zaznaczone pole wyboru, aby upewnić się, że nie są wyświetlane monity. 

### <a name="use-multi-turn-in-a-bot"></a>Włącz wielokrotnego użycia w robota

Aby zarządzać kontekstowych konwersacji, należy zmienić aplikacji klienckiej przez [dodając kod do bota](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Dodając kod umożliwia użytkownikom wyświetlanie monitami.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Tworzenie konwersacji wielu Włącz na podstawie struktury dokumentu

Po utworzeniu bazy wiedzy **wypełnić wiedzy** sekcja wyświetla **włączyć wielu Włącz wyodrębniania z adresów URL, pliki PDF lub .docx** pole wyboru. 

![Pole wyboru włączenia wielu Włącz wyodrębniania](../media/conversational-context/enable-multi-turn.png)

Po wybraniu tej opcji w przypadku zaimportowanych dokumentów, konwersacji wielu Włącz może wynikać z ze struktury dokumentu. Jeśli istnieje tej struktury, usługi QnA Maker tworzy monitowania wiersza tej pary pytań i odpowiedzi w ramach procesu importowania. 

Włącz wielu struktury można wywnioskować tylko z adresów URL, pliki PDF i pliki DOCX. Na przykład struktura wyświetlić obraz [plik PDF ręczne użytkownika Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Ze względu na rozmiar tego pliku PDF, wymaga zasobów usługi QnA Maker **warstwa cenowa wyszukiwania** z **B** (indeksy 15) lub nowszej. 

![! [Przykład struktury w podręczniku użytkownika] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Podczas importowania pliku PDF, narzędzie QnA Maker określa monitowania ze struktury wyświetli monit o utworzenie przepływu konwersacji. 

1. Usługa QnA Maker wybierz **tworzenie bazy wiedzy**.
1. Utwórz lub użyj istniejącej usługi QnA Maker. W powyższym przykładzie Microsoft Surface, ponieważ plik PDF jest zbyt duży dla warstwy mniejszych, użyj usługi QnA Maker za pomocą **usługi wyszukiwania** z **B** (indeksy 15) lub nowszej.
1. Wprowadź nazwę bazy wiedzy, takie jak **powierzchni podręcznika**.
1. Wybierz **włączyć wielu Włącz wyodrębniania z adresów URL, pliki PDF lub .docx** pole wyboru. 
1. Wybierz urządzenia Surface ręczne adres URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Wybierz **tworzenie wiedzy** przycisku. 

    Po utworzeniu bazy wiedzy knowledge base, wyświetlany jest widok pary pytań i odpowiedzi.

## <a name="show-questions-and-answers-with-context"></a>Pokaż pytań i odpowiedzi z kontekstem

Zmniejsz wyświetlanych pary pytań i odpowiedzi tylko do tych kontekstowych konwersacji. 

Wybierz **wyświetlić opcje**, a następnie wybierz pozycję **kontekstu Show (wersja ZAPOZNAWCZA)** . Zostanie wyświetlona lista pary pytań i odpowiedzi, które zawierają kolejnych monitów. 

![Filtruj pary pytań i odpowiedzi według kontekstowych konwersacje](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontekst wielu Włącz jest wyświetlany w pierwszej kolumnie.

![! [Kolumna "kontekst (wersja ZAPOZNAWCZA)"] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na wcześniejszej ilustracji **#1** wskazuje pogrubioną czcionką w kolumnie, który oznacza bieżącego zapytania. Pytanie nadrzędne jest pierwszy element na wiersz. Pytania poniżej są połączone pary pytań i odpowiedzi. Te elementy są można wybierać, tak aby od razu przejść do innych elementów w kontekście. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodawanie istniejącej pary pytań i odpowiedzi jako wiersz monitowania

Oryginalne pytanie zadane **Moje konto**, ma kolejnych monitów, takie jak **konta i zalogowaniu**. 

!["Kont i logowania" odpowiedzi i kolejnych monitów](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Dodaj wiersz monitowania do istniejącej pary pytań i odpowiedzi, która nie jest aktualnie połączony. Ponieważ pytanie nie jest połączony z każdej pary pytań i odpowiedzi, bieżące ustawienie widoku musi zostać zmienione.

1. Aby połączyć istniejącej pary pytań i odpowiedzi jako wiersz monitowania, wybierz wiersz dotyczący pary pytań i odpowiedzi. Dla powierzchni podręcznika, wyszukaj **Wyloguj** zmniejszyć na liście.
1. W wierszu **Signout**w **odpowiedzi** wybierz opcję **Dodaj wiersz monitowania**.
1. W polach **kontynuacji wiersza (wersja ZAPOZNAWCZA)** oknie podręcznym wprowadź następujące wartości:

    |Pole|Wartość|
    |--|--|
    |Wyświetlanie tekstu|Wprowadź **Wyłącz urządzenie**. Jest to niestandardowy tekst do wyświetlenia w wierszu monitowania.|
    |Tylko do kontekstu| Zaznacz to pole wyboru. Odpowiedź jest zwracana tylko wtedy, gdy jest to pytanie Określa kontekst.|
    |Link do odpowiedzi|Wprowadź **ekran logowania** można znaleźć istniejącej pary pytań i odpowiedzi.|


1.  Zwracany jest jedno dopasowanie. Wybierz odpowiedź jako kontynuacji, a następnie wybierz **Zapisz**. 

    ![Na stronie "Monitowania wiersz (wersja ZAPOZNAWCZA)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po dodaniu kolejnych wierszu wybierz **Zapisz i szkolenie** w górnym menu nawigacyjnym.
  
### <a name="edit-the-display-text"></a>Edytowanie tekstu 

Podczas kolejnych wiersza jest tworzony i istniejącej pary pytań i odpowiedzi jest wprowadzana jako **łącze do odpowiedzi**, można wprowadzić nowy **wyświetlania tekstu**. Ten tekst nie zastępuje istniejące zapytania, a nie jest dodawany nowe pytanie alternatywne. Jest oddzielony od tych wartości. 

1. Aby edytować tekst wyświetlany, wyszukaj i wybierz pytanie w **kontekstu** pola.
1. W wierszu to pytanie wybierz opcję monitowania wierszu w kolumnie odpowiedzi. 
1. Zaznacz tekst wyświetlaną chcesz edytować, a następnie wybierz **Edytuj**.

    ![Polecenie edytowania wyświetlanego tekstu](../media/conversational-context/edit-existing-display-text.png)

1. W **kontynuacji wiersza** oknie podręcznym, zmiana istniejących tekstu. 
1. Po zakończeniu edycji tekstu wyświetlanego, wybierz **Zapisz**. 
1. W górnym pasku nawigacyjnym **Zapisz i szkolenie**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodaj nową parę pytań i odpowiedzi jako wiersz monitowania

Po dodaniu nowej pary pytań i odpowiedzi w bazie wiedzy, każda para powinna być połączona istniejącego zapytania jako monitowania wiersza.

1. Na pasku narzędzi w bazie wiedzy knowledge base, wyszukiwanie i Wybieranie istniejącej pary pytań i odpowiedzi dla **konta i zalogowaniu**. 

1. W **odpowiedzi** kolumny na to pytanie, wybierz opcję **Dodaj wiersz monitowania**. 
1. W obszarze **kontynuacji wiersza (wersja ZAPOZNAWCZA)** , Utwórz nowy wiersz monitowania, wprowadzając następujące wartości: 

    |Pole|Wartość|
    |--|--|
    |Wyświetlanie tekstu|*Tworzenie konta Windows*. Niestandardowy tekst do wyświetlenia w wierszu monitowania.|
    |Tylko do kontekstu|Zaznacz to pole wyboru. Ta odpowiedź jest zwracana tylko wtedy, gdy jest to pytanie Określa kontekst.|
    |Link do odpowiedzi|Wprowadź następujący tekst, jako odpowiedź na pytanie:<br>*[Tworzenie](https://account.microsoft.com/) konta Windows, za pomocą konta e-mail nowego lub istniejącego*.<br>Po zapisaniu i uczenie bazy danych zostaną przekonwertowane ten tekst. |
    |||

    ![Utwórz nowy monitu pytań i odpowiedzi](../media/conversational-context/create-child-prompt-from-parent.png)


1. Wybierz **Utwórz nową**, a następnie wybierz pozycję **Zapisz**. 

    Ta akcja powoduje utworzenie nowej pary pytań i odpowiedzi oraz łączy wybranego pytania jako monitowania wiersza. **Kontekstu** kolumny na oba pytania wskazuje monitowania relacji monitu. 

1. Wybierz **wyświetlić opcje**, a następnie wybierz pozycję [ **kontekstu Show (wersja ZAPOZNAWCZA)** ](#show-questions-and-answers-with-context).

    Nowe pytanie pokazuje, jak jest połączony.

    ![Utwórz nowy wiersz monitowania](../media/conversational-context/new-qna-follow-up-prompt.png)

    Pytanie nadrzędne wyświetla nowe pytanie jako jeden z jego możliwości.

    ![! [Kolumnie kontekstu na oba pytania wskazuje relację monitu monitowania] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po dodaniu kolejnych wierszu wybierz **Zapisz i szkolenie** w górnym pasku nawigacyjnym.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Włączanie wielu Włącz podczas testowania kolejnych monitów

Podczas testowania pytania za pomocą funkcji monitowania monity w **Test** okienku wybierz **włączyć wielu Włącz**, a następnie wprowadź swoje pytanie. Odpowiedź zawiera kolejnych monitów.

![Odpowiedź zawiera kolejnych monitów](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Jeśli nie włączysz Włącz wielu, odpowiedź jest zwracana, ale kolejnych monitów nie są zwracane.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Żądanie JSON, aby zwrócić odpowiedź początkowej i kolejnych monitów

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Odpowiedź w formacie JSON do zwrócenia wiążącej początkowej i kolejnych monitów

Zażądano poprzedniej sekcji, odpowiedzi i wszystkie monity monitowania o **konta i zalogowaniu**. Odpowiedź zawiera szybkie informacje, które znajduje się w folderze *.context odpowiedzi [0]* , a tekst do wyświetlenia dla użytkownika. 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

`prompts` Tablicy zawiera tekst w `displayText` właściwości i `qnaId` wartość. Odpowiedzi na te pytania można wyświetlić, jak dalej opcji wyświetlanych w konwersacji przepływu, a następnie wyślij wybranego `qnaId` powrót do usługi QnA Maker w poniższych dokumentach. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Żądania JSON do zwrócenia odpowiedzi bez początkowej i kolejnych monitów

Wypełnij `context` obiektu do uwzględnienia poprzedniego kontekstu.

W poniższych dokumentach JSON bieżące pytanie jest *Użyj Windows Hello do logowania* , ale z poprzedniego pytania *konta i zalogowaniu*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpowiedź w formacie JSON do zwrócenia odpowiedzi bez początkowej i kolejnych monitów

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Zapytania w bazie wiedzy knowledge base identyfikatorem usługi QnA Maker

Odpowiedź na pytanie początkowej, wszystkie monity monitowania i związanych z nią `qnaId` jest zwracana. Teraz, gdy masz identyfikator, możesz przekazać ten wiersz monitowania treści żądania. Jeśli treść żądania zawiera `qnaId`i obiektu kontekstu (który zawiera poprzednie właściwości usługi QnA Maker), a następnie GenerateAnswer zwróci dokładnie zapytania według identyfikatorów, zamiast przy użyciu algorytmu klasyfikacji, aby znaleźć odpowiedzi przez tekst pytania. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Wyświetlane monity i wysłać kontekstu w aplikacji klienckiej 

Dodano monity w Twojej bazie wiedzy knowledge base i badać przepływ w okienko testowania. Teraz należy użyć tych monitów w aplikacji klienckiej. Dla platformy Bot Framework monity nie są automatycznie wyświetlane w aplikacjach klienckich. Można wyświetlić monity jako sugerowane akcje lub przyciski jako część odpowiedzi na zapytania użytkownika w aplikacjach klienckich umieszczając to [przykładowe platformy Bot Framework](https://aka.ms/qnamakermultiturnsample) w kodzie. Aplikacja kliencka przechowywania bieżący identyfikator usługi QnA Maker i kwerenda użytkownika i przekazywać je w [obiektu kontekstu interfejsu API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) dalej zapytania użytkownika. 

## <a name="display-order-is-supported-in-the-update-api"></a>Kolejność wyświetlania jest obsługiwana w interfejs API aktualizacji

[Wyświetlania tekstu i kolejność wyświetlania](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), zwracany w odpowiedzi JSON, jest obsługiwana w przypadku edycji przez [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tworzenie bazy wiedzy knowledge base wielu Włącz monitowania za pomocą interfejsu API tworzenia

Włącz wielu monitowania za pomocą może utworzyć przypadek wiedzy [interfejsu API usługi QnA Maker tworzenia](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Dodajesz monity w `context` właściwości `prompts` tablicy. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Dodawanie lub usuwanie wielu Włącz monity z interfejsem API aktualizacji

Można dodawać lub usuwać monity Włącz wielu przy użyciu [interfejs API usługi QnA Maker Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Dodajesz monity w `context` właściwości `promptsToAdd` tablicy i `promptsToDelete` tablicy. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o kontekstowych rozmowy z tego [przykładowe okno dialogowe](https://aka.ms/qnamakermultiturnsample) lub Dowiedz się więcej o [koncepcyjny bot projektowanie dla wielu Włącz konwersacji](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
