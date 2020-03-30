---
title: Tworzenie konta indeksatora wideo połączonego z platformą Azure
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto indeksatora wideo połączone z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499920"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Tworzenie konta indeksatora wideo połączonego z platformą Azure

Podczas tworzenia konta indeksatora wideo możesz wybrać bezpłatne konto próbne (gdzie otrzymujesz określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w której nie jesteś ograniczony przez przydział). Dzięki bezpłatnej wersji próbnej indeksator wideo zapewnia użytkownikom witryny do 600 minut bezpłatnego indeksowania i do 2400 minut bezpłatnego indeksowania dla użytkowników interfejsu API. Za pomocą opcji płatnej można utworzyć konto indeksatora wideo, które jest połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services. Płacisz za minuty indeksowane, a także opłaty związane z kontem multimedialnym.

W tym artykule pokazano, jak utworzyć konto indeksatora wideo, które jest połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services. W temacie przedstawiono kroki dotyczące łączenia się z platformą Azure przy użyciu przepływu automatycznego (domyślnego). Pokazano również, jak połączyć się z platformą Azure ręcznie (zaawansowane).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

    Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się w bezpłatnej [wersji próbnej platformy Azure.](https://azure.microsoft.com/free/)

* Domena usługi Azure Active Directory (Azure AD).

    Jeśli nie masz domeny usługi Azure AD, utwórz tę domenę za pomocą subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen w usłudze Azure AD](../../active-directory/users-groups-roles/domains-manage.md)

