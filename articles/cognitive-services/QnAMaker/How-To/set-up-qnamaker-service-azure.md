---
title: Konfigurowanie usługi QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967669"
---
# <a name="create-a-qna-maker-service"></a>Tworzenie usługi QnA Maker

Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

Ta procedura służy do wdrażania kilku zasobów platformy Azure. Razem te zasoby zarządzania zawartością w bazie wiedzy knowledge base i oferuje możliwości, udzielenie odpowiedzi na pytanie, chociaż punkt końcowy.

1. Zaloguj się do Azure Portal i [Utwórz zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Wybierz pozycję **Utwórz** po przeczytaniu warunków i postanowień.

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. W **QnA Maker**, wybierz odpowiednią warstwy i regiony.

    ![Tworzenie nowej usługi QnA Maker — warstwa cenowa i regiony](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Wypełnij **nazwa** na unikatową nazwę do identyfikacji usługi QnA Maker. Ta nazwa identyfikuje również punkt końcowy usługi QnA Maker, do którego mają zostać skojarzone z bazy wiedzy.
    * Wybierz **subskrypcji** , w którym zostanie wdrożone do zasobu usługi QnA Maker.
    * Wybierz **warstwę cenową** dla usług zarządzania QNA Maker (Portal i interfejsy API zarządzania). Zobacz [tutaj](https://aka.ms/qnamaker-pricing) Aby uzyskać szczegółowe informacje o cenach jednostek SKU.
    * Utwórz nową **grupy zasobów** (zalecane) lub użyć istniejącego, w której ma zostać wdrożony tego zasobu usługi QnA Maker. QnA Maker tworzy kilka zasobów platformy Azure; podczas tworzenia grupy zasobów do przechowywania tych zasobów można łatwo znajdować i usuwać te zasoby oraz nimi zarządzać według nazwy grupy zasobów.
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
    * Aby uzyskać środki oszczędnościowe, możesz [udostępnić](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) kilka zasobów platformy Azure utworzonych dla QNA Maker. 

1. Po sprawdzeniu poprawności wszystkich pól możesz wybrać pozycję **Utwórz** , aby rozpocząć wdrażanie tych usług w ramach subskrypcji. Potrwa kilka minut.

1. Po zakończeniu wdrożenia zostanie wyświetlony następujące zasoby utworzone w ramach subskrypcji.

    ![Nowa usługa QnA Maker utworzony zasób](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Region usługi zarządzania

Usługa zarządzania programu QnA Maker, używana tylko w portalu & do wstępnego przetwarzania danych, jest dostępna tylko w regionie zachodnie stany USA. W tym regionie zachodnie stany USA nie są przechowywane żadne dane klienta.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)
