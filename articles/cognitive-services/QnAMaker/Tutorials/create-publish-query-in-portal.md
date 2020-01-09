---
title: 'Samouczek: Tworzenie, publikowanie i odbieranie odpowiedzi w QnA Maker'
titleSuffix: Azure Cognitive Services
description: W tym samouczku pokazano, jak utworzyć nową bazę wiedzy z pytaniami i odpowiedziami z publicznych często zadawanych pytań. Zapisz, Wyszkol i Opublikuj bazę wiedzy. Po opublikowaniu bazy wiedzy Wyślij pytanie i odbierz odpowiedź z poleceniem zwinięcie. Następnie utwórz bot i przetestuj bot z tym samym pytaniem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5b2f2decf3d070da2fb58bebd424b4209f353316
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447384"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Samouczek: w portalu QnA Maker Utwórz bazę wiedzy

Utwórz nową bazę wiedzy z pytaniami i odpowiedziami z publicznych często zadawanych pytań. Zapisz, Wyszkol i Opublikuj bazę wiedzy. Po opublikowaniu bazy wiedzy Wyślij pytanie i odbierz odpowiedź z poleceniem zwinięcie. Następnie utwórz bot i przetestuj bot z tym samym pytaniem. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Utwórz bazę wiedzy w portalu QnA Maker.
> * Przeglądanie, zapisywanie i uczenie bazy wiedzy.
> * Publikowanie bazy wiedzy.
> * Użyj Zwinięciea, aby wykonać zapytanie dotyczące bazy wiedzy.
> * Utwórz element bot.
 

