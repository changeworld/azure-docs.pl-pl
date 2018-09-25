---
title: Konfigurowanie usługi QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 87bcd23983c1d973f7e90caf806092c909b7b9e4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032550"
---
# <a name="create-a-qna-maker-service"></a>Tworzenie usługi QnA Maker

Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.

Ten Instalator wdraża kilka zasobów platformy Azure. Razem te zasoby zarządzania zawartością w bazie wiedzy knowledge base i oferuje możliwości, udzielenie odpowiedzi na pytanie, chociaż punkt końcowy.

1. Zaloguj się do witryny [Azure Portal](<https://portal.azure.com>).

2.  Kliknij pozycję **Dodaj nowy zasób**, wpisz "usługi qna maker" w polu wyszukiwania i wybierz zasób usługi QnA Maker

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Kliknij pozycję **Utwórz** zapoznanie się z warunków i postanowień.

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. W **QnA Maker**, wybierz odpowiednią warstwy i regiony.

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Wypełnij **nazwa** na unikatową nazwę do identyfikacji usługi QnA Maker. Ta nazwa identyfikuje również punkt końcowy usługi QnA Maker, do którego mają zostać skojarzone z bazy wiedzy.
    * Wybierz **subskrypcji** , w którym zostanie wdrożone do zasobu usługi QnA Maker.
    * Wybierz **warstwę cenową zarządzania** usług zarządzania usługi QnA Maker (portal i interfejsów API zarządzania). Zobacz [tutaj](https://aka.ms/qnamaker-pricing) Aby uzyskać szczegółowe informacje o cenach jednostek SKU.
    * Utwórz nową **grupy zasobów** (zalecane) lub użyć istniejącego, w której ma zostać wdrożony tego zasobu usługi QnA Maker.
    * Wybierz **warstwa cenowa wyszukiwania** usługi Azure Search. Jeśli widzisz opcję w warstwie bezpłatna wyszarzone, oznacza to, że masz już wdrożone w subskrypcji warstwy bezpłatna usługi Azure Search. W takim przypadku konieczne będzie rozpoczęcie od warstwy podstawowa usługi Azure Search. Zobacz szczegóły cennika usługi Azure search [tutaj](https://azure.microsoft.com/en-us/pricing/details/search/).
    * Wybierz **Lokalizacja wyszukiwania** miejscu danych usługi Azure Search do wdrożenia. Ograniczenia, w której muszą być przechowywane dane klientów poinformuje lokalizacji, wybrany dla usługi Azure Search.
    * Nadaj nazwę usłudze App service w **nazwy aplikacji**.
    * Domyślnie usługa App service domyślnie do warstwy standardowa (S1). Plan można zmienić po utworzeniu. Zobacz więcej szczegółów dotyczących App service — ceny [tutaj](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Wybierz **lokalizacja witryny sieci Web** wdrożonym usługi App Service.

        > [!NOTE]
        > Lokalizacja wyszukiwania może być inny niż lokalizacja witryny sieci Web.

    * Wybierz, czy chcesz włączyć **usługi Application Insights** czy nie. Jeśli **usługi Application Insights** jest włączona, narzędzie QnA Maker gromadzi dane telemetryczne w ruchu, dzienniki czatu i błędów.
    * Wybierz **Lokalizacja usługi App insights** wdrożonym zasób usługi Application Insights.

5. Po zweryfikowaniu wszystkich pól, możesz kliknąć **Utwórz** można uruchomić wdrożenia tych usług w Twojej subskrypcji. Potrwa kilka minut.

6.  Po zakończeniu wdrożenia zostanie wyświetlony następujące zasoby utworzone w ramach subskrypcji.

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)