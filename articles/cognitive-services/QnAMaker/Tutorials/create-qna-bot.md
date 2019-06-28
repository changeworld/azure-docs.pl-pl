---
title: Bot pytań i odpowiedzi — usługa Azure Bot Service — QnA Maker
titleSuffix: Azure Cognitive Services
description: Utwórz czatbot pytań i odpowiedzi na podstawie strony publikowania dla istniejącej bazy wiedzy. Ten bot używa v4 Bot Framework SDK. Nie trzeba pisać kodu do tworzenia bota, cały kod znajduje się za Ciebie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 1d475d988847bd4cc9e37b91a0c5d28678cb05f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446650"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Samouczek: Utwórz Bota pytań i odpowiedzi z usługi Azure Bot Service w wersji 4

Utwórz czatbot pytań i odpowiedzi z **Publikuj** strony dla istniejącej bazy wiedzy. Ten bot używa v4 Bot Framework SDK. Nie trzeba pisać kodu do tworzenia bota, cały kod znajduje się za Ciebie.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie usługi Azure Bot Service z istniejącej bazy wiedzy
> * Rozmawiać z botami w celu sprawdzenia, czy kod działa 

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć wiedzy opublikowane w ramach tego samouczka. Jeśli nie masz, wykonaj kroki opisane w [Utwórz i odpowiedź z bazy wiedzy](create-publish-query-in-portal.md) samouczka, aby utworzyć narzędzie QnA Maker wiedzy pytań i odpowiedzi.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Utwórz Bota pytań i odpowiedzi

Utwórz bota jako aplikację kliencką na bazie wiedzy knowledge base. 

1. W portalu narzędzia QnA Maker, przejdź do **Publikuj** strony, a następnie opublikuj bazę wiedzy. Wybierz **tworzenie Botów**. 

    ![W portalu narzędzia QnA Maker przejdź do strony publikowania, a następnie opublikuj bazę wiedzy. Wybierz pozycję Utwórz Bota.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Witryna Azure portal zostanie otwarty z konfiguracją tworzenia botów.

1.  Wprowadź ustawienia, aby utworzyć bot:

    |Ustawienie|Wartość|Przeznaczenie|
    |--|--|--|
    |Nazwa bota|`my-tutorial-kb-bot`|Jest to nazwa zasobu platformy Azure dla bota.|
    |Subskrypcja|Zobacz cel.|Wybierz tej samej subskrypcji co użyte do utworzenia zasobów usługi QnA Maker.|
    |Grupa zasobów|`my-tutorial-rg`|Grupa zasobów, używany dla wszystkich powiązanych bot zasobów platformy Azure.|
    |Location|`west us`|Lokalizacja zasobów platformy Azure bot.|
    |Warstwa cenowa|`F0`|Warstwy bezpłatna usługa Azure bot service.|
    |Nazwa aplikacji|`my-tutorial-kb-bot-app`|Jest to aplikacji sieci web do obsługi tylko Twój bot. To nie powinna być taką samą nazwę aplikacji, ponieważ używa już usługi QnA Maker. Udostępnianie aplikacji sieci web usługi QnA Maker z innych zasobów nie jest obsługiwane.|
    |Zestaw SDK języka|C#|Jest to podstawowy język programowania używany przez zestaw SDK platformy bot framework. Dopuszczalne są [ C# ](https://github.com/Microsoft/botbuilder-dotnet) lub [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |Klucz uwierzytelniania pytań i odpowiedzi|**Nie zmieniaj**|Ta wartość jest wypełniane automatycznie.|
    |App service plan/lokalizacja|**Nie zmieniaj**|W tym samouczku lokalizacji nie jest ważna.|
    |Azure Storage|**Nie zmieniaj**|Konwersacja dane są przechowywane w tabelach usługi Azure Storage.|
    |Application Insights|**Nie zmieniaj**|Rejestrowanie są wysyłane do usługi Application Insights.|
    |Identyfikator aplikacji firmy Microsoft|**Nie zmieniaj**|Usługi Active directory użytkownika i hasło są wymagane.|

    ![Utwórz bota bazy wiedzy przy użyciu tych ustawień.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Poczekaj kilka minut, aż powiadomienie procesu tworzenia bota zgłosi powodzenie operacji.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Rozmawiać z Botami

1. W witrynie Azure portal Otwórz nowy zasób bot z powiadomienia. 

    ![W witrynie Azure portal Otwórz nowy zasób bot z powiadomienia.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Z **zarządzania Bot**, wybierz opcję **testu w czatów internetowych** i wprowadź: `How large can my KB be?`. Bot będzie odpowiadać za pomocą: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Przetestuj nowe bot bazy wiedzy knowledge base.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Aby uzyskać więcej informacji na temat Boty Azure zobacz [Użyj usługi QnA Maker odpowiedzi na pytania](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka bot, bot należy usunąć w witrynie Azure portal. 

Jeśli utworzono nową grupę zasobów dla zasobów bot, Usuń grupę zasobów. 

Jeśli nie utworzono nową grupę zasobów, musisz znaleźć zasoby skojarzone z bota. Najprostszym sposobem jest wyszukiwanie według nazwy, bot i aplikacja robota. Zasoby bot obejmują:

* Plan usługi App Service
* Usługa wyszukiwania
* Usługa Cognitive
* Usługa App service
* Opcjonalnie może również obejmować z usługi application insights i magazynu dla danych szczegółowych informacji w aplikacji


## <a name="related-to-qna-maker-bots"></a>Powiązane z botami usługi QnA Maker

* Bot pomocy usługi QnA Maker, używane w portalu narzędzia QnA Maker jest dostępna jako [przykładowe bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Ikona bot pomocy usługi QnA Maker jest czerwony robota](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Boty opieki zdrowotnej](https://docs.microsoft.com/HealthBot/qna_model_howto) Użyj usługi QnA Maker jako jeden z jego [modeli językowych](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pojęcia: Baza wiedzy](../concepts/knowledge-base.md)

## <a name="see-also"></a>Zobacz także

- [Zarządzanie bazy wiedzy](https://qnamaker.ai)
- [Włącz bota w różnych kanałów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
