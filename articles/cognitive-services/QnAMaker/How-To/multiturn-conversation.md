---
title: Rozmowy wieloewymiężne - QnA Maker
description: Użyj monitów i kontekstu, aby zarządzać wieloma turami, znanymi jako multi-turn, dla bota z jednego pytania do drugiego. Multi-turn to możliwość rozmowy w tył i w tył, gdzie kontekst poprzedniego pytania wpływa na następne pytanie i odpowiedź.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261469"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Tworzenie wielu etapów konwersacji za pomocą dodatkowych monitów

Użyj monitów uzupełniających i kontekstu, aby zarządzać wieloma turami, znanymi jako _multi-turn,_ dla bota z jednego pytania do drugiego.

Aby zobaczyć, jak działa wiele zakrętów, zobacz następujący film demonstracyjny:

[![Rozmowa z wieloma zakrętami w aplikacji QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co to jest rozmowa wieloecowe?

Na niektóre pytania nie można odpowiedzieć w jednej turze. Podczas projektowania aplikacji klienckiej (chat bot) konwersacje, użytkownik może zadać pytanie, które musi być filtrowane lub udoskonalane w celu określenia poprawnej odpowiedzi. Umożliwiamy przepływ ten przez pytania, przedstawiając użytkownikowi *monity uzupełniające*.

Gdy użytkownik zadaje pytanie, QnA Maker zwraca odpowiedź _i_ wszelkie monity uzupełniające. Ta odpowiedź pozwala przedstawić pytania uzupełniające jako wybór.

> [!CAUTION]
> Monity wielokrotnego obrotu nie są wyodrębniane z dokumentów często zadawanych pytań. Jeśli potrzebujesz ekstrakcji wielokierunkowej, usuń znaki zapytania, które oznaczają pary QnA jako często zadawane pytania.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Przykładowa rozmowa z wieloma kolejkami z botem czatu

W przypadku wielu kolejek, czat bot zarządza konwersacji z użytkownikiem, aby określić ostateczną odpowiedź, jak pokazano na poniższej ilustracji:

![Okno dialogowe z wieloma kolejkami z monitami, które prowadzą użytkownika przez konwersację](../media/conversational-context/conversation-in-bot.png)

Na poprzednim obrazie użytkownik rozpoczął konwersację, wprowadzając **moje konto**. Baza wiedzy ma trzy połączone pary pytań i odpowiedzi. Aby uściślić odpowiedź, użytkownik wybiera jedną z trzech opcji w bazie wiedzy. Pytanie (#1), ma trzy monity uzupełniające, które są prezentowane w czacie bota jako trzy opcje (#2).

Gdy użytkownik wybierze opcję (#3), zostanie wyświetlona następna lista opcji rafinacji (#4). Ta sekwencja jest kontynuowana (#5), dopóki użytkownik nie określi poprawnej, ostatecznej odpowiedzi (#6).


### <a name="use-multi-turn-in-a-bot"></a>Użyj wielu kolejek w botze

Po opublikowaniu bazy wiedzy można wybrać przycisk **Utwórz bota,** aby wdrożyć bota QnA Maker w usłudze botów platformy Azure. Monity pojawią się na klientach czatu, które zostały włączone dla bota.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Tworzenie konwersacji wieloewracanych ze struktury dokumentu

Podczas tworzenia bazy wiedzy w sekcji **Wypełnianie bazy wiedzy** jest wyświetlane pole wyboru **Włącz wyodrębnianie z wielu zakręceń z adresów URL, pdf lub .docx plików.**

![Pole wyboru umożliwiające ekstrakcję wielokierunkową](../media/conversational-context/enable-multi-turn.png)

Po wybraniu tej opcji program QnA Maker wyodrębnia hierarchię obecną w strukturze dokumentu. Hierarchia jest konwertowana w celu wykonania monitów, a katalog główny hierarchii służy jako nadrzędna qnA. W niektórych dokumentach katalog główny hierarchii nie zawiera zawartości, która mogłaby służyć jako odpowiedź, można podać "Domyślny tekst odpowiedzi", który ma być używany jako tekst odpowiedzi zastępczej w celu wyodrębnienia takich hierarchii.

Strukturę wielu zakrętów można wywnioskować tylko z adresów URL, plików PDF lub plików DOCX. Na przykład struktury, zobacz obraz [pliku PDF podręcznika użytkownika microsoft surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

![! [Przykład struktury w instrukcji obsługi] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Tworzenie własnego dokumentu wielokierunkowego

Jeśli tworzysz dokument wieloecowe, pamiętaj o następujących wskazówkach:

* Nagłówki i nagłówki używają do oznaczania hierarchii. Na przykład można h1 oznaczyć nadrzędne QnA i h2, aby oznaczyć QnA, które powinny być traktowane jako monit. Użyj małego rozmiaru nagłówka, aby oznaczyć kolejną hierarchię. Nie używaj stylu, koloru ani innego mechanizmu, aby sugerować strukturę w dokumencie, program QnA Maker nie wyodrębni monitów z wieloma turami.

* Pierwszy znak nagłówka musi być kapitalizowany.

* Nie końkwuj nagłówka `?`znakiem zapytania, .

* Przykładowego [dokumentu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) można użyć jako przykładu do utworzenia własnego dokumentu wielowywracanego.

### <a name="adding-files-to-a-multi-turn-kb"></a>Dodawanie plików do wielonakręcanego KB

Po dodaniu dokumentu hierarchicznego program QnA Maker określa monity uzupełniające ze struktury w celu utworzenia przepływu konwersacyjnego.

1. W aplikacji QnA Maker wybierz istniejącą bazę wiedzy, która została utworzona za pomocą **opcji Włącz wyodrębnianie wielokrotne z plików URL, pdf lub .docx.** Włączone.
1. Przejdź do strony **Ustawienia,** wybierz plik lub adres URL do dodania.
1. **Zapisz i trenuj** bazę wiedzy.

> [!Caution]
> Obsługa używania eksportowanego pliku bazy wiedzy TSV lub XLS jako źródła danych dla nowej lub pustej bazy wiedzy nie jest obsługiwana. Aby dodać eksportowane monity wielokrotnego obrotu do bazy wiedzy, należy **zaimportować** ten typ pliku na stronie **Ustawienia** portalu QnA Maker.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tworzenie bazy wiedzy za pomocą wielu kolejek za pomocą interfejsu Create API

Za pomocą interfejsu API tworzenia interfejsu API programu [QnA Maker Create](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)można utworzyć przypadek wiedzy z wieloma wierszami. Monity są dodania `context` w `prompts` tablicy właściwości.

## <a name="show-questions-and-answers-with-context"></a>Pokaż pytania i odpowiedzi z kontekstem

Zmniejsz wyświetlane pary pytań i odpowiedzi tylko do par z konwersacjami kontekstowymi.

Wybierz pozycję **Opcje widoku**, a następnie wybierz pozycję **Pokaż kontekst**. Na liście są wyświetlane pary pytań i odpowiedzi zawierające monity uzupełniające.

![Filtrowanie par pytań i odpowiedzi według konwersacji kontekstowych](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontekst wielu zakrętów jest wyświetlany w pierwszej kolumnie.

![! [Kolumna "Kontekst (PODGLĄD)" ] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na poprzednim obrazie **#1** wskazuje pogrubiony tekst w kolumnie, co oznacza bieżące pytanie. Pytanie nadrzędne jest górnym elementem w wierszu. Wszelkie pytania poniżej są połączone pytania i odpowiedzi pary. Te elementy są wybieralne, dzięki czemu można natychmiast przejść do innych elementów kontekstu.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodawanie istniejącej pary pytań i odpowiedzi jako monitu uzupełniającego

Oryginalne pytanie, **Moje konto,** ma monity uzupełniające, takie jak **Konta i logowanie**się .

![Odpowiedzi na pytania dotyczące "Konta i logowanie się" oraz monity uzupełniające](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Dodaj monit uzupełniający do istniejącej pary pytań i odpowiedzi, która nie jest aktualnie połączona. Ponieważ pytanie nie jest połączone z żadną parą pytań i odpowiedzi, bieżące ustawienie widoku musi zostać zmienione.

1. Aby połączyć istniejącą parę pytań i odpowiedzi jako monit uzupełniający, wybierz wiersz dla pary pytań i odpowiedzi. W podręczniku Tabletu Surface wyszukaj pozycję **Wyloguj,** aby zmniejszyć listę.
1. W wierszu dla **opcji Znakowanie**w kolumnie **Odpowiedź** wybierz pozycję **Dodaj monit monitu uzupełniającego**.
1. W polach w oknie **podręcznym Monit uzupełniający** wprowadź następujące wartości:

    |Pole|Wartość|
    |--|--|
    |Tekst wyświetlany|Wprowadź **Wyłącz urządzenie**. Jest to tekst niestandardowy wyświetlany w wierszu monitu uzupełniającym.|
    |Tylko kontekst| Zaznacz to pole wyboru. Odpowiedź jest zwracana tylko wtedy, gdy pytanie określa kontekst.|
    |Link do odpowiedzi|Wprowadź **Użyj ekranu logowania,** aby znaleźć istniejącą parę pytań i odpowiedzi.|


1.  Jeden mecz jest zwracany. Wybierz tę odpowiedź jako odpowiedź uzupełniającą, a następnie wybierz pozycję **Zapisz**.

    ![Strona "Monit uzupełniający (PODGLĄD)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po dodaniu monitu uzupełniającego wybierz pozycję **Zapisz i trenuj** w górnej części nawigacji.

### <a name="edit-the-display-text"></a>Edytowanie wyświetlanego tekstu

Po utworzeniu monitu uzupełniającego i wprowadzeniu istniejącej pary pytań i odpowiedzi jako **łącze do odpowiedzi**można wprowadzić nowy **tekst wyświetlany**. Ten tekst nie zastępuje istniejącego pytania i nie dodaje nowego pytania alternatywnego. Jest oddzielona od tych wartości.

1. Aby edytować wyświetlany tekst, wyszukaj i zaznacz pytanie w polu **Kontekst.**
1. W wierszu dla tego pytania wybierz monit uzupełniający w kolumnie odpowiedzi.
1. Zaznacz wyświetlany tekst, który chcesz edytować, a następnie wybierz pozycję **Edytuj**.

    ![Polecenie Edytuj wyświetlany tekst](../media/conversational-context/edit-existing-display-text.png)

1. W oknie **podręcznym Monit uzupełniający** zmień istniejący wyświetlany tekst.
1. Po zakończeniu edytowania wyświetlanego tekstu wybierz pozycję **Zapisz**.
1. Na górnym pasku nawigacyjnym **zapisz i trenuj**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodawanie nowej pary pytań i odpowiedzi jako monitu uzupełniającego

Po dodaniu nowej pary pytań i odpowiedzi do bazy wiedzy każda para powinna być połączona z istniejącym pytaniem jako monit monitem uzupełniającym.

1. Na pasku narzędzi bazy wiedzy wyszukaj i wybierz istniejącą parę pytań i odpowiedzi dla **kont i zalogowanie**się .

1. W kolumnie **Odpowiedź** dla tego pytania wybierz pozycję **Dodaj monit monitu o monit uzupełniający**.
1. W **obszarze Monit uzupełniający (PREVIEW)** utwórz nowy monit uzupełniający, wprowadzając następujące wartości:

    |Pole|Wartość|
    |--|--|
    |Tekst wyświetlany|*Utwórz konto systemu Windows*. Tekst niestandardowy do wyświetlenia w wierszu monitu uzupełniającego.|
    |Tylko kontekst|Zaznacz to pole wyboru. Ta odpowiedź jest zwracana tylko wtedy, gdy pytanie określa kontekst.|
    |Link do odpowiedzi|Jako odpowiedź wpisz następujący tekst:<br>* [Utwórz](https://account.microsoft.com/) konto systemu Windows przy nowym lub istniejącym koncie e-mail*.<br>Podczas zapisywania i szkolenia bazy danych, ten tekst zostanie przekonwertowany. |
    |||

    ![Tworzenie nowego pytania i odpowiedzi monitu](../media/conversational-context/create-child-prompt-from-parent.png)


1. Wybierz **pozycję Utwórz nowy**, a następnie wybierz pozycję **Zapisz**.

    Ta akcja tworzy nową parę pytań i odpowiedzi i łączy wybrane pytanie jako monit uzupełniający. **Kolumna Kontekst** dla obu pytań wskazuje relację monitu uzupełniającego.

1. Wybierz **pozycję Opcje widoku**, a następnie wybierz pozycję Pokaż kontekst [**(PODGLĄD)**](#show-questions-and-answers-with-context).

    Nowe pytanie pokazuje, jak jest to powiązane.

    ![Tworzenie nowego monitu uzupełniającego](../media/conversational-context/new-qna-follow-up-prompt.png)

    Pytanie nadrzędne wyświetla nowe pytanie jako jeden z jego wyborów.

    ![! [Kolumna Kontekst dla obu pytań wskazuje relację monitu uzupełniającego] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po dodaniu monitu uzupełniającego wybierz pozycję **Zapisz i trenuj** na górnym pasku nawigacyjnym.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Wyświetlanie wielu zakrętów podczas testowania monitów uzupełniających

Podczas testowania pytania z monitów uzupełniających w okienku **testowym,** odpowiedź zawiera monity uzupełniające.

![Odpowiedź obejmuje monity o kontynuację](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Żądanie JSON o zwrócenie wstępnej odpowiedzi i monitów uzupełniających

Użyj pustego `context` obiektu, aby poprosić o odpowiedź na pytanie użytkownika i dołączyć monity uzupełniające.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Odpowiedź JSON w celu zwrócenia wstępnej odpowiedzi i monitów uzupełniających

W poprzedniej sekcji zażądano odpowiedzi i wszelkich monitów uzupełniających do **konta i zalogowania**się . Odpowiedź zawiera informacje monitu, który znajduje się w *answers[0]context*i tekst do wyświetlenia użytkownikowi.

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

Tablica `prompts` zawiera tekst `displayText` we `qnaId` właściwości i wartość. Odpowiedzi te można wyświetlić jako następne wyświetlane opcje w przepływie `qnaId` konwersacji, a następnie wysłać wybrane z powrotem do programu QnA Maker w następującym żądaniu.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Żądanie JSON o zwrócenie niefównowej odpowiedzi i monitów uzupełniających

Wypełnij `context` obiekt, aby uwzględnić poprzedni kontekst.

W poniższym żądaniu JSON bieżące pytanie to *Użyj funkcji Windows Hello do zalogowania się,* a poprzednie pytanie dotyczyło kont i *logowania*się .

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpowiedź JSON w celu zwrócenia niefekcyjnej odpowiedzi i monitów uzupełniających

QnA Maker _GenerateAnswer_ JSON odpowiedzi zawiera monity `context` uzupełniające we właściwości `answers` pierwszego elementu w obiekcie:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Zapytanie o bazę wiedzy za pomocą identyfikatora QnA Maker

Jeśli budujesz niestandardową aplikację przy użyciu funkcji multi-turn. W odpowiedzi na pytanie początkowe zwracane `qnaId` są wszelkie monity uzupełniające i związane z nimi. Teraz, gdy masz identyfikator, można przekazać to w treści monitu uzupełniającego. Jeśli treść żądania `qnaId`zawiera obiekt , i kontekstu (który zawiera poprzednie właściwości QnA Maker), a następnie GenerateAnswer zwróci dokładne pytanie według identyfikatora, zamiast używać algorytmu klasyfikacji, aby znaleźć odpowiedź przez tekst pytania.


## <a name="display-order-is-supported-in-the-update-api"></a>Kolejność wyświetlania jest obsługiwana w interfejsie API aktualizacji

Wyświetlany [tekst i kolejność wyświetlania](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), zwrócone w odpowiedzi JSON, jest obsługiwany do edycji przez interfejs API [aktualizacji](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Dodawanie lub usuwanie wielowierszowych monitów za pomocą interfejsu API aktualizacji

Za pomocą interfejsu API aktualizacji programu [QnA Maker można](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)dodawać lub usuwać monity wielokrotnego obracania .  Monity są dodania `context` w `promptsToAdd` tablicy `promptsToDelete` właściwości i tablicy.

## <a name="export-knowledge-base-for-version-control"></a>Eksportowanie bazy wiedzy do kontroli wersji

Program QnA Maker obsługuje kontrolę wersji, dołączając do eksportowanego pliku wiele czynności konwersacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konwersacjach kontekstowych z tego [przykładu okna dialogowego](https://aka.ms/qnamakermultiturnsample) lub dowiedz się więcej o [koncepcyjnym projekcie bota dla konwersacji wielotwarze.](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
