---
title: Konfigurowanie usługi Maker — strona główna - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak skonfigurować usługę Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348664"
---
# <a name="create-a-qna-maker-service"></a>Tworzenie usługi Maker — strona główna

Przed utworzeniem żadnych baz wiedzy Maker — strona główna musi najpierw skonfigurować Maker — strona główna usługa na platformie Azure. Każda osoba mająca autoryzacji do utworzenia nowych zasobów w subskrypcji można skonfigurować usługi Maker — strona główna.

Ten Instalator wdraża kilku zasobów platformy Azure. Razem te zasoby zarządzania zawartością wiedzy i zapewniają możliwości udzielenie odpowiedzi na pytanie, że punkt końcowy.

1. Zaloguj się do witryny [Azure Portal](<https://portal.azure.com>).

2.  Polecenie **Dodaj nowy zasób**, wpisz "maker — strona główna" w wyszukiwaniu i wybierz zasób Maker — strona główna

    ![Utwórz nową usługę Maker — strona główna](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Polecenie **Utwórz** po przeczytaniu warunków i postanowień.

    ![Utwórz nową usługę Maker — strona główna](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. W **Maker — strona główna**, wybierz odpowiednie warstwy i regionów.

    ![Utwórz nową usługę Maker — strona główna](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Wypełnij **nazwa** o unikatowej nazwie do identyfikacji usługi Maker — strona główna. Ta nazwa identyfikuje również punkt końcowy Maker — strona główna, z którą zostanie skojarzona z bazy wiedzy.
    * Wybierz **subskrypcji** , w którym zostanie wdrożony zasobów Maker — strona główna.
    * Wybierz **zarządzania warstwy cenowej** dla usług zarządzania Maker — strona główna (portal i interfejsów API zarządzania). Zobacz [tutaj](https://aka.ms/qnamaker-pricing) szczegółowe informacje o cenach jednostki SKU.
    * Utwórz nową **grupy zasobów** (zalecane) lub użyj istniejącej, w której chcesz wdrożyć ten zasób Maker — strona główna.
    * Wybierz **wyszukiwania warstwy cenowej** usługi Azure Search. Jeśli widzisz opcję warstwę bezpłatna nieaktywna, oznacza to, że masz już warstwę bezpłatna usługi Azure Search wdrożone w ramach subskrypcji. W takim przypadku należy uruchomić z warstwy podstawowa usługi Azure Search. Zobacz szczegóły cennika usługi Azure search [tutaj](https://azure.microsoft.com/en-us/pricing/details/search/).
    * Wybierz **lokalizację wyszukiwania** miejscu danych usługi Azure Search do wdrożenia. Ograniczenia, w którym dane klienta musi być przechowywany poinformuje lokalizacji wybrany dla usługi Azure Search.
    * Nadaj nazwę usługi aplikacji w **Nazwa aplikacji**.
    * Domyślnie usługi App service domyślnie warstwy standardowa (S1). Plan można zmienić po jego utworzeniu. Dowiedz się więcej o App service — ceny [tutaj](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Wybierz **lokalizacji witryny sieci Web** wdrożonym usługi App Service.

        > [!NOTE]
        > Lokalizację wyszukiwania może różnić się od lokalizacji witryny sieci Web.

    * Wybierz, czy chcesz włączyć **usługi Application Insights** lub nie. Jeśli **usługi Application Insights** jest włączona, Maker — strona główna zbiera dane telemetryczne w ruchu, dzienniki rozmów i błędów.
    * Wybierz **App insights lokalizacji** wdrożonym zasobu usługi Application Insights.

5. Po zweryfikowaniu wszystkich pól możesz kliknąć **Utwórz** rozpocząć wdrażanie tych usług w Twojej subskrypcji. Potrwa kilka minut.

6.  Po zakończeniu wdrażania zostanie zobacz następujące zasoby utworzone w ramach subskrypcji.

    ![Utwórz nową usługę Maker — strona główna](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)