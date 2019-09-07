---
title: 'Samouczek: Bot pytań i odpowiedzi — usługa Azure Bot Service — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Utwórz QnA rozmowę bot ze strony publikowania dla istniejącej bazy wiedzy. Ten bot używa zestawu SDK programu bot Framework v4. Nie trzeba pisać żadnego kodu w celu skompilowania bot, który jest dostarczany przez Ciebie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ea6e0d266c181d930f3d18171b09d222e53da7ab
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390910"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Samouczek: Tworzenie QnA bot z Azure Bot Service v4

Utwórz QnA rozmowę bot ze strony **publikowania** dla istniejącej bazy wiedzy. Ten bot używa zestawu SDK programu bot Framework v4. Nie trzeba pisać żadnego kodu w celu skompilowania bot, który jest dostarczany przez Ciebie.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie Azure Bot Service z istniejącej bazy wiedzy
> * Rozmawiać z botami w celu sprawdzenia, czy kod działa 

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć wiedzy opublikowane w ramach tego samouczka. Jeśli nie masz takiego rozwiązania, wykonaj kroki opisane w samouczku [Tworzenie i odbieranie z artykułu KB](create-publish-query-in-portal.md) , aby utworzyć QNA Maker bazę wiedzy z pytaniami i odpowiedziami.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Utwórz Bota pytań i odpowiedzi

Utwórz bot jako aplikację kliencką dla bazy wiedzy. 

1. W portalu QnA Maker przejdź do strony **Publikowanie** i Opublikuj bazę wiedzy. Wybierz pozycję **Utwórz bot**. 

    ![W portalu QnA Maker przejdź do strony publikowanie i Opublikuj bazę wiedzy. Wybierz pozycję Utwórz bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Zostanie otwarty Azure Portal z konfiguracją tworzenia bot.

1.  Wprowadź ustawienia, aby utworzyć bot:

    |Ustawienie|Value|Cel|
    |--|--|--|
    |Nazwa bota|`my-tutorial-kb-bot`|Jest to nazwa zasobu platformy Azure dla bot.|
    |Subscription|Zobacz cel.|Wybierz tę samą subskrypcję, która była używana do tworzenia zasobów QnA Maker.|
    |Resource group|`my-tutorial-rg`|Grupa zasobów używana dla wszystkich zasobów platformy Azure związanych z bot.|
    |Location|`west us`|Bot lokalizacji zasobów platformy Azure.|
    |Warstwa cenowa|`F0`|Warstwa Bezpłatna usługi Azure bot.|
    |Nazwa aplikacji|`my-tutorial-kb-bot-app`|Jest to aplikacja internetowa, która obsługuje tylko Twoje bot. Ta nazwa aplikacji nie powinna być taka sama, jak usługa QnA Maker jest już używana. Udostępnianie aplikacji sieci Web QnA Maker przy użyciu dowolnego innego zasobu nie jest obsługiwane.|
    |Język zestawu SDK|C#|Jest to podstawowy język programowania używany przez zestaw SDK bot Framework. Dostępne opcje to [C#](https://github.com/Microsoft/botbuilder-dotnet) lub [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |Klucz uwierzytelniania QnA|**Nie zmieniaj**|Ta wartość jest wypełniana.|
    |Plan/Lokalizacja usługi App Service|**Nie zmieniaj**|W tym samouczku lokalizacja nie jest ważna.|
    |Azure Storage|**Nie zmieniaj**|Dane konwersacji są przechowywane w tabelach usługi Azure Storage.|
    |Application Insights|**Nie zmieniaj**|Rejestrowanie jest wysyłane do Application Insights.|
    |Identyfikator aplikacji firmy Microsoft|**Nie zmieniaj**|Użytkownik usługi Active Directory i hasło są wymagane.|

    ![Utwórz bot bazy wiedzy przy użyciu tych ustawień.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Poczekaj kilka minut, aż powiadomienie o procesie tworzenia bot zostanie zgłoszone pomyślnie.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Rozmawiać z Botami

1. W Azure Portal Otwórz nowy zasób bot z powiadomienia. 

    ![W Azure Portal Otwórz nowy zasób bot z powiadomienia.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. W obszarze **Zarządzanie bot**wybierz pozycję **Testuj w rozmowie w sieci Web** i wprowadź: `How large can my KB be?`. Bot będzie odpowiadać: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Przetestuj nową bazę wiedzy bot.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Aby uzyskać więcej informacji na temat usługi Azure botów, zobacz [używanie QNA Maker do odpowiadania na pytania](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka bot, bot należy usunąć w witrynie Azure portal. 

Jeśli utworzono nową grupę zasobów dla zasobów bot, Usuń grupę zasobów. 

Jeśli nie utworzono nowej grupy zasobów, należy znaleźć zasoby skojarzone z bot. Najprostszym sposobem jest przeszukanie nazwy aplikacji bot i bot. Zasoby bot obejmują:

* Plan usługi App Service
* Usługa wyszukiwania
* Usługa Cognitive
* Usługa App service
* Opcjonalnie może również obejmować z usługi application insights i magazynu dla danych szczegółowych informacji w aplikacji


## <a name="related-to-qna-maker-bots"></a>Powiązane z QnA Maker botów

* QnA Maker pomocy bot, używane w portalu QnA Maker, jest dostępny jako [przykład bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![Ikona bot pomocy QnA Maker jest czerwoną robotem](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Botów opieki zdrowotnej](https://docs.microsoft.com/HealthBot/qna_model_howto) QNA Maker używa jako jednego z [modeli językowych](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pojęcia: Baza wiedzy](../concepts/knowledge-base.md)

## <a name="see-also"></a>Zobacz także

- [Zarządzanie bazy wiedzy](https://qnamaker.ai)
- [Włącz bota w różnych kanałów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