* Użytkownik w domenie usługi Azure AD z rolą **administratora aplikacji.** Użyjesz tego członka podczas łączenia konta indeksatora wideo z platformą Azure.

    Ten użytkownik powinien być użytkownikiem usługi Azure AD z kontem służbowym. Nie używaj konta osobistego, takiego jak outlook.com, live.com lub hotmail.com.

    ![wszyscy użytkownicy AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Dodatkowe wymagania wstępne dla przepływu automatycznego

* Użytkownik i członek domeny usługi Azure AD.

    Użyjesz tego członka podczas łączenia konta indeksatora wideo z platformą Azure.

    Ten użytkownik powinien być członkiem subskrypcji platformy Azure z rolą **właściciela** lub rolami **administratora** dostępu współautora i **administratora dostępu użytkownika.** Użytkownik można dodać dwa razy, z dwóch ról. Raz z współautorem i raz z administratorem dostępu użytkownika.

    ![kontrola dostępu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Dodatkowe wymagania wstępne dotyczące przepływu ręcznego

* Zarejestruj dostawcę zasobów EventGrid przy użyciu portalu Azure.

    W [witrynie Azure portal](https://portal.azure.com/)przejdź do **sekcji Subskrypcje**->[subskrypcja]->**ResourceProviders**.

    Wyszukaj **witrynę Microsoft.Media** i **Microsoft.EventGrid**. Jeśli nie w stanie "Zarejestrowany", kliknij przycisk **Zarejestruj**. Rejestracja zajmuje kilka minut.

    ![EventGrid ( EventGrid )](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

> [!NOTE]
> Jeśli subskrypcja platformy Azure korzysta z uwierzytelniania wieloskładnikowego opartego na certyfikatach, ważne jest wykonanie następujących kroków na urządzeniu z zainstalowanymi wymaganymi certyfikatami.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.

2. Wybierz przycisk **Utwórz nowe konto:**

    ![Tworzenie nowego konta indeksatora wideo](./media/create-account/connect-to-azure.png)

3. Po wyświetleniu listy subskrypcji wybierz subskrypcję, której chcesz użyć.

    ![Łączenie indeksatora wideo z platformą Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Wybierz region platformy Azure z obsługiwanych lokalizacji: Zachodnie stany USA 2, Europa Północna lub Azja Wschodnia.
5. W obszarze **Konto usługi Azure Media Services**wybierz jedną z następujących opcji:

    * Aby utworzyć nowe konto programu Media Services, wybierz pozycję **Utwórz nową grupę zasobów**. Podaj nazwę grupy zasobów.

        Platforma Azure utworzy nowe konto w ramach subskrypcji, w tym nowe konto usługi Azure Storage. Nowe konto usługi Media Services ma domyślną konfigurację początkową z punktem końcowym przesyłania strumieniowego i 10 jednostkami zarezerwowanymi S3.
    * Aby użyć istniejącego konta usługi Media Services, wybierz pozycję **Użyj istniejącego zasobu**. Z listy kont wybierz swoje konto.

        Twoje konto usługi Media Services musi mieć ten sam region co konto indeksatora wideo.

        > [!NOTE]
        > Aby zminimalizować czas trwania indeksowania i niską przepływność, zaleca się dostosowanie typu i liczby [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) na koncie usługi Media Services do **10 jednostek zarezerwowanych S3**. Zobacz [Zmienianie jednostek zarezerwowanych za pomocą portalu.](../previous/media-services-portal-scale-media-processing.md)

    * Aby ręcznie skonfigurować połączenie, wybierz łącze **Przełącz na konfigurację ręczną.**

        Aby uzyskać szczegółowe informacje, zobacz [poniżej sekcja Połącz z platformą Azure ręcznie](#connect-to-azure-manually-advanced-option) (opcja zaawansowana).
6. Po zakończeniu wybierz pozycję **Połącz**. Ta operacja może potrwać do kilku minut.

    Po nawiązaniu połączenia z platformą Azure na liście kont pojawi się nowe konto indeksatora wideo:

    ![nowe konto](./media/create-account/new-account.png)

7. Przejdź do nowego konta.

## <a name="connect-to-azure-manually-advanced-option"></a>Ręczne łączenie się z platformą Azure (opcja zaawansowana)

Jeśli połączenie z platformą Azure nie powiodło się, można spróbować rozwiązać problem, łącząc się ręcznie.

> [!NOTE]
> Zdecydowanie zaleca się, aby mieć następujące trzy konta w tym samym regionie: konto indeksatora wideo, które łączysz się z kontem usługi Media Services, a także konto usługi Azure storage połączone z tym samym kontem usługi Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Tworzenie i konfigurowanie konta usługi Media Services

1. Użyj [witryny Azure](https://portal.azure.com/) Portal, aby utworzyć konto usługi Azure Media Services, zgodnie z [opisem](../previous/media-services-portal-create-account.md)w obszarze Tworzenie konta .

    Podczas tworzenia konta magazynu dla konta usługi Media Services wybierz **opcję StorageV2** dla rodzaju konta i **Geo-redundant (GRS)** dla pól replikacji.

    ![Nowe konto AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Pamiętaj, aby zapisać nazwy zasobów i kont usługi Media Services. Będą potrzebne do czynności kroków w następnej sekcji.

2. Dostosuj typ i liczbę [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) do **10 jednostek zarezerwowanych S3** na utworzonym koncie usługi Media Services. Zobacz [Zmienianie jednostek zarezerwowanych za pomocą portalu.](../previous/media-services-portal-scale-media-processing.md)
3. Aby można było odtwarzać klipy wideo w aplikacji internetowej indeksatora wideo, należy uruchomić domyślny **punkt końcowy przesyłania strumieniowego** nowego konta usługi Media Services.

    Na nowym koncie usługi Media Services wybierz pozycję **Punkty końcowe przesyłania strumieniowego**. Następnie wybierz punkt końcowy przesyłania strumieniowego i naciśnij przycisk start.

    ![Nowe konto AMS](./media/create-account/create-ams-account2.png)

4. Aby indeksator wideo uwierzytelnił się za pomocą interfejsu API usługi Media Services, należy utworzyć aplikację USŁUGI AD. Poniższe kroki prowadzą użytkownika przez proces uwierzytelniania usługi Azure AD opisany w obszarze [Wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu portalu Azure:](../previous/media-services-portal-get-started-with-aad.md)

    1. Na nowym koncie usługi Media Services wybierz pozycję **Dostęp do interfejsu API**.
    2. Wybierz [opcję Metoda uwierzytelniania jednostkowego usługi](../previous/media-services-portal-get-started-with-aad.md).
    3. Uzyskaj identyfikator klienta i klucz tajny klienta

        Po **wybraniu**->pozycji**Klawisze**ustawień dodaj **opis**, naciśnij **klawisz Zapisz**, a wartość klucza zostanie wypełniona.

        Jeśli klucz wygaśnie, właściciel konta będzie musiał skontaktować się z pomocą techniczną indeksatora wideo, aby odnowić klucz.

        > [!NOTE]
        > Pamiętaj, aby zapisać wartość klucza i identyfikator aplikacji. Będzie potrzebny do kroków w następnej sekcji.

### <a name="connect-manually"></a>Łączenie ręczne

W oknie **dialogowym Połącz indeksatora wideo z subskrypcją platformy Azure** na stronie [Indeksator wideo](https://www.videoindexer.ai/) wybierz łącze **Przełącz do konfiguracji ręcznej.**

W oknie dialogowym podaj następujące informacje:

|Ustawienie|Opis|
|---|---|
|Region konta indeksatora wideo|Nazwa regionu konta indeksatora wideo. Aby uzyskać lepszą wydajność i niższe koszty, zdecydowanie zaleca się określenie nazwy regionu, w którym znajdują się zasoby usługi Azure Media Services i konto usługi Azure Storage. |
|Dzierżawa usługi Azure AD|Nazwa dzierżawy usługi Azure AD, na przykład "contoso.onmicrosoft.com". Informacje o dzierżawie można pobrać z witryny Azure portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu. Znajdź nazwę po prawej stronie **domeny**.|
|Identyfikator subskrypcji|Subskrypcja platformy Azure, w ramach której należy utworzyć to połączenie. Identyfikator subskrypcji można pobrać z witryny Azure portal. Wybierz **pozycję Wszystkie usługi** w lewym panelu i wyszukaj hasło "subskrypcje". Wybierz **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
|Nazwa grupy zasobów usługi Azure Media Services|Nazwa grupy zasobów, w której utworzono konto usługi Media Services.|
|Nazwa zasobu usługi multimediów|Nazwa konta usługi Azure Media Services utworzonego w poprzedniej sekcji.|
|Identyfikator aplikacji|Identyfikator aplikacji usługi Azure AD (z uprawnieniami dla określonego konta usługi Media Services), który został utworzony w poprzedniej sekcji.|
|Klucz aplikacji|Klucz aplikacji usługi Azure AD utworzony w poprzedniej sekcji. |

## <a name="considerations"></a>Zagadnienia do rozważenia

Zastosowanie mają następujące zagadnienia związane z usługą Azure Media Services:

* Jeśli łączysz się automatycznie, w subskrypcji platformy Azure zostanie wyświetlona nowa grupa zasobów, konto usługi Media Services i konto usługi Storage.
* Jeśli połączysz się automatycznie, indeksator wideo ustawi multimedia **Zarezerwowane jednostki** na 10 jednostek S3:

    ![Jednostki zarezerwowane usługi Media Services](./media/create-account/ams-reserved-units.png)

* Jeśli połączysz się z istniejącym kontem usługi Media Services, indeksator wideo nie zmieni istniejącej konfiguracji **jednostek zarezerwowanych nośników.**

   Może być konieczne dostosowanie typu i liczby jednostek zarezerwowanych nośnika zgodnie z planowanym obciążeniem. Pamiętaj, że jeśli obciążenie jest wysokie i nie masz wystarczającej liczby jednostek lub szybkości, przetwarzanie wideo może spowodować błędy limitu czasu.

* Jeśli połączysz się z nowym kontem usługi Media Services, indeksator wideo automatycznie uruchomi domyślny **punkt końcowy przesyłania strumieniowego** w nim:

    ![Punkt końcowy przesyłania strumieniowego usług Media Services](./media/create-account/ams-streaming-endpoint.png)

    Punkty końcowe przesyłania strumieniowego mają znaczny czas uruchamiania. W związku z tym może upłynąć kilka minut od czasu połączenia konta z platformą Azure, aż twoje filmy mogą być przesyłane strumieniowo i oglądane w aplikacji sieci web indeksatora wideo.

* Jeśli połączysz się z istniejącym kontem usługi Media Services, indeksator wideo nie zmieni domyślnej konfiguracji punktu końcowego przesyłania strumieniowego. Jeśli nie ma uruchomionego **punktu końcowego przesyłania strumieniowego,** nie można oglądać klipów wideo z tego konta usługi Media Services ani z indeksatora wideo.

## <a name="next-steps"></a>Następne kroki

Możesz programowo wchodzić w interakcje z kontem próbnym i/lub z kontami indeksatora wideo połączonymi z platformą Azure, postępując zgodnie z instrukcjami w: [Użyj interfejsów API](video-indexer-use-apis.md).

Należy użyć tego samego użytkownika usługi Azure AD, który był używany podczas łączenia się z platformą Azure.