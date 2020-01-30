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
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dc3bb6882963205e17e37f52ec9dcdffecdf9e21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843198"
---
# <a name="manage-qna-maker-resources"></a>Zarządzanie zasobami QnA Maker

Przed utworzeniem żadnych baz wiedzy usługi QnA Maker, należy najpierw skonfigurować usługa QnA Maker na platformie Azure. Każda osoba mająca autoryzacji do tworzenia nowych zasobów w ramach subskrypcji można skonfigurować usługę QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Typy kluczy w QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy: **klucze subskrypcji** i **klucze punktów końcowych**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

|Nazwa|Lokalizacja|Przeznaczenie|
|--|--|--|
|Klucz subskrypcji|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Te klucze są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **Cognitive Services** na stronie **klucze** .|
|Klucz punktu końcowego|[Portal QnA Maker](https://www.qnamaker.ai)|Te klucze są używane w celu uzyskania dostępu do opublikowanego punktu końcowego bazy wiedzy w celu uzyskania odpowiedzi na pytanie użytkownika. Ten punkt końcowy jest zazwyczaj używany w bot rozmowy lub w kodzie aplikacji klienta, który nawiązuje połączenie z usługą QnA Maker. Te klucze są tworzone podczas publikowania bazy wiedzy QnA Maker.<br><br>Te klucze znajdują się na stronie **ustawień usługi** . Znajdź Tę stronę w menu użytkownika w prawym górnym rogu strony z menu rozwijanego.|

## <a name="create-a-new-qna-maker-service"></a>Utwórz nową usługę QnA Maker

Ta procedura służy do tworzenia zasobów platformy Azure wymaganych do zarządzania zawartością bazy wiedzy. Po wykonaniu tych kroków znajdują się klucze _subskrypcji_ na stronie **klucze** dla zasobu w Azure Portal.

1. Zaloguj się do Azure Portal i [Utwórz zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Wybierz pozycję **Utwórz** po przeczytaniu warunków i postanowień:

    ![Utwórz nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. W **QNA Maker**wybierz odpowiednie warstwy i regiony:

    ![Tworzenie nowej usługi QnA Maker — warstwa cenowa i regiony](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * W polu **Nazwa** wprowadź unikatową nazwę identyfikującą tę usługę QNA Maker. Ta nazwa określa również punkt końcowy QnA Maker, z którym będą skojarzone bazy wiedzy.
    * Wybierz **subskrypcję** , w ramach której zostanie wdrożony zasób QNA Maker.
    * Wybierz **warstwę cenową** dla usług zarządzania QNA Maker (Portal i interfejsy API zarządzania). Zobacz [więcej szczegółowych informacji o cenach jednostki SKU](https://aka.ms/qnamaker-pricing).
    * Utwórz nową **grupę zasobów** (zalecane) lub Użyj istniejącej grupy, w której ma zostać wdrożony ten zasób QNA Maker. QnA Maker tworzy kilka zasobów platformy Azure. Podczas tworzenia grupy zasobów do przechowywania tych zasobów można łatwo znajdować i usuwać te zasoby oraz nimi zarządzać według nazwy grupy zasobów.
    * Wybierz **lokalizację grupy zasobów**.
    * Wybierz **warstwę cenową wyszukiwania** usługi Azure wyszukiwanie poznawcze. Jeśli opcja warstwy Bezpłatna jest niedostępna (jest wyszarzona), oznacza to, że masz już bezpłatną usługę wdrożoną w ramach subskrypcji. W takim przypadku należy zacząć od warstwy Podstawowa. Zobacz [szczegóły cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).
    * Wybierz **lokalizację wyszukiwania** , w której mają zostać wdrożone indeksy wyszukiwanie poznawcze platformy Azure. Ograniczenia dotyczące lokalizacji, w której dane klienta muszą być przechowywane, ułatwiają określenie wybranej opcji Wyszukiwanie poznawcze platformy Azure.
    * W polu **Nazwa aplikacji** wprowadź nazwę wystąpienia Azure App Service.
    * Domyślnie App Service domyślnie warstwą Standard (S1). Plan można zmienić po utworzeniu. Dowiedz się więcej o [cenach App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Wybierz **lokalizację witryny sieci Web** , w której zostanie wdrożona App Service.

        > [!NOTE]
        > **Lokalizacja wyszukiwania** może się różnić od **lokalizacji witryny sieci Web**.

    * Zdecyduj, czy chcesz włączyć **Application Insights**. Jeśli **usługi Application Insights** jest włączona, narzędzie QnA Maker gromadzi dane telemetryczne w ruchu, dzienniki czatu i błędów.
    * Wybierz **lokalizację usługi App Insights** , w której zostanie wdrożony zasób Application Insights.
    * Aby uzyskać środki oszczędnościowe, możesz [udostępnić](#share-existing-services-with-qna-maker) kilka zasobów platformy Azure utworzonych dla QNA Maker.

1. Po sprawdzeniu poprawności wszystkich pól wybierz pozycję **Utwórz**. Proces może potrwać kilka minut.

1. Po zakończeniu wdrażania zostaną wyświetlone następujące zasoby utworzone w ramach subskrypcji:

   ![Nowa usługa QnA Maker utworzony zasób](../media/qnamaker-how-to-setup-service/resources-created.png)

    Zasób z typem _Cognitive Services_ ma Twoje klucze _subskrypcji_ .

## <a name="find-subscription-keys-in-the-azure-portal"></a>Znajdź klucze subskrypcji w Azure Portal

Klucze subskrypcji można wyświetlić i zresetować z poziomu Azure Portal, w którym został utworzony zasób QnA Maker.

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz zasób, który ma _Cognitive Services_ typ:

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klawiszy**:

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Znajdź klucze punktów końcowych w portalu QnA Maker

Punkt końcowy znajduje się w tym samym regionie, w którym znajduje się zasób, ponieważ klucze punktów końcowych są używane do wywołania bazy wiedzy.

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie wybierz pozycję **Ustawienia usługi**:

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Wyświetl lub zresetuj klucze:

    ![Menedżer kluczy punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Odśwież klucze, jeśli uważasz, że zostały naruszone. Może to wymagać odpowiednich zmian w aplikacji klienckiej lub kodzie bot.

## <a name="share-existing-services-with-qna-maker"></a>Udostępnianie istniejących usług za pomocą QnA Maker

QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z zalet udostępniania kosztów, Skorzystaj z poniższej tabeli, aby zrozumieć, co można i czego nie można udostępnić:

|Usługa|Udostępnianie|Przyczyna|
|--|--|--|
|Usługi Cognitive Services|X|Niemożliwa przez projekt|
|Plan usługi App Service|✔|Stałe miejsce na dysku przydzielono dla planu App Service. Jeśli inne aplikacje, które współużytkują ten sam plan App Service, użyją znacznego miejsca na dysku, wystąpienie App Service QnAMaker napotka problemy.|
|App Service|X|Niemożliwa przez projekt|
|Application Insights|✔|Mogą być udostępniane|
|Search Service|✔|1. `testkb` jest zarezerwowaną nazwą usługi QnAMaker. nie mogą być używane przez inne osoby.<br>2. Mapa synonimów o nazwie `synonym-map` jest zarezerwowana dla usługi QnAMaker.<br>3. liczba opublikowanych baz wiedzy jest ograniczona przez warstwę usługi wyszukiwania. Jeśli dostępne są wolne indeksy, można z nich korzystać inne usługi.|

Dowiedz się więcej na temat [usługi App Service](../../../app-service/index.yml) i [usługi wyszukiwania](../../../search/index.yml).

## <a name="using-a-single-search-service"></a>Korzystanie z pojedynczej usługi wyszukiwania

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) w portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie App Service tak, aby korzystało już z istniejącej usługi wyszukiwania, i usunąć właśnie utworzoną.

Jeśli utworzysz usługę QnA za pomocą szablonów Azure Resource Manager, możesz utworzyć wszystkie zasoby i kontrolować tworzenie App Service, aby użyć istniejącej usługi wyszukiwania.


## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurowanie QnA Maker do używania różnych zasobów Wyszukiwanie poznawcze

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) w portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie App Service tak, aby korzystało już z istniejącej usługi wyszukiwania, i usunąć właśnie utworzoną.

Zasób **App Service** QNA Maker używa zasobu wyszukiwanie poznawcze. Aby zmienić zasób Wyszukiwanie poznawcze używany przez QnA Maker, należy zmienić ustawienie w Azure Portal.

1. Pobierz **klucz administratora** i **nazwę** zasobu Wyszukiwanie poznawcze, którego chcesz QNA Maker użyć.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź **App Service** skojarzone z Twoim zasobem QNA Maker. Oba z mają tę samą nazwę.

1. Wybierz opcję **Ustawienia**, a następnie **Konfiguracja**. Spowoduje to wyświetlenie wszystkich istniejących ustawień App Service QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający Azure Portal pokazujący App Service ustawienia konfiguracji](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Zmień wartości następujących kluczy:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Aby użyć nowych ustawień, należy ponownie uruchomić usługę App Service. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Uruchom ponownie**.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu Azure Portal ponownego uruchomienia App Service po zmianie ustawień konfiguracji](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Jeśli utworzysz usługę QnA za pomocą szablonów Azure Resource Manager, możesz utworzyć wszystkie zasoby i kontrolować tworzenie App Service, aby użyć istniejącej usługi wyszukiwania.

## <a name="upgrade-qna-maker"></a>QnA Maker uaktualniania

|Uaktualnienie|Przyczyna|
|--|--|
|[Uaktualnij](#upgrade-qna-maker-sku) Jednostka SKU zarządzania QnA Maker|Chcesz uzyskać więcej pytań i odpowiedzi w bazie wiedzy.|
|[Uaktualnij](#upgrade-app-service) Jednostka SKU App Service|Baza wiedzy musi obtworzyć więcej żądań z aplikacji klienckiej, takich jak rozmowa bot.|
|[Uaktualnij](#upgrade-the-azure-cognitive-search-service) Usługa Wyszukiwanie poznawcze platformy Azure|Planowane jest posiadanie wielu baz wiedzy.|


### <a name="upgrade-qna-maker-sku"></a>Uaktualnij QnA Maker jednostki SKU

Jeśli chcesz uzyskać więcej pytań i odpowiedzi w bazie wiedzy, poza bieżącą warstwą, Uaktualnij warstwę cenową usługi QnA Maker Service.

Aby uaktualnić jednostkę SKU zarządzania QnA Maker:

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz pozycję **warstwa cenowa**.

    ![Zasób QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Wybierz odpowiednią jednostkę SKU i naciśnij **pozycję Wybierz**.

    ![Cennik QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App Service uaktualniania

 Gdy baza wiedzy musi obpracować więcej żądań z aplikacji klienckiej, należy uaktualnić warstwę cenową App Service.

App Service można [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w poziomie.

Przejdź do zasobu App Service w Azure Portal i wybierz opcję **Skaluj w górę** lub w **poziomie** w zależności od potrzeb.

![QnA Maker skalowanie App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Uaktualnianie usługi Azure Wyszukiwanie poznawcze

Jeśli planujesz posiadanie wielu baz wiedzy, Uaktualnij warstwę cenową usługi Azure Wyszukiwanie poznawcze Service.

Obecnie nie można przeprowadzić uaktualnienia w miejscu jednostki SKU usługi Azure Search. Można jednak utworzyć nowy zasób usługi Azure Search z odpowiednią jednostką SKU, przywrócić dane do nowego zasobu, a następnie połączyć je z stosem QnA Maker. W tym celu wykonaj następujące kroki:

1. Utwórz nowy zasób usługi Azure Search w Azure Portal i wybierz żądaną jednostkę SKU.

    ![QnA Maker zasobów usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Przywróć indeksy z oryginalnego zasobu usługi Azure Search do nowego. Zapoznaj się z [przykładowym kodem przywracania kopii zapasowej](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Po przywróceniu danych przejdź do nowego zasobu usługi Azure Search, wybierz pozycję **klucze**, a następnie wpisz **nazwę** i **klucz administratora**:

    ![QnA Maker kluczy usługi Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Aby połączyć nowy zasób usługi Azure Search ze stosem QnA Maker, przejdź do wystąpienia App Service QnA Maker.

    ![Wystąpienie App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Wybierz pozycję **Ustawienia aplikacji** i zmodyfikuj ustawienia w polach **AzureSearchName** i **AzureSearchAdminKey** w kroku 3.

    ![Ustawienie App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Uruchom ponownie wystąpienie App Service.

    ![Uruchom ponownie wystąpienie App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Pobierz najnowsze aktualizacje środowiska uruchomieniowego

Środowisko uruchomieniowe QnAMaker jest częścią wystąpienia Azure App Service, które jest wdrażane podczas [tworzenia usługi QnAMaker](./set-up-qnamaker-service-azure.md) w Azure Portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. Wystąpienie App Service QnA Maker jest w trybie aktualizacji z kwietnia 2019 (wersja 5 +). Ta aktualizacja została zaprojektowana tak, aby zadbać o brak przestojów podczas uaktualniania.

Bieżącą wersję można sprawdzić pod adresem https://www.qnamaker.ai/UserSettings. Jeśli wersja jest starsza niż wersja 5. x, należy ponownie uruchomić App Service, aby zastosować najnowsze aktualizacje:

1. Przejdź do usługi QnAMaker (Grupa zasobów) w [Azure Portal](https://portal.azure.com).

    ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz wystąpienie App Service i Otwórz sekcję **Przegląd** .

    ![Wystąpienie App Service QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Uruchom ponownie App Service. Proces aktualizacji powinien zakończyć się w ciągu kilku sekund. Wszystkie aplikacje zależne lub botów korzystające z tej usługi QnAMaker będą niedostępne dla użytkowników końcowych w trakcie tego okresu.

    ![Uruchom ponownie wystąpienie App Service QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Region usługi zarządzania

Usługa zarządzania QnA Maker jest używana tylko dla portalu QnA Maker i do wstępnego przetwarzania danych. Ta usługa jest dostępna tylko w regionie zachodnie stany USA. W tym regionie zachodnie stany USA nie są przechowywane żadne dane klienta.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [usługi App Service](../../../app-service/index.yml) i [usługi wyszukiwania](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)