> [!NOTE]
> Wersja programowa tego samouczka jest dostępna z kompletnym rozwiązaniem w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga istniejącej [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Utwórz bazę wiedzy 

1. Zaloguj się do portalu usługi [QnA Maker](https://www.qnamaker.ai). 

1. Wybierz pozycję **Tworzenie bazy wiedzy** z górnego menu.

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Pomiń pierwszy krok, ponieważ będziesz używać istniejącej usługi QnA Maker. 

1. Wybierz istniejące ustawienia:  

    |Ustawienie|Przeznaczenie|
    |--|--|
    |Identyfikator katalogu Microsoft Azure|Ten identyfikator jest skojarzony z kontem używanym do logowania się do Azure Portal i portalu QnA Maker. |
    |Identyfikator subskrypcji platformy Azure|Konto rozliczeniowe, w którym utworzono zasób QnA Maker.|
    |Usługa Azure QnA|Istniejący zasób usługi QnA Maker.|

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Wprowadź nazwę bazy wiedzy, `My Tutorial kb`.

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Wypełnij bazę wiedzy następującymi ustawieniami:  

    |Nazwa ustawienia|Wartość ustawienia|Przeznaczenie|
    |--|--|--|
    |Adres URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Zawartość często zadawanych pytań pod tym adresem URL jest formatowana za pomocą pytania, po którym następuje odpowiedź. Ten format może być interpretowany przez usługę QnA Maker w celu wyodrębnienia pytań i skojarzonych odpowiedzi.|
    |Plik |_nieużywane w tym samouczku_|Służy do przekazywania plików dla pytań i odpowiedzi. |
    |Osobowość dla konwersacji|Wyświetlana|Zapewnia to łatwą i swobodną [osobowość](../Concepts/best-practices.md#chit-chat) typowych pytań i odpowiedzi. Później można edytować te pytania i odpowiedzi. |

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Wybierz pozycję **Tworzenie bazy wiedzy**, aby zakończyć proces tworzenia.

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Przegląd, zapisywanie i uczenie bazy wiedzy

1. Przejrzyj pytania i odpowiedzi. Pierwsza strona zawiera pytania i odpowiedzi z adresu URL. 

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Wybierz ostatnią stronę pytań i odpowiedzi u dołu tabeli. Na tej stronie wyświetlane są pytania i odpowiedzi od osobowości dla konwersacji. 

1. Na pasku narzędzi powyżej listy pytań i odpowiedzi wybierz ikonę **Wyświetl opcje** , a następnie wybierz pozycję **Pokaż metadane**. Spowoduje to wyświetlenie tagów metadanych dla każdego pytania i odpowiedzi. Pytania dla konwersacji mają już ustawione metadane **redakcyjny: konwersacja**. Te metadane są zwracane do aplikacji klienckiej wraz z wybraną odpowiedzią. Aplikacja kliencka, na przykład czatbot, może używać tych filtrowanych metadanych w celu określenia dodatkowego przetwarzania lub interakcji z użytkownikiem.

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Wybierz pozycję **Zapisywanie i szkolenie** na górnym pasku menu.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publikuj, aby uzyskać punkty końcowe bazy wiedzy

Wybierz przycisk **Publikuj** z górnego menu. Na stronie publikowania wybierz pozycję **Publikuj**.

![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po opublikowaniu bazy wiedzy zostanie wyświetlony punkt końcowy.

![Zrzut ekranu ustawień punktu końcowego](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nie zamykaj tej strony **publikowania** . Będzie ona potrzebna w dalszej części tego samouczka, aby utworzyć bot. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Użyj Zwinięciea, aby wykonać zapytanie dotyczące odpowiedzi na często zadawane pytania

1. Wybierz kartę **Curl**. 

    ![Zrzut ekranu przedstawiający kartę zwinięcie](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Skopiuj tekst z karty **zwinięcie** i uruchom ją w terminalu z włączonym zwinięciem lub w wierszu polecenia. Wartość nagłówka autoryzacji obejmuje tekst `Endpoint`, z końcowym spacją i kluczem.

1. Zastąp element `<Your question>` pytaniem `How large can my KB be?`. Jest ono podobne do pytania `How large a knowledge base can I create?`, ale nie identyczne. Usługa QnA Maker stosuje przetwarzanie języka naturalnego w celu określenia, czy dwa pytania są takie same.     

1. Uruchom polecenie zwinięcie i odbierz odpowiedź JSON, łącznie z oceną i odpowiedzią. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    Usługa QnA Maker jest dość pewna odpowiedzi (ocena 42,81%).  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Użyj Zwinięciea, aby wykonać zapytanie o odpowiedź Chit-Chat

1. W terminalu z włączoną funkcją zastępowania Zastąp `How large can my KB be?` instrukcją bot z konwersacją użytkownika, taką jak `Thank you`.   

1. Uruchom polecenie zwinięcie i odbierz odpowiedź JSON, łącznie z oceną i odpowiedzią. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). QnA Maker zwrócić także wszystkie powiązane pytania, a także Właściwość metadanych zawierająca informacje znacznika metadanych Chit-Chat.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Użyj Zwinięciea, aby wykonać zapytanie o domyślną odpowiedź

Każde pytanie, które QnA Maker nie ma pewności, że otrzymuje odpowiedź domyślną. Ta odpowiedź jest skonfigurowana w witrynie Azure portal. 

1. W terminalu z włączoną funkcją zwinięcia Zastąp `Thank you` z `x`. 

1. Uruchom polecenie zwinięcie i odbierz odpowiedź JSON, łącznie z oceną i odpowiedzią. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker zwróciła wynik `0`, co oznacza brak pewności. Zwraca również odpowiedź domyślną. 

## <a name="create-a-knowledge-base-bot"></a>Tworzenie bazy wiedzy bot

Aby uzyskać więcej informacji, zobacz [tworzenie rozmowy bot z tą bazą wiedzy](create-qna-bot.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z bot bazy wiedzy Usuń grupę zasobów `my-tutorial-rg`, aby usunąć wszystkie zasoby platformy Azure utworzone w procesie bot.

Po zakończeniu pracy z bazą wiedzy w portalu QnA Maker wybierz pozycję **Moje bazy wiedzy**. Następnie wybierz bazę wiedzy, **mój samouczek KB**i wybierz ikonę Usuń znajdującą się po prawej stronie w tym wierszu.  

## <a name="next-steps"></a>Następne kroki

Zobacz [Obsługiwane źródła danych](../Concepts/data-sources-supported.md), aby uzyskać więcej informacji na temat obsługiwanych formatów plików. 

Dowiedz się więcej o rozmowy [osobowościach](../Concepts/best-practices.md#chit-chat) dla konwersacji.

Aby uzyskać więcej informacji na temat odpowiedzi domyślnej, zobacz [Nie znaleziono dopasowań](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Utwórz czat bot z tą bazą wiedzy](create-qna-bot.md)