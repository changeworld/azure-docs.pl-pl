---
title: Uaktualnianie usługi QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Można uaktualnić poszczególne składniki stosu usługi QnA Maker po pierwszym utworzeniu.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 22320564204b619d4510b3b005dc8ffe7ef5c45e
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911408"
---
# <a name="upgrade-your-qna-maker-service"></a>Uaktualnianie usługi QnA Maker
Można uaktualnić poszczególne składniki stosu usługi QnA Maker po pierwszym utworzeniu. Zobacz szczegóły dotyczące składników zależnych i wybór jednostki SKU [tutaj](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Uaktualnianie usługi QnA Maker zarządzania jednostki SKU

Gdy musisz mieć więcej pytań i odpowiedzi w Twojej bazie wiedzy, poza Twoja bieżąca warstwa Uaktualnij warstwę cenową usługi QnA Maker. 

Aby uaktualnić zarządzania usługi QnA Maker jednostki SKU:

1. Przejdź do zasobu usługi QnA Maker w witrynie Azure portal i wybierz **warstwa cenowa**.

    ![Zasób usługi QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Wybierz odpowiednie jednostki SKU i naciśnij klawisz **wybierz**.

    ![Ceny usługi QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uaktualnij usługę App service

 W przypadku bazy wiedzy musi obsługiwać więcej żądań z aplikacji klienckich, należy uaktualnić usługi app service w warstwie cenowej.

Możesz [skalowanie w górę](https://docs.microsoft.com/azure/app-service/web-sites-scale) lub w dół usługi App service.

1. Przejdź do zasobu usługi aplikacji w witrynie Azure portal, a następnie wybierz pozycję **skalowanie w górę** lub **skalowanie w dół** opcje zgodnie z potrzebami.

    ![Skala usługi aplikacji usługi QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uaktualnij usługę Azure Search

Podczas planowania mają wiele bazy wiedzy, Uaktualnij warstwę cenową usługi Azure Search. 

Obecnie nie jest możliwe do wykonania od razu uaktualnienia platformy Azure, wyszukaj jednostki SKU. Można jednak utworzyć nowy zasób usługi Azure search z odpowiednią jednostką SKU, przywrócenia danych do nowego zasobu i połączyć go do stosu usługi QnA Maker.

1. Utwórz nowy zasób usługi Azure search w witrynie Azure portal, a następnie wybierz odpowiednią jednostkę SKU.

    ![Usługa QnA Maker Azure wyszukiwania zasobów](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Przywróć indeksy z oryginalny zasób usługi Azure search na nową. Zobacz przykładowy kod przywracania kopii zapasowej [tutaj](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Po przywróceniu danych, przejdź do nowego zasobu usługi Azure search, wybierz opcję **klucze**i zanotuj **nazwa** i **klucz administratora**.

    ![Usługa QnA Maker w usłudze Azure search kluczy](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Aby połączyć nowy zasób usługi Azure search do stosu usługi QnA Maker, przejdź do usługi QnA Maker aplikacji.

    ![Usługa QnA Maker usługi App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Wybierz **ustawienia aplikacji** i Zastąp **AzureSearchName** i **AzureSearchAdminKey** pól z kroku 3.

    ![Ustawienie usługi App Service usługa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Uruchom ponownie usługi App service.

    ![Ponowne uruchomienie usługi App Service usługa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Użyj usługi QnA Maker API](../Quickstarts/csharp.md)
