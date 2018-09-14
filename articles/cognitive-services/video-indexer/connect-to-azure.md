---
title: Utwórz konto usługi Video Indexer połączony z platformą Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak utworzyć konto usługi Video Indexer połączony z platformą Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 229624f4070c931b6ad892533aad269af49c738b
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544197"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Utwórz konto usługi Video Indexer połączony z platformą Azure

Podczas tworzenia konta indeksatora wideo, możesz wybrać bezpłatne konto próbne (skąd określonej liczbie bezpłatnych minut indeksowania) lub opcję płatną (gdzie możesz nie są ograniczone według przydziału). Za pomocą bezpłatnej wersji próbnej Video Indexer zapewnia do 600 minut indeksować bezpłatne dla użytkowników witryny sieci Web, i maksymalnie 2 400 minut bezpłatne indeksowania dla użytkowników interfejsu API. W przypadku płatnych opcji tworzenia konta Video Indexer, który jest połączony z subskrypcją platformy Azure i konto usługi Azure Media Services. Płacisz indeksowane w ciągu kilku minut, a także konta multimediów powiązane opłaty. 

W tym artykule pokazano, jak utworzyć konto usługi Video Indexer, który jest połączony z subskrypcją platformy Azure i konto usługi Azure Media Services. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. 

    Jeśli nie masz jeszcze subskrypcji platformy Azure, należy zasubskrybować [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/).

* Domeny usługi Azure Active Directory (AD). 

    Jeśli nie masz domenę usługi Azure AD, Utwórz tej domeny z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Użytkownik i element członkowski w domenie usługi Azure AD. Użyjesz tego elementu członkowskiego, łącząc swoje konto usługi Video Indexer na platformie Azure.

    Ten użytkownik powinien spełniać następujące kryteria:

    * Należy być użytkownikiem usługi Azure AD przy użyciu służbowego konta służbowego, nie konta osobistego, takich jak outlook.com, live.com lub hotmail.com.
        
        ![Wszyscy użytkownicy usługi AAD](./media/create-account/all-aad-users.png)

    *  Być członkiem roli właściciela lub współautora i Administrator dostępu użytkowników ról Twojej subskrypcji platformy Azure. Użytkownika można dodać dwa razy, za pomocą 2 role. Raz przy użyciu Współautor, a drugi raz z Administrator dostępu użytkowników.

        ![Kontrola dostępu](./media/create-account/access-control-iam.png)

