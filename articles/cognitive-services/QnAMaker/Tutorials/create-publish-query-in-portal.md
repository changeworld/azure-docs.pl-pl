---
title: Tworzenie, publikowanie i odpowiedzi w usługi QnA Maker
titleSuffix: Azure Cognitive Services
description: Tworzenie nowej bazy wiedzy za pomocą pytań i odpowiedzi z publicznych opartej na sieci web — często zadawane pytania. Zapisz, uczenie i publikowanie bazy wiedzy knowledge base. Po opublikowaniu bazy wiedzy knowledge base pytanie Wyślij i odbierz odpowiedź przy użyciu polecenia cURL. Utwórz bota i przetestować botów za pomocą tego samego zapytania.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692999"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Samouczek: Tworzenie bazy wiedzy w portalu narzędzia QnA Maker

Tworzenie nowej bazy wiedzy za pomocą pytań i odpowiedzi z publicznych opartej na sieci web — często zadawane pytania. Zapisz, uczenie i publikowanie bazy wiedzy knowledge base. Po opublikowaniu bazy wiedzy knowledge base pytanie Wyślij i odbierz odpowiedź przy użyciu polecenia cURL. Utwórz bota i przetestować botów za pomocą tego samego zapytania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie bazy wiedzy w portalu narzędzia QnA Maker.
> * Przejrzyj, zapisywania i uczenie bazy wiedzy knowledge base.
> * Publikowanie bazy wiedzy knowledge base.
> * Korzystanie z programu cURL, aby wysłać zapytanie w bazie wiedzy knowledge base.
> * Utwórz bota.
 

