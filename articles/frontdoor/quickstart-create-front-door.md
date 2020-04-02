---
title: 'Szybki start: tworzenie profilu drzwi przednich w celu zapewnienia wysokiej dostępności aplikacji'
description: W tym artykule Szybki start opisano, jak utworzyć usługę Front Door na potrzeby globalnej aplikacji internetowej o wysokiej dostępności i wydajności.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521447"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Szybki start: tworzenie usługi Front Door na potrzeby globalnej aplikacji internetowej o wysokiej dostępności

W tym artykule Szybki start opisano, jak utworzyć profil usługi Front Door, który umożliwia uzyskanie wysokiej dostępności i wydajności globalnej aplikacji internetowej. 

Scenariusz opisany w tym przewodniku Szybki start uwzględnia dwa wystąpienia aplikacji internetowej działające w różnych regionach platformy Azure. Zostanie utworzona konfiguracja usługi Front Door oparta na [zapleczach o równym obciążeniu i takim samym priorytecie](front-door-routing-methods.md), która pomaga kierować ruch użytkowników do najbliższego zestawu zapleczy witryny obsługujących aplikację. Usługa Front Door stale monitoruje aplikację internetową i zapewnia automatyczne włączenie trybu failover w kolejnym dostępnym zapleczu, gdy najbliższa witryna jest niedostępna.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki Start wymaga wdrożenia dwóch wystąpień aplikacji internetowej działających w różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Oba wystąpienia aplikacji internetowej działają w trybie aktywny/aktywny, czyli każde z nich może przyjąć ruch w dowolnym momencie, w przeciwieństwie do konfiguracji aktywny/rezerwa, w której jedno z nich jest przeznaczone na potrzeby trybu failover.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **aplikacji Web** > **App**.
2. W obszarze **Aplikacja internetowa** wprowadź lub wybierz poniższe informacje oraz wprowadź domyślne ustawienia tam, gdzie ustawienia nie są określone:

     | Ustawienie         | Wartość     |
     | ---              | ---  |
     | Grupa zasobów          | Wybierz pozycję **Nowa**, a następnie wpisz ciąg *myResourceGroupFD1* |
     | Nazwa           | Wprowadź unikatową nazwę aplikacji internetowej  |
     | Stos środowiska uruchomieniowego          | Wybieranie stosu środowiska uruchomieniowego dla aplikacji |
     |      Region  |   Zachodnie stany USA        |
     | Plan usługi App Service/lokalizacja         | Wybierz **pozycję Nowy**.  W obszarze Plan usługi App Service wprowadź *myAppServicePlanEastUS*, a następnie wybierz pozycję **OK**.| 
     |Jednostka SKU i rozmiar  | Wybierz **zmień rozmiar** Wybierz standard **S1 100 całkowita ACU, 1,75 GB pamięci** |
     
3. Wybierz **pozycję Recenzja + Utwórz**.
4. Przejrzyj informacje o podsumowaniu aplikacji sieci Web. Wybierz **pozycję Utwórz**.
5. Po około 5 minutach domyślna witryna sieci Web jest tworzona po pomyślnym wdrożeniu aplikacji sieci Web.
6. Powtórz kroki 1–3, aby utworzyć drugą witrynę internetową w innym regionie platformy Azure z następującymi ustawieniami:

     | Ustawienie         | Wartość     |
     | ---              | ---  |
     | Grupa zasobów          | Wybierz pozycję **Nowa**, a następnie wpisz ciąg*myResourceGroupFD2* |
     | Nazwa           | Wprowadź unikatową nazwę aplikacji internetowej  |
     | Stos środowiska uruchomieniowego          | Wybieranie stosu środowiska uruchomieniowego dla aplikacji |
     |      Region  |   Europa Zachodnia      |
     | Plan usługi App Service/lokalizacja         | Wybierz **pozycję Nowy**.  W obszarze Plan usługi App Service wprowadź *myAppServicePlanWestEurope*, a następnie wybierz pozycję **OK**.|   
     |Jednostka SKU i rozmiar  | Wybierz **zmień rozmiar** Wybierz standard **S1 100 całkowita ACU, 1,75 GB pamięci** |
    
## <a name="create-a-front-door-for-your-application"></a>Tworzenie usługi Front Door dla aplikacji
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Dodawanie hosta frontonu na potrzeby usługi Front Door
Utwórz konfigurację usługi Front Door, która będzie kierować ruch użytkowników na podstawie najniższego opóźnienia pomiędzy dwoma zapleczami.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > Drzwi**frontowe****sieci .** > 
2. W **obszarze Tworzenie drzwi przednich**wprowadź lub wybierz następujące informacje i wprowadź ustawienia domyślne, w których nie określono:

     | Ustawienie         | Wartość     |
     | ---              | ---  |
     |Subskrypcja  | Wybierz subskrypcję, w której mają być tworzone drzwiami frontowymi.|
     | Grupa zasobów          | Wybierz **pozycję Nowy**, a następnie wpisz *myResourceGroupFD0* |
     | Lokalizacja grupy zasobów  |   Środkowe stany USA        |
     
     > [!NOTE]
     > Nie trzeba tworzyć nowej grupy zasobów, aby wdrożyć drzwi frontowe.  Jeśli można również wybrać istniejącą grupę zasobów.
     
