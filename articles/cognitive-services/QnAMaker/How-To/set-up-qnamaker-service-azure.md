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
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195322"
---
# <a name="manage-qna-maker-resources"></a>Zarządzanie zasobami QnA Maker

Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Typy kluczy w QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy **klucze subskrypcji** i **klucze punktu końcowego**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Cel|
|--|--|--|
|Klucz subskrypcji|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Te klucze są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **Cognitive Services** , na stronie **klucze** .|
|Klucz punktu końcowego|[Portal QnA Maker](http://www.qnamaker.ai)|Te klucze są używane w celu uzyskania dostępu do opublikowanego punktu końcowego bazy wiedzy w celu uzyskania odpowiedzi na pytanie użytkownika. Ten punkt końcowy jest zazwyczaj używany w programie Chat bot lub kodzie aplikacji klienta, który nawiązuje połączenie z usługą QnA Maker. Te klucze są tworzone podczas publikowania bazy wiedzy QnA Maker.<br><br>Znajdź te klucze na stronie **Ustawienia usługi** . Znajdź Tę stronę z menu najwyższego poziomu, odpowiedniego użytkownika jako wyboru na liście rozwijanej.|

## <a name="create-a-new-qna-maker-service"></a>Utwórz nową usługę QnA Maker

Ta procedura służy do tworzenia zasobów platformy Azure wymaganych do zarządzania zawartością bazy wiedzy. Po wykonaniu tych kroków można znaleźć klucze _subskrypcji_ na stronie **klucze** dla zasobu w Azure Portal.

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
    * Aby uzyskać środki oszczędnościowe, możesz [udostępnić](#share-existing-services-with-qna-maker) kilka zasobów platformy Azure utworzonych dla QNA Maker. 

1. Po sprawdzeniu poprawności wszystkich pól wybierz pozycję **Utwórz**. Ukończenie tego procesu może potrwać kilka minut.

1. Po zakończeniu wdrożenia zostanie wyświetlony następujące zasoby utworzone w ramach subskrypcji.

    ![Nowa usługa QnA Maker utworzony zasób](../media/qnamaker-how-to-setup-service/resources-created.png)

    Zasób z typem _Cognitive Services_ ma Twoje klucze _subskrypcji_ .

## <a name="find-subscription-keys-in-azure-portal"></a>Znajdź klucze subskrypcji w Azure Portal

Można wyświetlać i resetować klucze subskrypcji, co umożliwia edycję z Azure Portal, w którym został utworzony zasób QnA Maker. 

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz zasób z typem _Cognitive Services_.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Znajdź klucze punktów końcowych w portalu QnA Maker

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie kliknij pozycję **Ustawienia usługi**.

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Umożliwia wyświetlenie lub zresetować klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Jeśli uważasz, że zostały naruszone, należy odświeżyć klucze. Może to wymagać odpowiednich zmian w aplikacji klienckiej lub kodzie bot.

## <a name="share-existing-services-with-qna-maker"></a>Udostępnianie istniejących usług za pomocą QnA Maker

QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z zalet udostępniania kosztów, Skorzystaj z poniższej tabeli, aby dowiedzieć się, co można i czego nie można udostępnić:

|Usługa|Udostępnij|Reason|
|--|--|--|
|Cognitive Services|X|Niemożliwa przez projekt|
|Plan usługi aplikacji|✔|Stałe miejsce na dysku przydzielono dla planu usługi App Service. W przypadku innych aplikacji, które współdzielą ten sam plan App Service, należy użyć znacznego miejsca na dysku, QnAMaker App Service spowoduje problemy.|
|App Service|X|Niemożliwa przez projekt|
|Application Insights|✔|Mogą być udostępniane|
|Usługa wyszukiwania|✔|1. `testkb` jest zarezerwowaną nazwą usługi QnAMaker, nie może być używana przez inne osoby.<br>2. Mapa synonimów o nazwie `synonym-map` jest zarezerwowana dla usługi QnAMaker.<br>3. Liczba opublikowanych artykułów bazy wiedzy jest ograniczona przez warstwę usługi wyszukiwania. Jeśli dostępne są wolne indeksy, można z nich korzystać inne usługi.|

### <a name="using-a-single-search-service"></a>Korzystanie z pojedynczej usługi wyszukiwania

W przypadku tworzenia usługi QnA i jej zależności (takich jak wyszukiwanie) w portalu usługa wyszukiwania zostanie utworzona dla Ciebie i powiązana z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie usługi App Service, aby korzystało z istniejącej usługi wyszukiwania i usunąć właśnie utworzoną usługę wyszukiwania.

Jeśli utworzysz usługę QnA za pomocą szablonów Azure Resource Manager, możesz utworzyć wszystkie zasoby i kontrolować Tworzenie usługi App Service, aby używać istniejącej usługi wyszukiwania. 

## <a name="upgrade-qna-maker"></a>QnA Maker uaktualniania

|Uaktualnienie|Reason|
|--|--|
|[Uaktualnij](#upgrade-qna-maker-sku) Jednostka SKU zarządzania QnA Maker|Musisz mieć więcej pytań i odpowiedzi w bazie wiedzy.|
|[Uaktualnij](#upgrade-app-service) Jednostka SKU usługi App Service|Baza wiedzy musi obtworzyć więcej żądań z aplikacji klienckiej, takich jak rozmowa bot.|
|[Uaktualnij](#upgrade-azure-search-service) Usługa Azure Search|Planowane jest posiadanie wielu baz wiedzy.|


### <a name="upgrade-qna-maker-sku"></a>Uaktualnij QnA Maker jednostki SKU

Jeśli potrzebujesz więcej pytań i odpowiedzi w bazie wiedzy, poza bieżącą warstwą, Uaktualnij warstwę cenową usługi QnA Maker Service. 

Aby uaktualnić jednostkę SKU zarządzania QnA Maker:

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz pozycję **warstwa cenowa**.

    ![Zasób QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Wybierz odpowiednią jednostkę SKU i naciśnij **pozycję Wybierz**.

    ![Cennik QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Uaktualnij usługę App Service

 Gdy baza wiedzy musi obpracować więcej żądań z aplikacji klienckiej, należy uaktualnić warstwę cenową usługi App Service.

Usługę App Service można [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół.

Przejdź do zasobu usługi App Service w Azure Portal i wybierz pozycję **Skaluj w górę** lub **w dół** odpowiednio do potrzeb.

![Skalowanie usługi App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Uaktualnij usługę Azure Search

Planując posiadanie wielu baz wiedzy, należy uaktualnić warstwę cenową usługi Azure Search. 

Obecnie nie jest możliwe przeprowadzenie uaktualnienia w miejscu jednostki SKU usługi Azure Search. Można jednak utworzyć nowy zasób usługi Azure Search z odpowiednią jednostką SKU, przywrócić dane do nowego zasobu, a następnie połączyć je z stosem QnA Maker.

1. Utwórz nowy zasób usługi Azure Search w Azure Portal i wybierz żądaną jednostkę SKU.

    ![QnA Maker zasobów usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Przywróć indeksy z oryginalnego zasobu usługi Azure Search do nowego. Zapoznaj się z przykładowym [](https://github.com/pchoudhari/QnAMakerBackupRestore)kodem przywracania kopii zapasowej.

1. Po przywróceniu danych przejdź do nowego zasobu usługi Azure Search, wybierz pozycję **klucze**i zanotuj **nazwę** i **klucz administratora**.

    ![QnA Maker kluczy usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Aby połączyć nowy zasób usługi Azure Search ze stosem QnA Maker, przejdź do usługi QnA Maker App Service.

    ![QnA Maker appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Wybierz pozycję **Ustawienia aplikacji** i Zastąp pola **AzureSearchName** i **AzureSearchAdminKey** w kroku 3.

    ![QnA Maker ustawienie appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Uruchom ponownie usługi App service.

    ![QnA Maker ponowne uruchomienie appService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Pobierz najnowsze aktualizacje środowiska uruchomieniowego

Środowisko uruchomieniowe QnAMaker jest częścią Azure App Service wdrożone podczas [tworzenia usługi QnAMaker](./set-up-qnamaker-service-azure.md) w programie Azure Portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. QnA Maker App Service jest w trybie aktualizacji z aktualizacją Update 2019 (wersja 5 +). Ta opcja została już zaprojektowana, aby zadbać o ZERO przestoju podczas uaktualniania. 

Bieżącą wersję można sprawdzić pod adresem https://www.qnamaker.ai/UserSettings. Jeśli wersja jest starsza niż wersja 5. x, należy ponownie uruchomić App Service, aby zastosować najnowsze aktualizacje.

1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Kliknij w usłudze App Service, a następnie otwórz sekcję — Omówienie

     ![Usługa qna Maker przechowuje wszelką App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Uruchom ponownie usługi App service. Powinno się zakończyć w ciągu kilku sekund. Wszystkie aplikacje zależne lub botów korzystające z tej usługi QnAMaker będą niedostępne dla użytkowników końcowych w trakcie tego okresu.

    ![Ponowne uruchomienie usługi App Service interfejsu QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Region usługi zarządzania

Usługa zarządzania QnA Maker, używana tylko w celu QnA Maker & do wstępnego przetwarzania danych, jest dostępna tylko w regionie zachodnie stany USA. W tym regionie zachodnie stany USA nie są przechowywane żadne dane klienta.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)
