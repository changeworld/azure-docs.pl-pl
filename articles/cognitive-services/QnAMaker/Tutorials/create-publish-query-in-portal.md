---
title: Tworzenie i publikowanie bazy wiedzy oraz odpowiadanie w usłudze QnA Maker
titleSuffix: Azure Cognitive Services
description: Ten oparty na portalu samouczek przedstawia sposób programowego tworzenia i publikowania bazy wiedzy, a następnie odpowiadania na pytanie z bazy wiedzy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 6f79614e4b1ec660d2ec5c8aee40924908cf8f5c
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884129"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Samouczek: tworzenie bazy wiedzy, a następnie odpowiadanie na pytanie za pomocą portalu usługi QnA Maker

Ten samouczek przedstawia sposób tworzenia i publikowania bazy wiedzy, a następnie odpowiadania na pytanie z bazy wiedzy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie bazy wiedzy w portalu usługi QnA Maker
> * Przegląd, zapisywanie i uczenie bazy wiedzy
> * Publikowanie bazy wiedzy
> * Używanie programu Curl do wykonywania zapytań w bazie wiedzy
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
    |Identyfikator katalogu platformy Microsoft Azure|Twój _identyfikator katalogu platformy Microsoft Azure_ jest skojarzony z kontem, którego używasz do logowania się do witryny Azure Portal i portalu usługi QnA Maker. |
    |Identyfikator subskrypcji platformy Azure|Twoje konto billingowe, w ramach którego utworzono zasób usługi QnA Maker.|
    |Usługa Azure QnA|Istniejący zasób usługi QnA Maker.|

    ![Krok 2 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. W następnym kroku wprowadź nazwę bazy wiedzy, `My Tutorial kb`.

    ![Krok 3 procesu tworzenia bazy wiedzy](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. W następnym kroku wypełnij swoją bazę wiedzy przy użyciu następujących ustawień:  

    |Nazwa ustawienia|Wartość ustawienia|Przeznaczenie|
    |--|--|--|
    |Adres URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Zawartość często zadawanych pytań pod tym adresem URL jest formatowana za pomocą pytania, po którym następuje odpowiedź. Ten format może być interpretowany przez usługę QnA Maker w celu wyodrębnienia pytań i skojarzonych odpowiedzi.|
    |Plik |_nie są używane w ramach tego samouczka_|Służy do przekazywania plików dla pytań i odpowiedzi. |
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

## <a name="use-curl-to-query-for-an-faq-answer"></a>Używanie narzędzia Curl do wykonywania zapytania o odpowiedź na często zadawane pytanie

1. Wybierz kartę **Curl**. 

    ![Polecenie narzędzia Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Skopiuj tekst z karty **Curl** i wykonaj go w terminalu lub wierszu polecenia z włączoną obsługą narzędzia Curl. Wartość nagłówka autoryzacji zawiera tekst `Endpoint` ze spacją końcową, po której następuje klucz.

1. Zastąp element `<Your question>` pytaniem `How large can my KB be?`. Jest ono podobne do pytania `How large a knowledge base can I create?`, ale nie identyczne. Usługa QnA Maker stosuje przetwarzanie języka naturalnego w celu określenia, czy dwa pytania są takie same.     

1. Wykonaj polecenie narzędzia CURL, aby otrzymać odpowiedź w formacie JSON, w tym ocenę i odpowiedź. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Używanie narzędzia Curl do wykonywania zapytania o odpowiedź na konwersację

1. W terminalu z włączoną obsługą narzędzia Curl zastąp pytanie `How large can my KB be?` wyrażeniem, którym użytkownik kończy konwersację z botem, takim jak `Thank you`.   

1. Wykonaj polecenie narzędzia CURL, aby otrzymać odpowiedź w formacie JSON, w tym ocenę i odpowiedź. 

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

## <a name="use-curl-to-query-for-the-default-answer"></a>Używanie narzędzia Curl do wykonywania zapytania o odpowiedź domyślną

Każde pytanie, w przypadku którego usługa QnA Maker nie jest pewna odpowiedzi, otrzymuje odpowiedź domyślną. Ta odpowiedź jest skonfigurowana w witrynie Azure portal. 

1. W terminalu z włączoną obsługą narzędzia Curl zastąp tekst `Thank you` znakiem `x`. 

1. Wykonaj polecenie narzędzia CURL, aby otrzymać odpowiedź w formacie JSON, w tym ocenę i odpowiedź. 

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
    
    Narzędzie QnA Maker zwróciło ocenę 0, co oznacza, że nie ma żadnej pewności, ale zwróciło także odpowiedź domyślną. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Obsługiwane źródła danych](../Concepts/data-sources-supported.md), aby uzyskać więcej informacji na temat obsługiwanych formatów plików. 

Dowiedz się więcej o rozmowy [osobowościach](../Concepts/best-practices.md#chit-chat) dla konwersacji.

Aby uzyskać więcej informacji na temat odpowiedzi domyślnej, zobacz [Nie znaleziono dopasowań](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Pojęcia dotyczące wiedzy](../Concepts/knowledge-base.md)