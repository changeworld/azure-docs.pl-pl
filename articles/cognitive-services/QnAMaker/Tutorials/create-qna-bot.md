---
title: Bot pytań i odpowiedzi z usługi Azure Bot Service — QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: 0c6feb67b273ec30afba89f2d4d9b59a8a9f5acf
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731420"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Utwórz Bota pytań i odpowiedzi z usługi Azure Bot Service
Ten samouczek przeprowadzi Cię przez tworzenie bota pytań i odpowiedzi, przy użyciu usługi Azure Bot service w witrynie Azure portal.

## <a name="prerequisite"></a>Wymagania wstępne
Przed utworzeniem, wykonaj kroki opisane w [tworzenie bazy wiedzy](../How-To/create-knowledge-base.md) Aby utworzyć usługę QnA Maker za pomocą pytań i odpowiedzi.

Bot odpowiada na pytania z bazy wiedzy, który został utworzony, za pośrednictwem QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Utwórz Bota pytań i odpowiedzi
1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Utwórz** nowego zasobu w bloku menu, a następnie wybierz **holograficznych**.

    ![Tworzenie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**.

    ![Wybieranie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. W **bloku Bot Service**, podaj wymagane informacje i wybierz **Utwórz**. To tworzy i wdraża usługi botów za pomocą QnAMakerDialog na platformie Azure.

    - Ustaw **nazwy aplikacji** nazwę Twój bot. Nazwa jest używana jako domenę podrzędną, gdy Twój bot jest wdrażane w chmurze (na przykład mynotesbot.azurewebsites.net).
    - Wybierz subskrypcję, grupy zasobów, plan usługi App service i lokalizacji.
    - Wybierz **pytanie i odpowiedź** szablonu (Node.js lub C#) dla pola szablonu Bota.
    - Zaznacz pole wyboru potwierdzenia prawne. Warunki prawne są wyświetlane poniżej pola wyboru.

        ![Wybieranie usługi BOT](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Upewnij się, czy usługa bot service został pomyślnie wdrożony.

    - Wybierz **powiadomienia** (ikonę dzwonka, który znajduje się wzdłuż górnej krawędzi w witrynie Azure Portal). Powiadomienie zmieni się z **Wdrażanie rozpoczęte** do **wdrażanie zakończyło się pomyślnie**.
    - Po powiadomienie zmieni się na **wdrażanie zakończyło się pomyślnie**, wybierz opcję **przejdź do zasobu** na powiadomienia.

## <a name="chat-with-the-bot"></a>Rozmawiać z Botami
Wybieranie **przejdź do zasobu** spowoduje przejście do bloku zasobów botów.

Po zarejestrowaniu bot kliknij **testu w czatów internetowych** aby otworzyć okienko czatów internetowych. Wpisz "hello" w czatów internetowych.

![Rozmowy w sieci web bota pytań i odpowiedzi](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot odpowiada za pomocą "Ustaw QnAKnowledgebaseId i QnASubscriptionKey w ustawieniach aplikacji. Dowiedz się, jak pobrać je na https://aka.ms/qnaabssetup". Ta odpowiedź potwierdza Bota pytań i odpowiedzi otrzymał komunikat, że nie jest jeszcze skojarzone z nią nie usługi QnA Maker bazie wiedzy knowledge base. To zrobić w następnym kroku.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Usługa QnA Maker wiedzy nawiązać połączenie z bota

1. Otwórz **ustawienia aplikacji** i edytować **QnAKnowledgebaseId**, **QnAAuthKey**i **QnAEndpointHostName** pola, które zawiera wartości usługi QnA Maker bazie wiedzy knowledge base.

    ![Ustawienia aplikacji](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Pobierz swój identyfikator bazy wiedzy knowledge base, adres url hosta i klucza punktu końcowego z karty Ustawienia bazy wiedzy w https://qnamaker.ai.
    - Zaloguj się do [usługi QnA Maker](https://qnamaker.ai)
    - Przejdź do bazy wiedzy
    - Kliknij pozycję **ustawienia** kartę
    - **Publikowanie** wiedzy, jeśli nie zrobiono

    ![Usługa QnA Maker wartości](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Jeśli chcesz połączyć z wersji zapoznawczej w bazie wiedzy knowledge base przy użyciu bota pytań i odpowiedzi, ustaw wartość **Ocp-Apim-Subscription-Key** do **QnAAuthKey**. Pozostaw **QnAEndpointHostName** puste.

## <a name="test-the-bot"></a>Testowanie robota
W witrynie Azure portal kliknij pozycję **testowania w czatów internetowych** do testowania robota. 

![Bot usługi QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Bota pytań i odpowiedzi jest teraz odpowiedzi z bazy wiedzy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Integracja usługi QnA Maker i LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Zobacz także

- [Zarządzanie bazy wiedzy](https://qnamaker.ai)
- [Włącz bota w różnych kanałów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
