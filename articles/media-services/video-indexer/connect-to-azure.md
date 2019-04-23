---
title: Utwórz konto usługi Video Indexer w witrynie Azure portal
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak utworzyć konto usługi Video Indexer w witrynie Azure portal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: affa6f9a808543401b7d57812c7d2bef4324a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796549"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Utwórz konto usługi Video Indexer połączony z platformą Azure

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku płatnych opcji tworzenia konta Video Indexer, który jest połączony z subskrypcją platformy Azure i konto usługi Azure Media Services. Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Media. 

W tym artykule pokazano, jak utworzyć konto usługi Video Indexer, który jest połączony z subskrypcją platformy Azure i konto usługi Azure Media Services. Temat zawiera instrukcje łączenie z platformą Azure przy użyciu usługi flow automatyczne (opcja domyślna). Pokazano również, jak połączyć się z platformy Azure ręcznie (zaawansowane).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

    Jeśli nie masz jeszcze subskrypcji platformy Azure, należy zasubskrybować [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/).

* Domeny usługi Azure Active Directory (AD).

    Jeśli nie masz domenę usługi Azure AD, Utwórz tej domeny z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Użytkownik i element członkowski w domenie usługi Azure AD. Użyjesz tego elementu członkowskiego, łącząc swoje konto usługi Video Indexer na platformie Azure.

    Ten użytkownik powinien być użytkownika usługi Azure AD przy użyciu służbowego konta służbowego, nie konta osobistego, takich jak outlook.com, live.com lub hotmail.com.

    ![Wszyscy użytkownicy usługi AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Dodatkowe wymagania wstępne dotyczące automatycznego przepływu

Użytkownik i element członkowski w domenie usługi Azure AD. Użyjesz tego elementu członkowskiego, łącząc swoje konto usługi Video Indexer na platformie Azure.

Ten użytkownik powinien być element członkowski w Twojej subskrypcji platformy Azure albo **właściciela** roli i / lub **Współautor** i **Administrator dostępu użytkowników** ról. Użytkownika można dodać dwa razy, za pomocą 2 role. Raz przy użyciu Współautor, a drugi raz z Administrator dostępu użytkowników.

![Kontrola dostępu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Dodatkowe wymagania wstępne dotyczące ręcznego przepływu

Zarejestruj dostawcę zasobów EventGrid przy użyciu witryny Azure portal.

W [witryny Azure portal](https://portal.azure.com/), przejdź do **subskrypcje**-> [subskrypcji] ->**ResourceProviders**. 

Wyszukaj **Microsoft.Media** i **Microsoft.EventGrid**. Jeśli nie jest w stanie "Zarejestrowane", kliknij przycisk **zarejestrować**. Może potrwać kilka minut, aby zarejestrować.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.

2. Kliknij pozycję **Utwórz nowe konto** przycisku:

    ![Łączenie z platformą Azure](./media/create-account/connect-to-azure.png)

3. Po wyświetleniu listy subskrypcji Wybierz subskrypcję, której chcesz użyć.

    ![Łączenie indeksatora wideo z platformą Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Wybierz region platformy Azure obsługiwane lokalizacje: Zachodnie stany USA 2, Europa Północna lub Azja Wschodnia.
5. W obszarze **konta usługi Azure Media Services**, wybierz jedną z następujących opcji:

    * Aby utworzyć nowe konto usługi Media Services, wybierz **Utwórz nową grupę zasobów**. Podaj nazwę grupy zasobów.

        Azure spowoduje utworzenie nowego konta w ramach subskrypcji, w tym nowe konto usługi Azure Storage. Nowe konto usługi Media Services zawiera domyślną konfigurację początkową z punktu końcowego przesyłania strumieniowego i 10 jednostki zarezerwowane S3.
    * Aby użyć istniejącego konta usługi Media Services, wybierz **Użyj istniejącego zasobu**. Z listy kont wybierz swoje konto.

        Konto usługi Media Services musi mieć w tym samym regionie co konto usługi Video Indexer. 

        > [!NOTE]
        > Aby zminimalizować czas trwania indeksowania i niska przepływność, zaleca dostosowanie typu i liczby [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) do **10 jednostki zarezerwowane S3** na Twoim koncie usługi Media Services. Zobacz [w obsłudze portalu, aby zmienić liczbę jednostek zarezerwowanych](../previous/media-services-portal-scale-media-processing.md).

    * Aby ręcznie skonfigurować połączenie, kliknij przycisk **przejdź do Konfiguracja ręczna** łącza.

        Aby uzyskać szczegółowe informacje, zobacz [łączenie z platformą Azure ręcznie](#connect-to-azure-manually-advanced-option) Poniższa sekcja (zaawansowana opcja).
6. Gdy wszystko będzie gotowe, wybierz pozycję **Connect**. Ta operacja może potrwać kilka minut. 

    Po nawiązaniu na platformie Azure, Twoje nowe konto usługi Video Indexer pojawia się na liście kont:

    ![nowe konto](./media/create-account/new-account.png)

7. Przejdź do nowego konta

## <a name="connect-to-azure-manually-advanced-option"></a>Łączenie z platformą Azure ręcznie (opcja zaawansowana)

Jeśli połączenie z platformą Azure nie powiodła się, można spróbować rozwiązać problem, nawiązując połączenie ręcznie.

> [!NOTE]
> Zdecydowanie zaleca się następujące trzy konta w tym samym regionie: konta indeksatora wideo, na którym jest nawiązywane przy użyciu konta usługi Media Services, a także konta magazynu platformy Azure podłączone do tego samego konta usługi Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Tworzenie i konfigurowanie konta usługi Media Services

1. Użyj [Azure](https://portal.azure.com/) portalu, aby utworzyć konto usługi Azure Media Services, zgodnie z opisem w [Tworzenie konta usługi](../previous/media-services-portal-create-account.md).

    Podczas tworzenia konta magazynu dla konta usługi Media Services, wybierz **StorageV2** dla rodzaju konta i **geograficznie nadmiarowy (RGS)** dla pól replikacji.

    ![nowe konto usługi AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Koniecznie Zanotuj nazwy zasobów i konta usługi Media Services. Należy go na potrzeby procedury w następnej sekcji.

2. Dostosowanie typu i liczby [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) do **10 jednostki zarezerwowane S3** w utworzone konto usługi Media Services. Zobacz [w obsłudze portalu, aby zmienić liczbę jednostek zarezerwowanych](../previous/media-services-portal-scale-media-processing.md).
3. Zanim będzie można odtwarzać filmów wideo w aplikacji sieci web indeksatora wideo, należy uruchomić domyślną **punkt końcowy przesyłania strumieniowego** nowego konta usługi Media Services.

    Nowe konto usługi Media Services, kliknij **punkty końcowe przesyłania strumieniowego**. Wybierz punkt końcowy przesyłania strumieniowego i naciśnij przycisk start.

    ![nowe konto usługi AMS](./media/create-account/create-ams-account2.png)

4. Video Indexer można uwierzytelnić przy użyciu interfejsu API usługi Media Services aplikację usługi AD musi zostać utworzona. Następującymi wskazówkami kroki podczas procesu uwierzytelniania usługi Azure AD opisanego w [wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu witryny Azure portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Nowe konto usługi Media Services, wybierz **dostęp do interfejsu API**.
    2. Wybierz [metody uwierzytelniania jednostki usługi](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Pobierz identyfikator klienta oraz klucz tajny klienta, zgodnie z opisem w [uzyskania Identyfikatora klienta oraz klucz tajny klienta](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) sekcji.

        Po wybraniu **ustawienia**->**klucze**, Dodaj **opis**, naciśnij klawisz **Zapisz**, wartość klucza jest wypełniana.

        Jeśli klucz wygaśnie właściciel konta musi się z pomocą techniczną indeksatora wideo, aby odnowić klucz.

        > [!NOTE]
        > Upewnij się, że Zapisz wartość klucza i identyfikator aplikacji. Należy go na potrzeby procedury w następnej sekcji.

### <a name="connect-manually"></a>Połącz ręcznie

W **połączyć indeksatora wideo z subskrypcją platformy Azure** okna dialogowego z Twojej [Video Indexer](https://www.videoindexer.ai/) wybierz opcję **przejdź do Konfiguracja ręczna** łącza.

W oknie dialogowym podaj następujące informacje:

|Ustawienie|Opis|
|---|---|
|Region konta usługi Video Indexer|Nazwa regionu konta Video Indexer. Aby uzyskać lepszą wydajność i obniżyć koszty zaleca do określenia nazwy regionu, w którym znajdują się zasobów usługi Azure Media Services i konto usługi Azure Storage. |
|Dzierżawa usługi Azure Active Directory (AAD)|Nazwa dzierżawy usługi Azure AD, na przykład "contoso.onmicrosoft.com". W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor na nazwie użytkownika zalogowanego w prawym górnym rogu. Znajdowanie nazwy po prawej stronie **domeny**.|
|Identyfikator subskrypcji|Subskrypcja platformy Azure, w którym można utworzyć tego połączenia. Identyfikator subskrypcji można pobrać z witryny Azure portal. Kliknij pozycję **wszystkich usług** w panelu po lewej stronie i wyszukaj "subskrypcje". Wybierz **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
|Nazwa grupy zasobów usługi Azure Media Services|Nazwa grupy zasobów, w którym utworzono konto usługi Media Services.|
|Nazwa zasobu usługi multimediów|Nazwa konta usługi Azure Media Services, który został utworzony w poprzedniej sekcji.|
|Identyfikator aplikacji|Usługa Azure AD identyfikator aplikacji (z uprawnieniami określonego konta usługi Media Services) utworzonego w poprzedniej sekcji.|
|Klucz aplikacji|Klucz aplikacji usługi Azure AD, który został utworzony w poprzedniej sekcji. |

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia pokrewne usługi Azure Media Services:

* Jeśli łączysz się automatycznie, zobaczysz nową grupę zasobów, konto usługi Media Services i konto magazynu w ramach subskrypcji platformy Azure.
* Jeśli łączysz się automatycznie, Video Indexer ustawia nośnika **jednostek zarezerwowanych** do 10 jednostek S3:

    ![Zastrzeżone jednostki usługi Media Services](./media/create-account/ams-reserved-units.png)

* Jeśli łączysz się do istniejącego konta usługi Media Services, Video Indexer nie powoduje zmiany istniejących nośników **jednostek zarezerwowanych** konfiguracji.

   Może być konieczne dostosowanie typu i liczby jednostek zarezerwowanych multimediów, zgodnie z planowanych obciążenia. Należy pamiętać, że obciążenia jest duże i nie masz wystarczającej liczby jednostek lub szybkość, filmy wideo, przetwarzanie może spowodować błędy przekroczenia limitu czasu.

* Jeśli łączysz się do nowego konta usługi Media Services, indeksatora wideo automatycznie rozpoczyna się domyślnie **punkt końcowy przesyłania strumieniowego** w nim:

    ![Punkt końcowy przesyłania strumieniowego usługi Media Services](./media/create-account/ams-streaming-endpoint.png)

    Punkty końcowe przesyłania strumieniowego nie znaczne uruchamiania. W związku z tym może upłynąć kilka minut od czasu, połączone konta na platformie Azure, dopóki filmy wideo mogą być przesyłane strumieniowo i obserwowane w aplikacji sieci web Video Indexer.

* Jeśli łączysz się do istniejącego konta usługi Media Services, Video Indexer nie zmienia domyślna konfiguracja punktu końcowego przesyłania strumieniowego. Jeśli jest uruchomiony nie **punkt końcowy przesyłania strumieniowego**, nie będzie można oglądać filmy wideo za pomocą tego konta usługi Media Services lub Video Indexer.

## <a name="next-steps"></a>Kolejne kroki

Mogą programowo współdziałać z konta wersji próbnej i/lub z kontami usługi Video Indexer, które są podłączone do platformy azure, postępując zgodnie z instrukcjami wyświetlanymi w: [Korzystanie z interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, używane podczas nawiązywania połączenia z platformą Azure.


