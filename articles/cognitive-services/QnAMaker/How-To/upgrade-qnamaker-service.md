---
title: Uaktualnij usługę Maker — strona główna - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak uaktualnić usługi Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348648"
---
# <a name="upgrade-your-qna-maker-service"></a>Uaktualnij usługę Maker — strona główna
Można uaktualnić po pierwszym utworzeniu poszczególne składniki stosu Maker — strona główna. Zobacz szczegóły składników zależnych i wybór jednostki SKU [tutaj](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Uaktualnij zarządzania Maker — strona główna jednostki SKU
Aby uaktualnić zarządzania Maker — strona główna SKU:
1. Przejdź do zasobu Maker — strona główna w portalu Azure i wybierz **warstwa cenowa**.

    ![Zasób Maker — strona główna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Wybierz odpowiednie jednostki SKU i naciśnij klawisz **wybierz**.

    ![Cennik Maker — strona główna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uaktualnij usługi aplikacji
Możesz [skalowanie w górę](https://docs.microsoft.com/azure/app-service/web-sites-scale) lub skali usługi App service.

1. Przejdź do zasobu usługi aplikacji w portalu Azure i wybierz **skalowanie w górę** lub **dół** opcje zgodnie z potrzebami.

    ![Skalowanie usługi aplikacji — strona główna Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uaktualnij usługi Azure Search
Obecnie nie jest możliwe do wykonania w miejscu SKU wyszukiwania uaktualnienia platformy Azure. Można jednak utworzyć nowy zasób usługi Azure search z żądaną jednostki SKU, Przywróć dane do nowego zasobu i połączyć ją stosu Maker — strona główna.

1. Utwórz nowy zasób usługi Azure search w portalu Azure i wybierz odpowiednią jednostki SKU.

    ![Azure Maker — strona główna wyszukiwania zasobów](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Przywróć indeksy z oryginalnego zasobu usługi Azure search do nowego. Zobacz przykładowy kod z kopii zapasowej [tutaj](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Po przywróceniu danych, przejdź do nowego zasobu usługi Azure search, wybierz opcję **klucze**i zanotuj **nazwa** i **klucz administratora**.

    ![Klucze wyszukiwania Azure Maker — strona główna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Aby połączyć nowy zasób usługi Azure search stosu Maker — strona główna, przejdź do usługi — strona główna Maker aplikacji.

    ![Appservice Maker — strona główna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Wybierz **ustawienia aplikacji** i Zastąp **AzureSearchName** i **AzureSearchAdminKey** pól z kroku 3.

    ![Ustawienie appservice Maker — strona główna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Uruchom ponownie usługę aplikacji.

    ![Ponowne uruchomienie usługi aplikacji — strona główna Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Użyj Maker — strona główna interfejsu API](../Quickstarts/csharp.md)
