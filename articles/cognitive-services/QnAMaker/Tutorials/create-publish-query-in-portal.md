---
title: Tworzenie i publikowanie bazy wiedzy oraz odpowiadanie w usłudze QnA Maker
titleSuffix: Azure Cognitive Services
description: Tworzenie nowej bazy wiedzy za pomocą pytań i odpowiedzi z publicznych opartej na sieci web — często zadawane pytania. Zapisz, uczenie i publikowanie bazy wiedzy knowledge base. Po opublikowaniu bazy wiedzy knowledge base pytanie Wyślij i odbierz odpowiedź przy użyciu polecenia CURL. Utwórz bota i przetestować botów za pomocą tego samego zapytania.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: diberry
ms.openlocfilehash: 299dd61055503f0b5a11cbe97e137e4760edadda
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266957"
---
# <a name="tutorial-from-qna-maker-portal-create-a-knowledge-base"></a>Samouczek: Tworzenie bazy wiedzy w portalu narzędzia QnA Maker

Tworzenie nowej bazy wiedzy za pomocą pytań i odpowiedzi z publicznych opartej na sieci web — często zadawane pytania. Zapisz, uczenie i publikowanie bazy wiedzy knowledge base. Po opublikowaniu bazy wiedzy knowledge base pytanie Wyślij i odbierz odpowiedź przy użyciu polecenia Curl. Utwórz bota i przetestować botów za pomocą tego samego zapytania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie bazy wiedzy w portalu usługi QnA Maker
> * Przegląd, zapisywanie i uczenie bazy wiedzy
> * Publikowanie bazy wiedzy
> * Używanie programu Curl do wykonywania zapytań w bazie wiedzy
> * Tworzenie botów
> 
> [!NOTE]
> Wersja programowa tego samouczka jest dostępna z kompletnym rozwiązaniem w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga istniejącej [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy 

1. Zaloguj się do portalu usługi [QnA Maker](https://www.qnamaker.ai). 

1. Wybierz pozycję **Tworzenie bazy wiedzy** z górnego menu.

    ![Krok 1 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Pomiń pierwszy krok, ponieważ będziesz używać istniejącej usługi QnA Maker. 

1. W następnym kroku wybierz istniejące ustawienia:  

    |Ustawienie|Przeznaczenie|
    |--|--|
    |Identyfikator katalogu platformy Microsoft Azure|Twoje _identyfikator usługi Microsoft Azure Directory_ jest skojarzony z kontem używanym do logowania się do witryny Azure portal i portalem usługi QnA Maker. |
    |Identyfikator subskrypcji platformy Azure|Twoje konto billingowe, w ramach którego utworzono zasób usługi QnA Maker.|
    |Usługa Azure QnA|Istniejący zasób usługi QnA Maker.|

    ![Krok 2 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. W następnym kroku wprowadź nazwę bazy wiedzy, `My Tutorial kb`.

    ![Krok 3 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. W następnym kroku wypełnij swoją bazę wiedzy przy użyciu następujących ustawień:  

    |Nazwa ustawienia|Wartość ustawienia|Przeznaczenie|
    |--|--|--|
    |Adres URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Zawartość często zadawanych pytań pod tym adresem URL jest formatowana za pomocą pytania, po którym następuje odpowiedź. Ten format może być interpretowany przez usługę QnA Maker w celu wyodrębnienia pytań i skojarzonych odpowiedzi.|
    |Plik |_nieużywane w tym samouczku_|Służy do przekazywania plików dla pytań i odpowiedzi. |
    |Osobowość dla konwersacji|Przyjaciel|Nadaje przyjazny i swobodny ton typowym pytaniom i odpowiedziom. Później można edytować te pytania i odpowiedzi. |

    ![Krok 4 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Wybierz pozycję **Tworzenie bazy wiedzy**, aby zakończyć proces tworzenia.

    ![Krok 5 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Przegląd bazy wiedzy, zapisywanie i uczenie

1. Przejrzyj pytania i odpowiedzi. Pierwsza strona zawiera pytania i odpowiedzi z adresu URL. 

    ![Zapisywanie i szkolenie](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Wybierz ostatnią stronę pytań i odpowiedzi u dołu tabeli. Na tej stronie wyświetlane są pytania i odpowiedzi od osobowości dla konwersacji. 

1. Na pasku narzędzi powyżej listy pytań i odpowiedzi wybierz ikonę metadanych. Spowoduje to wyświetlenie tagów metadanych dla każdego pytania i odpowiedzi. Pytania dla konwersacji mają już ustawione metadane **redakcyjny: konwersacja**. Te metadane są zwracane do aplikacji klienckiej wraz z wybraną odpowiedzią. Aplikacja kliencka, na przykład czatbot, może używać tych filtrowanych metadanych w celu określenia dodatkowego przetwarzania lub interakcji z użytkownikiem.

    ![Wyświetlanie tagów metadanych](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Wybierz pozycję **Zapisywanie i szkolenie** na górnym pasku menu.

## <a name="publish-to-get-kb-endpoints"></a>Publikowanie w celu pobrania punktów końcowych bazy wiedzy

Wybierz przycisk **Publikuj** z górnego menu. Na stronie publikowania wybierz przycisk **Publikuj** obok przycisku **Anuluj**.

![Publikowanie](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po opublikowaniu bazy wiedzy wyświetlany jest punkt końcowy

![Ustawienia punktu końcowego strony publikowania](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nie zamykaj tego **Publikuj** stronie użyjesz go do utworzenia robotów w dalszej części tego samouczka. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Za pomocą programu Curl zapytania dla odpowiedzi na często zadawane pytania

1. Wybierz kartę **Curl**. 

    ![Polecenie narzędzia Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Skopiuj tekst z karty **Curl** i wykonaj go w terminalu lub wierszu polecenia z włączoną obsługą narzędzia Curl. Wartość nagłówka autoryzacji zawiera tekst `Endpoint` ze spacją końcową, po której następuje klucz.

1. Zastąp element `<Your question>` pytaniem `How large can my KB be?`. Jest ono podobne do pytania `How large a knowledge base can I create?`, ale nie identyczne. Usługa QnA Maker stosuje przetwarzanie języka naturalnego w celu określenia, czy dwa pytania są takie same.     

1. Wykonanie polecenia Curl, a otrzymasz odpowiedź JSON, takich jak ocena i odpowiedzi. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Za pomocą programu Curl zapytania odpowiedzi czatu Chit

1. W terminalu włączone Curl Zastąp `How large can my KB be?` z instrukcją bot zakończenia konwersacji przez użytkownika, takie jak `Thank you`.   

1. Wykonanie polecenia Curl, a otrzymasz odpowiedź JSON, takich jak ocena i odpowiedzi. 

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

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). Usługa QnA Maker zwróciła także wszystkie powiązane pytania oraz właściwość metadanych zawierającą informacje o tagu metadanych konwersacji.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Za pomocą programu Curl zapytania odpowiedź domyślną

Każde pytanie, w przypadku którego usługa QnA Maker nie jest pewna odpowiedzi, otrzymuje odpowiedź domyślną. Ta odpowiedź jest skonfigurowana w witrynie Azure portal. 

1. W terminalu z włączoną obsługą narzędzia Curl zastąp tekst `Thank you` znakiem `x`. 

1. Wykonanie polecenia Curl, a otrzymasz odpowiedź JSON, takich jak ocena i odpowiedzi. 

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
    
    Usługa QnA Maker zwrócony wynik `0`, co oznacza nie zaufania, ale także zwracany odpowiedzi domyślnej. 

## <a name="create-a-knowledge-base-bot"></a>Utwórz bota bazy wiedzy

Aby uzyskać więcej informacji, zobacz [tworzenie czatbot za pomocą tej wiedzy](create-qna-bot.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z botami w bazie wiedzy knowledge base, Usuń grupę zasobów, `my-tutorial-rg`, aby usunąć wszystkie zasoby platformy Azure utworzone w procesie botów.

Po wykonaniu tych czynności w bazie wiedzy, w portalu narzędzia QnA Maker wybierz **moich baz wiedzy**, następnie wybierz pozycję bazy wiedzy knowledge base, **kb Moje samouczek**, następnie wybierz ikonę Usuń po prawej stronie w tym wierszu.  

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [źródła danych obsługiwane](../Concepts/data-sources-supported.md) Aby uzyskać więcej informacji na temat formatów plików pomocy technicznej. 

Dowiedz się więcej o rozmowy [osobowościach](../Concepts/best-practices.md#chit-chat) dla konwersacji.

Aby uzyskać więcej informacji na temat odpowiedzi domyślnej, zobacz [Nie znaleziono dopasowań](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Tworzenie czatbot za pomocą tej wiedzy](create-qna-bot.md)