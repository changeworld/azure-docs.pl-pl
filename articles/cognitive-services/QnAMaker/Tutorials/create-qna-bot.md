---
title: Utwórz bot — strona główna z usługi Azure Service Bot - kognitywnych usług Azure | Dokumentacja firmy Microsoft
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349897"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Tworzenie z usługą Azure Bot Bot — strona główna
W tym samouczku przedstawiono tworzenie robotów — strona główna z usługą Azure Bot w portalu Azure.

## <a name="prerequisite"></a>Wymagania wstępne
Przed utworzeniem, postępuj zgodnie z instrukcjami [utworzyć bazę wiedzy]() Aby utworzyć usługę Maker — strona główna z pytania i odpowiedzi.

Bot odpowiada na pytania z bazy wiedzy, który został utworzony za pomocą QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Utwórz Bot — strona główna
1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **Utwórz** nowego zasobu w bloku menu, a następnie wybierz **zobaczyć wszystkie**.

    ![Tworzenie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**.

    ![Wybór usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. W **bloku usługi Bot**, podaj wymagane informacje i wybierz **Utwórz**. To tworzy i wdraża usługi bot o QnAMakerDialog na platformie Azure.

    - Ustaw **Nazwa aplikacji** Twojego bot nazwy. Nazwa jest używana jako poddomeny po wdrożeniu programu bot do chmury (na przykład mynotesbot.azurewebsites.net).
    - Wybierz subskrypcję, grupy zasobów, plan usługi aplikacji i lokalizacji.
    - Wybierz **pytanie i odpowiedź** szablonu (Node.js lub C#) w polu Bot szablonu.
    - Zaznacz pole wyboru potwierdzenia powiadomień prawnych. Warunki prawne komunikatu znajdują się poniżej pola wyboru.

        ![Wybór usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Upewnij się, że wdrożono usługę bot.

    - Wybierz **powiadomienia** (ikonę dzwonka, która jest wzdłuż górnej krawędzi portalu Azure). Powiadomienie zostanie zmieniony z **rozpoczęto wdrażanie** do **wdrożenie zakończyło się pomyślnie**.
    - Po zmianie powiadomienia do **wdrożenie zakończyło się pomyślnie**, wybierz pozycję **przejdź do zasobu** na powiadomienie.

## <a name="chat-with-the-bot"></a>Rozmawiać z Bot
Wybieranie **przejdź do zasobu** przejście do bloku zasobów bot.

Po zarejestrowaniu bot kliknij przycisk **testów w sieci Web rozmowę** aby otworzyć okienko rozmowę sieci Web. Wpisz tekst "hello" w sieci Web rozmawiać.

![Rozmów w sieci web bot — strona główna](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot odpowie "Ustaw QnAKnowledgebaseId i QnASubscriptionKey w ustawieniach aplikacji. Dowiedz się, jak uzyskać je pod adresem https://aka.ms/qnaabssetup". Tej odpowiedzi potwierdza, że Twoje Bot — strona główna odebrała komunikat, ale nie nie Maker — strona główna bazy wiedzy knowledge base jeszcze skojarzony z nim. To zrobić w następnym kroku.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Nawiązać Maker — strona główna bazy wiedzy bot

1. Otwórz **ustawienia aplikacji** i edytować **QnAKnowledgebaseId**, **QnAAuthKey**i **QnAEndpointHostName** pola, które zawiera wartości Maker — strona główna bazy wiedzy.

    ![Ustawienia aplikacji](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Pobieranie Identyfikatora bazy wiedzy knowledge base, adres url hosta i klucz punktu końcowego na karcie Ustawienia bazy wiedzy w https://qnamaker.ai.
    - Zaloguj się do [Maker — strona główna](https://qnamaker.ai)
    - Przejdź do bazy wiedzy
    - Polecenie **ustawienia** kartę
    - **Publikowanie** bazy wiedzy, jeśli nie zrobiono

    ![Wartości Maker — strona główna](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Jeśli chcesz nawiązać połączenia z bot — strona główna wersja zapoznawcza bazy wiedzy knowledge base, ustaw wartość **Ocp-Apim-subskrypcji — klucz** do **QnAAuthKey**. Pozostaw **QnAEndpointHostName** puste.

## <a name="test-the-bot"></a>Testowanie bot
W portalu Azure kliknij **testów w sieci Web rozmowę** do testowania bot. 

![Bot Maker — strona główna](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Twoje Bot — strona główna teraz odpowiedzi z bazy wiedzy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Integracja Maker — strona główna i LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Zobacz także

- [Zarządzanie bazy wiedzy](https://qnamaker.ai)
- [Włącz z bot w różnych kanałów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
