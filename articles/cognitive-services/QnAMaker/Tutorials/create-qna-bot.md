---
title: Bot pytań i odpowiedzi — usługa Azure Bot Service — QnA Maker
titleSuffix: Azure Cognitive Services
description: Ten samouczek przeprowadzi Cię przez tworzenie bota pytań i odpowiedzi, przy użyciu usługi Azure Bot service w wersji 3 w witrynie Azure portal.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.openlocfilehash: 218103f2c75ec1016a997c259767ccd011191fab
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879615"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Samouczek: Utwórz Bota pytań i odpowiedzi z usługi Azure Bot Service w wersji 3

Ten samouczek przeprowadzi Cię przez tworzenie bota pytań i odpowiedzi, przy użyciu usługi Azure Bot service v3 w [witryny Azure portal](https://portal.azure.com) bez pisania żadnego kodu. Proces łączenia dwóch opublikowanych bazy wiedzy (KB) robota przebiega tak prosta jak zmiana ustawień aplikacji botów. 

> [!Note] 
> Ten temat dotyczy wersji 3 Bot zestawu SDK. Można znaleźć w wersji 4 [tutaj](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie usługi Azure Bot Service za pomocą szablonu usługi QnA Maker
> * Rozmawiać z botami w celu sprawdzenia, czy kod działa 
> * Połącz opublikowanych wiedzy do robota
> * Testowanie bot do pytania

W tym artykule, można użyć bezpłatnego narzędzia QnA Maker [usługi](../how-to/set-up-qnamaker-service-azure.md).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć wiedzy opublikowane w ramach tego samouczka. Jeśli nie masz, wykonaj kroki opisane w [tworzenie bazy wiedzy](../How-To/create-knowledge-base.md) Aby utworzyć usługę QnA Maker za pomocą pytań i odpowiedzi.

## <a name="create-a-qna-bot"></a>Utwórz Bota pytań i odpowiedzi

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.

    ![Tworzenie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**.

    ![Wybieranie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    - Ustaw **nazwy aplikacji** nazwę Twój bot. Nazwa jest używana jako domenę podrzędną, gdy Twój bot jest wdrażane w chmurze (na przykład mynotesbot.azurewebsites.net).
    - Wybierz subskrypcję, grupy zasobów, plan usługi App service i lokalizacji.

4. Aby użyć szablonów w wersji 3, wybierz wersję zestawu SDK **zestawu SDK w wersji 3** i zestawu SDK języka **C#** lub **Node.js**.

    ![Bot ustawień zestawu sdk](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Wybierz **pytanie i odpowiedź** szablonu w polu szablonu Bota, a następnie Zapisz ustawienia szablonu, wybierając **wybierz**.

    ![Zapisz bot wybór szablonu usługi](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Przejrzyj ustawienia, a następnie wybierz **Utwórz**. To tworzy i wdraża usługi botów za pomocą platformy Azure.

    ![Tworzenie botów](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Upewnij się, czy usługa bot service został pomyślnie wdrożony.

    - Wybierz **powiadomienia** (ikonę dzwonka, który znajduje się wzdłuż górnej krawędzi w witrynie Azure Portal). Powiadomienie zmieni się z **Wdrażanie rozpoczęte** do **wdrażanie zakończyło się pomyślnie**.
    - Po powiadomienie zmieni się na **wdrażanie zakończyło się pomyślnie**, wybierz opcję **przejdź do zasobu** na powiadomienia.

## <a name="chat-with-the-bot"></a>Rozmawiać z Botami

Wybieranie **przejdź do zasobu** spowoduje przejście do zasobu botów.

Wybierz **testu w czatów internetowych** aby otworzyć okienko czatów internetowych. Wpisz "hi" czatów internetowych.

![Rozmowy w sieci web bota pytań i odpowiedzi](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot odpowiada za pomocą "Ustaw QnAKnowledgebaseId i QnASubscriptionKey w ustawieniach aplikacji. Ta odpowiedź potwierdza Bota pytań i odpowiedzi otrzymał komunikat, że nie jest jeszcze skojarzone z nią nie usługi QnA Maker bazie wiedzy knowledge base. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Usługa QnA Maker wiedzy nawiązać połączenie z bota

1. Otwórz **ustawienia aplikacji** i edytować **QnAKnowledgebaseId**, **QnAAuthKey**i **QnAEndpointHostName** pola, które zawiera wartości usługi QnA Maker bazie wiedzy knowledge base.

    ![Ustawienia aplikacji](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Pobierz swój identyfikator bazy wiedzy knowledge base, adres url hosta i klucza punktu końcowego z karty Ustawienia w portalu narzędzia QnA Maker bazy wiedzy.

   - Zaloguj się do [usługi QnA Maker](https://qnamaker.ai)
   - Przejdź do bazy wiedzy
   - Wybierz **ustawienia** kartę
   - **Publikowanie** wiedzy, jeśli nie zrobiono

     ![Usługa QnA Maker wartości](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>Testowanie robota

W witrynie Azure portal wybierz **testowania w czatów internetowych** do testowania robota. 

![Bot usługi QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Odpowiedzi Bota pytań i odpowiedzi z bazy wiedzy.

## <a name="related-to-qna-maker-bots"></a>Powiązane z botami usługi QnA Maker

* Bot pomocy usługi QnA Maker, używane w portalu narzędzia QnA Maker jest dostępna jako [przykładowe bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Ikona bot pomocy usługi QnA Maker jest czerwony robota](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Boty opieki zdrowotnej](https://docs.microsoft.com/HealthBot/qna_model_howto) Użyj usługi QnA Maker jako jeden z jego [modeli językowych](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka bot, bot należy usunąć w witrynie Azure portal. Usługi botów obejmują:

* Plan usługi App Service
* Usługa wyszukiwania
* Usługa Cognitive
* Usługa App service
* Opcjonalnie może również obejmować z usługi application insights i magazynu dla danych szczegółowych informacji w aplikacji

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pojęcia: Baza wiedzy](../concepts/knowledge-base.md)

## <a name="see-also"></a>Zobacz także

- [Zarządzanie bazy wiedzy](https://qnamaker.ai)
- [Włącz bota w różnych kanałów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