3. Kliknij **przycisk Dalej: Konfiguracja**.
4. Kliknij ikonę "+" na karcie Frontends/domains.  Dla nazwy `<Your Initials>frontend` **hosta** wprowadź . Ta nazwa hosta musi być unikatowa globalnie, drzwi frontowe zajmą się sprawdzaniem poprawności.
5. Kliknij przycisk **Dodaj**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Dodawanie zaplecza aplikacji i pul zaplecza

Następnie należy skonfigurować interfejsy/domeny i zaplecza aplikacji w puli wewnętrznej bazy danych dla drzwi frontowych, aby wiedzieć, gdzie znajduje się aplikacja. 

1. Kliknij ikonę "+" na karcie Backend pools, aby dodać pulę `myBackendPool`wewnętrznej bazy danych dla **nazwy** puli wewnętrznej bazy danych, wprowadź .
2. Następnie kliknij **pozycję Dodaj zaplecze,** aby dodać witryny utworzone wcześniej.
3. Wybierz **typ hosta wewnętrznej bazy danych** jako "Usługa aplikacji", wybierz subskrypcję, w której utworzono witrynę sieci web, a następnie wybierz pierwszą witrynę sieci Web z listy rozwijanej Nazwa **hosta wewnętrznej bazy danych.**
4. W tym momencie pozostaw pozostałe pola bez zmian i kliknij polecenie **Dodaj**.
5. Wybierz **typ hosta wewnętrznej bazy danych** jako "Usługa aplikacji", wybierz subskrypcję, w której utworzono witrynę sieci web, a następnie wybierz **drugą** witrynę sieci Web z listy rozwijanej **Nazwa hosta wewnętrznej bazy danych.**
6. W tym momencie pozostaw pozostałe pola bez zmian i kliknij polecenie **Dodaj**.
7. Opcjonalnie można zaktualizować sondy kondycji i równoważenia obciążenia ustawienia dla puli wewnętrznej bazy danych, ale wartości domyślne powinny również działać. W obu przypadkach kliknij przycisk **Dodaj**.


### <a name="c-add-a-routing-rule"></a>C. Dodawanie reguły rozsyłania
1. Na koniec kliknij ikonę "+" na karcie Reguł routingu, aby skonfigurować regułę routingu. Jest to niezbędne w celu mapowania hosta frontonu do puli zaplecza — mówiąc najprościej, ta konfiguracja określa, że jeśli żądanie przychodzi na adres `myappfrontend.azurefd.net`, to powinno zostać przekazane do puli zaplecza `myBackendPool`. 
2. Dla **name** wprowadź "LocationRule".
3. Kliknij przycisk **Dodaj**, aby dodać regułę rozsyłania dla usługi Front Door. 
4. Kliknij **przejrzyj i utwórz**.
5. Przejrzyj ustawienia tworzenia drzwi frontowych. Kliknij **przycisk Utwórz**

>[!WARNING]
> **Musisz** upewnić się, że każdy z hostów frontonu w usłudze Front Door ma regułę rozsyłania ze skojarzoną ścieżką domyślną („/\*”). Oznacza to, że wśród wszystkich reguł rozsyłania musi być co najmniej jedna reguła rozsyłania dla każdego z hostów frontonu zdefiniowanych w ścieżce domyślnej („/\*”). Jeśli tak nie będzie, ruch użytkowników końcowych może nie być kierowany prawidłowo.

## <a name="view-front-door-in-action"></a>Usługa Front Door w działaniu
Po utworzeniu usługi Front Door globalne wdrożenie konfiguracji może zająć kilka minut. Gdy proces ten dobiegnie końca, uzyskaj dostęp do utworzonego hosta frontonu — czyli przejdź do adresu URL `myappfrontend.azurefd.net` w przeglądarce internetowej. Twoje żądanie zostanie automatycznie skierowane do najbliższego zaplecza spośród określonych zapleczy w puli zapleczy. 

### <a name="view-front-door-handle-application-failover"></a>Obsługa trybu failover dla aplikacji w usłudze Front Door
Jeśli chcesz przetestować natychmiastowe globalne działanie trybu failover usługi Front Door, przejdź do jednej z utworzonych witryn internetowych i zatrzymaj ją. Na podstawie ustawienia Sonda kondycji zdefiniowanego dla puli zaplecza ruch zostanie natychmiast skierowany do innego wdrożenia witryny internetowej, skonfigurowanego dla trybu failover. Możesz również przetestować działanie tej funkcji, wyłączając zaplecze w konfiguracji puli zaplecza dla usługi Front Door. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebne, usuń grupy zasobów **myResourceGroupFD1**, **myResourceGroupFD2**i **myResourceGroupFD0:**

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start została utworzona usługa Front Door, która pozwala kierować ruch użytkowników do aplikacji internetowych wymagających wysokiej dostępności i maksymalnej wydajności. Aby dowiedzieć się więcej na temat kierowania ruchu, zapoznaj się z [metodami rozsyłania](front-door-routing-methods.md) używanymi przez usługę Front Door.
