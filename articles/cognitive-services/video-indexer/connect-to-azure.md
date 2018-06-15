---
title: Utwórz konto indeksatora wideo podłączone do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak utworzyć konto indeksatora wideo podłączone do platformy Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: ac9093d41a2e70905ea82c6d11f020696488ff27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349861"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Utwórz konto indeksatora wideo podłączone do platformy Azure

Korzystając z indeksatora wideo bezpłatnego konta wersji próbnej, ograniczeniem przydziału i liczby plików wideo, które można indeksować. W tym artykule przedstawiono sposób tworzenia konta indeksatora wideo, która jest połączona z subskrypcją platformy Azure, co zwalnia od tych limitów i używa cennika z.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. 

    Jeśli nie masz jeszcze subskrypcji platformy Azure, należy zarejestrować się w celu [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/).

* Domena usługi Azure Active Directory (AD). 

    Jeśli nie masz domeny usługi Azure AD, należy utworzyć tej domeny z subskrypcją platformy Azure.

* Użytkownik i element członkowski w domenie usługi Azure AD. Ten element członkowski będzie używany podczas łączenia z kontem indeksatora wideo na platformie Azure.

    Ten użytkownik powinna spełniać następujące kryteria:

    * Być użytkownikiem usługi Azure AD z firmowego konta służbowego, nie osobistego konta, takich jak outlook.com, live.com lub usługi.
        
        ![Wszyscy użytkownicy usługi AAD](./media/create-account/all-aad-users.png)

    *  Jest elementem członkowskim w Twojej subskrypcji platformy Azure z rolę właściciela lub role zarówno współautora i Administrator dostępu użytkowników.

        ![Kontrola dostępu](./media/create-account/access-control-iam.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

1. Zaloguj się przy użyciu tego użytkownika, a następnie kliknij polecenie **Connect Azure** przycisk:

    ![Połączenia z platformą Azure](./media/create-account/connect-to-azure.png)

2. Gdy pojawi się na liście subskrypcji, wybierz subskrypcję, której chcesz użyć. 

    ![Indeksator wideo połączenia z platformą Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Wybierz region platformy Azure z obsługiwanych lokalizacji: zachodnie stany USA 2, Europa Północna, Europa lub Azja Wschodnia.
4. W obszarze **konta usługi Azure Media Services**, wybierz jedną z następujących opcji:

    * Aby utworzyć nowe konto usługi Media Services, wybierz **Utwórz nową grupę zasobów**. Podaj nazwę grupy zasobów.

        Azure spowoduje utworzenie nowego konta w ramach subskrypcji, w tym nowe konto magazynu Azure. Nowe konto usługi Media Services ma domyślnej konfiguracji początkowej punktu końcowego przesyłania strumieniowego i 10 jednostek zarezerwowanego S3.
    * Aby użyć istniejącego konta usługi Media Services, wybierz opcję **używać istniejącego zasobu**. Wybierz konto z listy kont.

        Konta usługi Media Services musi mieć tego samego regionu co konto indeksatora wideo. Aby zminimalizować czas trwania indeksowania i niskiej przepustowości, typ i dostosować liczbę jednostek zarezerwowanych do **10 jednostek zarezerwowanego S3** na koncie usługi Media Services.
    * Kliknij, aby ręcznie skonfigurować połączenie **przełączyć się do konfiguracji ręcznej** połączyć, a następnie wprowadź niezbędne informacje:

    ![Indeksator wideo połączenia z platformą Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Gdy wszystko będzie gotowe, wybierz pozycję **Connect**. Ta operacja może potrwać kilka minut. 

    Po nawiązaniu połączenia na platformie Azure nowego konta indeksatora wideo pojawia się na liście kont:

    ![nowe konto](./media/create-account/new-account.png)

6. Przejdź do nowego konta: 

    ![Wideo konta indeksatora](./media/create-account/vi-account.png)

## <a name="considerations"></a>Zagadnienia do rozważenia

Zastosuj następujące zagadnienia pokrewne usługi Azure Media Services:

* Jeśli komputer jest podłączony do nowego konta usługi Media Services, zobaczysz nową grupę zasobów, konto usługi Media Services oraz konta magazynu w ramach subskrypcji platformy Azure.
* Jeśli komputer jest podłączony do nowego konta usługi Media Services, indeksatora wideo ustawi nośnika **jednostki zarezerwowanego** do 10 jednostek S3:

    ![Zastrzeżone jednostki usługi Media Services](./media/create-account/ams-reserved-units.png)

* Jeśli komputer jest podłączony do istniejącego konta usługi Media Services, wideo indeksatora nie powoduje zmiany istniejących nośników **jednostki zarezerwowanego** konfiguracji.

    Może być konieczne dostosowanie o rodzaju i liczbie nośnika **jednostki zarezerwowanego**, zgodnie z planowanych obciążenia. Należy pamiętać, że obciążenie jest wysoka, nie ma wystarczającej liczby jednostek lub szybkość przetwarzania plików wideo może spowodować błędy przekroczenia limitu czasu.

* Jeśli komputer jest podłączony do nowego konta usługi Media Services, indeksatora wideo jest automatycznie uruchamiana **punktu końcowego przesyłania strumieniowego** w niej:

    ![Punkt końcowy przesyłania strumieniowego usługi Media Services](./media/create-account/ams-streaming-endpoint.png)

* Jeśli komputer jest podłączony do istniejącego konta usługi Media Services, wideo indeksatora nie zmienia konfiguracji punktów końcowych przesyłania strumieniowego. Jeśli nie istnieje żadne uruchomione **punktu końcowego przesyłania strumieniowego**, nie można obejrzeć filmy wideo z tego konta usługi Media Services lub indeksator wideo.

## <a name="use-video-indexer-apis-v2"></a>Użyj wideo indeksatora interfejsów API w wersji 2

Pozwala na interakcję programowo przy użyciu konta wersji próbnej i/lub konta indeksatora wideo podłączonymi do platformy azure zgodnie z instrukcjami w: [Użyj interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, używane podczas nawiązywania połączenia platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

[Przejrzyj szczegóły danych wyjściowych JSON](video-indexer-output-json-v2.md).

