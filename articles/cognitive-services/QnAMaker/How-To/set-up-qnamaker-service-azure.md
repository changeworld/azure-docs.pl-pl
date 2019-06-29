---
title: Konfigurowanie usługi QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4bc40c0d4d44ea4dd809f59965ec5d1107be8541
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439770"
---
# <a name="create-a-qna-maker-service"></a>Tworzenie usługi QnA Maker

Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

Ta procedura służy do wdrażania zasobów platformy Azure w kilku. Razem te zasoby zarządzania zawartością w bazie wiedzy knowledge base i oferuje możliwości, udzielenie odpowiedzi na pytanie, chociaż punkt końcowy.

1. Zaloguj się do witryny Azure portal i [tworzenia usługi QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) zasobów.

1. Wybierz **Utwórz** zapoznanie się z warunków i postanowień.

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. W **QnA Maker**, wybierz odpowiednią warstwy i regiony.

    ![Tworzenie nowej usługi QnA Maker — warstwa cenowa i regiony](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Wypełnij **nazwa** na unikatową nazwę do identyfikacji usługi QnA Maker. Ta nazwa identyfikuje również punkt końcowy usługi QnA Maker, do którego mają zostać skojarzone z bazy wiedzy.
    * Wybierz **subskrypcji** , w którym zostanie wdrożone do zasobu usługi QnA Maker.
    * Wybierz **warstwa cenowa** usług zarządzania usługi QnA Maker (portal i interfejsów API zarządzania). Zobacz [tutaj](https://aka.ms/qnamaker-pricing) Aby uzyskać szczegółowe informacje o cenach jednostek SKU.
    * Utwórz nową **grupy zasobów** (zalecane) lub użyć istniejącego, w której ma zostać wdrożony tego zasobu usługi QnA Maker. Usługa QnA Maker tworzy kilka zasobów platformy Azure; Podczas tworzenia grupy zasobów do przechowywania tych zasobów, można łatwo znaleźć, zarządzanie, a następnie usunąć tych zasobów, nazwę grupy zasobów.
    * Wybierz **lokalizację grupy zasobów**.
    * Wybierz **warstwa cenowa wyszukiwania** usługi Azure Search. Jeśli widzisz opcję w warstwie bezpłatna wyszarzone, oznacza to, że masz już wdrożone w subskrypcji warstwy bezpłatna usługi Azure Search. W takim przypadku konieczne będzie rozpoczęcie od warstwy podstawowa usługi Azure Search. Zobacz szczegóły cennika usługi Azure search [tutaj](https://azure.microsoft.com/pricing/details/search/).
    * Wybierz **Lokalizacja wyszukiwania** miejscu danych usługi Azure Search do wdrożenia. Ograniczenia, w której muszą być przechowywane dane klientów poinformuje lokalizacji, wybrany dla usługi Azure Search.
    * Nadaj nazwę usłudze App service w **nazwy aplikacji**.
    * Domyślnie usługa App service domyślnie do warstwy standardowa (S1). Plan można zmienić po utworzeniu. Zobacz więcej szczegółów dotyczących App service — ceny [tutaj](https://azure.microsoft.com/pricing/details/app-service/).
    * Wybierz **lokalizacja witryny sieci Web** wdrożonym usługi App Service.

        > [!NOTE]
        > Lokalizacja wyszukiwania może być inny niż lokalizacja witryny sieci Web.

    * Wybierz, czy chcesz włączyć **usługi Application Insights** czy nie. Jeśli **usługi Application Insights** jest włączona, narzędzie QnA Maker gromadzi dane telemetryczne w ruchu, dzienniki czatu i błędów.
    * Wybierz **Lokalizacja usługi App insights** wdrożonym zasób usługi Application Insights.
    * Miary oszczędności kosztów, możesz [udostępnianie](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) niektórych, ale nie wszystkie zasoby platformy Azure utworzonych dla usługi QnA Maker. 

1. Po zweryfikowaniu wszystkich pól, można wybrać **Utwórz** można uruchomić wdrożenia tych usług w Twojej subskrypcji. Potrwa kilka minut.

1. Po zakończeniu wdrożenia zostanie wyświetlony następujące zasoby utworzone w ramach subskrypcji.

    ![Nowa usługa QnA Maker utworzony zasób](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Region usługi zarządzania

Usługa zarządzania usługi QnA Maker, używana tylko dla portalu & do początkowego przetwarzania danych, jest dostępna tylko w regionie zachodnie stany USA. Nie dane klientów są przechowywane w tej usłudze zachodnie stany USA.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)
