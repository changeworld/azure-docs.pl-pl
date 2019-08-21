---
title: Uaktualnij usługę QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Udostępnianie lub uaktualnianie usług QnA Maker w celu lepszego zarządzania zasobami.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640479"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Udostępnianie lub uaktualnianie usługi QnA Maker
Udostępnianie lub uaktualnianie usług QnA Maker w celu lepszego zarządzania zasobami. 

Po wstępnym utworzeniu można uaktualnić poszczególne składniki stosu QnA Maker. Zobacz szczegóły dotyczące składników zależnych i wyboru jednostki SKU [tutaj](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Udostępnianie istniejących usług za pomocą QnA Maker

QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z zalet udostępniania kosztów, Skorzystaj z poniższej tabeli, aby dowiedzieć się, co można i czego nie można udostępnić:

|Usługa|Udostępnij|
|--|--|
|Cognitive Services|X|
|Plan usługi aplikacji|✔|
|App Service|X|
|Application Insights|✔|
|Usługa wyszukiwania|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Uaktualnij jednostkę SKU zarządzania QnA Maker

Jeśli potrzebujesz więcej pytań i odpowiedzi w bazie wiedzy, poza bieżącą warstwą, Uaktualnij warstwę cenową usługi QnA Maker Service. 

Aby uaktualnić jednostkę SKU zarządzania QnA Maker:

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz pozycję **warstwa cenowa**.

    ![Zasób QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Wybierz odpowiednią jednostkę SKU i naciśnij **pozycję Wybierz**.

    ![Cennik QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uaktualnij usługę App Service

 Gdy baza wiedzy musi obpracować więcej żądań z aplikacji klienckiej, należy uaktualnić warstwę cenową usługi App Service.

Usługę App Service można [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół.

1. Przejdź do zasobu usługi App Service w Azure Portal i wybierz pozycję **Skaluj w górę** lub **w dół** odpowiednio do potrzeb.

    ![Skalowanie usługi App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uaktualnij usługę Azure Search

Planując posiadanie wielu baz wiedzy, należy uaktualnić warstwę cenową usługi Azure Search. 

Obecnie nie jest możliwe przeprowadzenie uaktualnienia w miejscu jednostki SKU usługi Azure Search. Można jednak utworzyć nowy zasób usługi Azure Search z odpowiednią jednostką SKU, przywrócić dane do nowego zasobu, a następnie połączyć je z stosem QnA Maker.

1. Utwórz nowy zasób usługi Azure Search w Azure Portal i wybierz żądaną jednostkę SKU.

    ![QnA Maker zasobów usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Przywróć indeksy z oryginalnego zasobu usługi Azure Search do nowego. Zapoznaj się z przykładowym [](https://github.com/pchoudhari/QnAMakerBackupRestore)kodem przywracania kopii zapasowej.

3. Po przywróceniu danych przejdź do nowego zasobu usługi Azure Search, wybierz pozycję **klucze**i zanotuj **nazwę** i **klucz administratora**.

    ![QnA Maker kluczy usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Aby połączyć nowy zasób usługi Azure Search ze stosem QnA Maker, przejdź do usługi QnA Maker App Service.

    ![QnA Maker appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Wybierz pozycję **Ustawienia aplikacji** i Zastąp pola **AzureSearchName** i **AzureSearchAdminKey** w kroku 3.

    ![QnA Maker ustawienie appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Uruchom ponownie usługi App service.

    ![QnA Maker ponowne uruchomienie appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Użyj interfejs API usługi QnA Maker](../Quickstarts/csharp.md)