* Zarejestruj dostawcę zasobów EventGrid przy użyciu witryny Azure portal.

    W [witryny Azure portal](https://portal.azure.com/), przejdź do **subskrypcje** > [subskrypcja] > **ResourceProviders** > **Microsoft.EventGrid**. Jeśli nie jest w stanie "Zarejestrowane", kliknij przycisk **zarejestrować**. Może potrwać kilka minut, aby zarejestrować. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

1. Przejdź do [Video Indexer](https://www.videoindexer.ai/) witryny sieci Web i zaloguj się.

2. Kliknij pozycję **Połącz z platformą Azure** przycisku:

    ![Łączenie z platformą Azure](./media/create-account/connect-to-azure.png)

3. Po wyświetleniu listy subskrypcji Wybierz subskrypcję, której chcesz użyć. 

    ![Łączenie indeksatora wideo z platformą Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Wybierz region platformy Azure z obsługiwanych lokalizacji: zachodnie stany USA 2, Europa Północna lub Azja Wschodnia.
5. W obszarze **konta usługi Azure Media Services**, wybierz jedną z następujących opcji:

    * Aby utworzyć nowe konto usługi Media Services, wybierz **Utwórz nową grupę zasobów**. Podaj nazwę grupy zasobów.

        Azure spowoduje utworzenie nowego konta w ramach subskrypcji, w tym nowe konto usługi Azure Storage. Nowe konto usługi Media Services zawiera domyślną konfigurację początkową z punktu końcowego przesyłania strumieniowego i 10 jednostki zarezerwowane S3.
    * Aby użyć istniejącego konta usługi Media Services, wybierz **Użyj istniejącego zasobu**. Z listy kont wybierz swoje konto.

        Konto usługi Media Services musi mieć w tym samym regionie co konto usługi Video Indexer. Aby zminimalizować czas trwania indeksowania i niska przepływność, dostosować typ i liczbę jednostek zarezerwowanych do **10 jednostki zarezerwowane S3** na Twoim koncie usługi Media Services.
    * Aby ręcznie skonfigurować połączenie, kliknij przycisk **przejdź do Konfiguracja ręczna**. 
    
        Można ręcznie skonfigurować połączenie, jeśli z jakiegoś powodu opcji automatycznego nie powiedzie się, czy Twoja instalacja i konfiguracja różni się od typowe przypadki lub chcesz mieć pełną widoczność i kontrolę nad ustawieniami. 
        
        W **połączyć indeksatora wideo z subskrypcją platformy Azure**, podaj następujące informacje.

        |Ustawienie|Opis|
        |---|---|
        |Region konta usługi wideo indeksatora|Nazwa regionu konta Video Indexer. Aby uzyskać lepszą wydajność i obniżyć koszty zaleca do określenia nazwy regionu, w którym znajdują się zasobów usługi Azure Media Services i konto usługi Azure Storage. |
        |Dzierżawa usługi Azure Active Directory (AAD)|Nazwa dzierżawy usługi Azure AD, na przykład "contoso.onmicrosoft.com". W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor na nazwie zalogowanego użytkownika w prawym górnym rogu.|
        |Identyfikator subskrypcji|Subskrypcja platformy Azure, w którym można utworzyć tego połączenia. Identyfikator subskrypcji można pobrać z witryny Azure portal. Kliknij pozycję **wszystkich usług** w panelu po lewej stronie i wyszukaj "subskrypcje". Wybierz, **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
        |Nazwa grupy zasobów w usłudze Azure Media Services|Nazwa grupy zasobów, w którym do konta usługi Media Services istnieje.|
        |Nazwa zasobu usługi multimediów|Nazwa zasobu usługi Azure Media Services.|
        |Identyfikator aplikacji|Identyfikator aplikacji usługi Azure AD z uprawnieniami określonego konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie jednostki usługi użyj](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Klucz aplikacji|Aby uzyskać więcej informacji, zobacz [uwierzytelnianie jednostki usługi użyj](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. Gdy wszystko będzie gotowe, wybierz pozycję **Connect**. Ta operacja może potrwać kilka minut. 

    Po nawiązaniu na platformie Azure, Twoje nowe konto usługi Video Indexer pojawia się na liście kont:

    ![nowe konto](./media/create-account/new-account.png)

7. Przejdź do nowego konta: 

    ![Wideo konta indeksatora](./media/create-account/vi-account.png)

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia pokrewne usługi Azure Media Services:

* Jeśli łączysz się do nowego konta usługi Media Services, zobaczysz nową grupę zasobów, konto usługi Media Services i konto magazynu w ramach subskrypcji platformy Azure.
* Jeśli łączysz się do nowego konta usługi Media Services Video Indexer ustawi nośnika **jednostek zarezerwowanych** do 10 jednostek S3:

    ![Zastrzeżone jednostki usługi Media Services](./media/create-account/ams-reserved-units.png)

* Jeśli łączysz się do istniejącego konta usługi Media Services Video Indexer nie powoduje zmiany istniejących nośników **jednostek zarezerwowanych** konfiguracji.

    Może być konieczne dostosowanie typu i nośniki **jednostek zarezerwowanych**, zgodnie z planowanych obciążenia. Należy pamiętać, że obciążenia jest duże i nie masz wystarczającej liczby jednostek lub szybkość, filmy wideo, przetwarzanie może spowodować błędy przekroczenia limitu czasu.

* Jeśli łączysz się do nowego konta usługi Media Services indeksatora wideo automatycznie rozpoczyna się domyślnie **punkt końcowy przesyłania strumieniowego** w nim:

    ![Punkt końcowy przesyłania strumieniowego usługi Media Services](./media/create-account/ams-streaming-endpoint.png)

* Jeśli łączysz się do istniejącego konta usługi Media Services Video Indexer nie zmienia domyślna konfiguracja punktu końcowego przesyłania strumieniowego. Jeśli jest uruchomiony nie **punkt końcowy przesyłania strumieniowego**, nie będzie można oglądać filmy wideo za pomocą tego konta usługi Media Services lub Video Indexer.

## <a name="next-steps"></a>Kolejne kroki

Mogą programowo współdziałać z konta wersji próbnej i/lub z kontami usługi Video Indexer, które są podłączone do platformy azure, postępując zgodnie z instrukcjami wyświetlanymi w: [użycia interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, używane podczas nawiązywania połączenia z platformą Azure.