> [!NOTE]
> Wersja programowa tego samouczka jest dostępna z kompletnym rozwiązaniem w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga istniejącej [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy 

1. Zaloguj się do portalu usługi [QnA Maker](https://www.qnamaker.ai). 

1. Wybierz pozycję **Tworzenie bazy wiedzy** z górnego menu.

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Pomiń pierwszy krok, ponieważ będzie używać istniejącej usługi QnA Maker. 

1. Wybierz z istniejących ustawień:  

    |Ustawienie|Przeznaczenie|
    |--|--|
    |Identyfikator katalogu platformy Microsoft Azure|Ten identyfikator jest skojarzony z kontem, którego używasz do logowania się do witryny Azure portal i portalem usługi QnA Maker. |
    |Identyfikator subskrypcji platformy Azure|Konto rozliczeniowe, w którym został utworzony zasób usługi QnA Maker.|
    |Usługa Azure QnA|Istniejący zasób usługi QnA Maker.|

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Wprowadź nazwę bazy wiedzy knowledge base, `My Tutorial kb`.

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Wypełniania bazy wiedzy z następującymi ustawieniami:  

    |Nazwa ustawienia|Wartość ustawienia|Przeznaczenie|
    |--|--|--|
    |Adres URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Zawartość często zadawanych pytań pod tym adresem URL jest formatowana za pomocą pytania, po którym następuje odpowiedź. Ten format może być interpretowany przez usługę QnA Maker w celu wyodrębnienia pytań i skojarzonych odpowiedzi.|
    |Plik |_nieużywane w tym samouczku_|Służy do przekazywania plików dla pytań i odpowiedzi. |
    |Osobowość dla konwersacji|Przyjazne|Dzięki temu, przyjazne i dorywczy [osobowość](../Concepts/best-practices.md#chit-chat) do typowych pytań i odpowiedzi. Później można edytować te pytania i odpowiedzi. |

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Wybierz pozycję **Tworzenie bazy wiedzy**, aby zakończyć proces tworzenia.

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Przegląd, zapisywanie i uczenie bazy wiedzy

1. Przejrzyj pytania i odpowiedzi. Pierwsza strona zawiera pytania i odpowiedzi z adresu URL. 

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Wybierz ostatnią stronę pytań i odpowiedzi u dołu tabeli. Na tej stronie wyświetlane są pytania i odpowiedzi od osobowości dla konwersacji. 

1. Na pasku narzędzi powyżej listy pytań i odpowiedzi, wybierz **wyświetlić opcje** ikonę, a następnie wybierz **Pokaż metadanych**. Spowoduje to wyświetlenie tagów metadanych dla każdego pytania i odpowiedzi. Pytania dla konwersacji mają już ustawione metadane **redakcyjny: konwersacja**. Te metadane są zwracane do aplikacji klienckiej, wraz z wybranej odpowiedzi. Aplikacja kliencka, na przykład czatbot, może używać tych filtrowanych metadanych w celu określenia dodatkowego przetwarzania lub interakcji z użytkownikiem.

    ![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Wybierz pozycję **Zapisywanie i szkolenie** na górnym pasku menu.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publikować można pobrać punktów końcowych w bazie wiedzy knowledge base

Wybierz przycisk **Publikuj** z górnego menu. Na stronie publikowania wybierz pozycję **Publikuj**.

![Zrzut ekranu usługi QnA Maker w witrynie portal](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po opublikowaniu bazy wiedzy knowledge base, punkt końcowy jest wyświetlany.

![Zrzut ekranu przedstawiający ustawienia punktu końcowego](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nie zamykaj tego **Publikuj** strony. Będą potrzebne w dalszej części samouczka, aby utworzyć robota. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Korzystanie z programu cURL, aby wykonać zapytanie dla odpowiedzi na często zadawane pytania

1. Wybierz kartę **Curl**. 

    ![Zrzut ekranu Curl kartę](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Skopiuj tekst z **Curl** , a następnie uruchom go z włączonym programu cURL, terminal lub wiersza polecenia. Wartość nagłówka autoryzacji zawiera tekst `Endpoint`, za pomocą spacji, a następnie klawisz.

1. Zastąp element `<Your question>` pytaniem `How large can my KB be?`. Jest ono podobne do pytania `How large a knowledge base can I create?`, ale nie identyczne. Usługa QnA Maker stosuje przetwarzanie języka naturalnego w celu określenia, czy dwa pytania są takie same.     

1. Uruchom polecenie programu cURL i odbierać odpowiedź JSON, takich jak ocena i odpowiedzi. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Korzystanie z programu cURL do wykonywania zapytań w odpowiedzi czatu Chit

1. W terminalu włączone cURL Zastąp `How large can my KB be?` z instrukcją bot zakończenia konwersacji przez użytkownika, takie jak `Thank you`.   

1. Uruchom polecenie programu cURL i odbierać odpowiedź JSON, takich jak ocena i odpowiedzi. 

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

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). Usługa QnA Maker jest także zwracany powiązane pytania, jak również właściwość metadanych zawierającego informacje o tagu metadanych Chit rozmowy.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Korzystanie z programu cURL do zapytania odpowiedź domyślną

Każde pytanie, usługa QnA Maker nie będący wątpliwości, czy otrzyma odpowiedź domyślną. Ta odpowiedź jest skonfigurowana w witrynie Azure portal. 

1. W terminalu włączone cURL Zastąp `Thank you` z `x`. 

1. Uruchom polecenie programu cURL i odbierać odpowiedź JSON, takich jak ocena i odpowiedzi. 

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
    
    Usługa QnA Maker zwrócony wynik `0`, co oznacza, że nie obaw. Zwrócony odpowiedzi domyślnej. 

## <a name="create-a-knowledge-base-bot"></a>Utwórz bota bazy wiedzy

Aby uzyskać więcej informacji, zobacz [tworzenie czatbot za pomocą tej wiedzy](create-qna-bot.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z botami w bazie wiedzy knowledge base, Usuń grupę zasobów, `my-tutorial-rg`, aby usunąć wszystkie zasoby platformy Azure utworzone w procesie botów.

Po wykonaniu tych czynności w bazie wiedzy, w portalu narzędzia QnA Maker wybierz **moich baz wiedzy**. Następnie wybierz pozycję bazy wiedzy knowledge base, **kb Moje samouczek**i wybierz ikonę Usuń po prawej stronie w tym wierszu.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Obsługiwane źródła danych](../Concepts/data-sources-supported.md), aby uzyskać więcej informacji na temat obsługiwanych formatów plików. 

Dowiedz się więcej o rozmowy [osobowościach](../Concepts/best-practices.md#chit-chat) dla konwersacji.

Aby uzyskać więcej informacji na temat odpowiedzi domyślnej, zobacz [Nie znaleziono dopasowań](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Tworzenie czatbot za pomocą tej wiedzy](create-qna-bot.md)