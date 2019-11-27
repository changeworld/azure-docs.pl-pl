---
title: 'Szybki Start: Tworzenie profilu frontonu w celu zapewnienia wysokiej dostępności aplikacji'
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
ms.openlocfilehash: 67a4f9eb3290ba09a2c19325464cf7ad224856e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184513"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Szybki start: tworzenie usługi Front Door na potrzeby globalnej aplikacji internetowej o wysokiej dostępności

W tym artykule Szybki start opisano, jak utworzyć profil usługi Front Door, który umożliwia uzyskanie wysokiej dostępności i wydajności globalnej aplikacji internetowej. 

Scenariusz opisany w tym przewodniku Szybki start uwzględnia dwa wystąpienia aplikacji internetowej działające w różnych regionach platformy Azure. Zostanie utworzona konfiguracja usługi Front Door oparta na [zapleczach o równym obciążeniu i takim samym priorytecie](front-door-routing-methods.md), która pomaga kierować ruch użytkowników do najbliższego zestawu zapleczy witryny obsługujących aplikację. Usługa Front Door stale monitoruje aplikację internetową i zapewnia automatyczne włączenie trybu failover w kolejnym dostępnym zapleczu, gdy najbliższa witryna jest niedostępna.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki Start wymaga wdrożenia dwóch wystąpień aplikacji internetowej działających w różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Oba wystąpienia aplikacji internetowej działają w trybie aktywny/aktywny, czyli każde z nich może przyjąć ruch w dowolnym momencie, w przeciwieństwie do konfiguracji aktywny/rezerwa, w której jedno z nich jest przeznaczone na potrzeby trybu failover.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć Web** > **Aplikacja internetowa** > **Utwórz**.
2. W obszarze **Aplikacja internetowa** wprowadź lub wybierz poniższe informacje oraz wprowadź domyślne ustawienia tam, gdzie ustawienia nie są określone:

     | Ustawienie         | Wartość     |
     | ---              | ---  |
     | Nazwa           | Wprowadź unikatową nazwę aplikacji internetowej  |
     | Grupa zasobów          | Wybierz pozycję **Nowa**, a następnie wpisz ciąg *myResourceGroupFD1* |
     | Plan usługi App Service/lokalizacja         | Wybierz pozycję **Nowy**.  W obszarze Plan usługi App Service wprowadź *myAppServicePlanEastUS*, a następnie wybierz pozycję **OK**. 
     |      Location  |   Wschodnie stany USA        |
    |||

3. Wybierz pozycję **Utwórz**.
4. Po pomyślnym wdrożeniu aplikacji internetowej zostanie utworzona domyślna witryna internetowa.
5. Powtórz kroki 1–3, aby utworzyć drugą witrynę internetową w innym regionie platformy Azure z następującymi ustawieniami:

     | Ustawienie         | Wartość     |
     | ---              | ---  |
     | Nazwa           | Wprowadź unikatową nazwę aplikacji internetowej  |
     | Grupa zasobów          | Wybierz pozycję **Nowa**, a następnie wpisz ciąg*myResourceGroupFD2* |
     | Plan usługi App Service/lokalizacja         | Wybierz pozycję **Nowy**.  W obszarze Plan usługi App Service wprowadź *myAppServicePlanWestEurope*, a następnie wybierz pozycję **OK**. 
     |      Location  |   Europa Zachodnia      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Tworzenie usługi Front Door dla aplikacji
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Dodawanie hosta frontonu na potrzeby usługi Front Door
Utwórz konfigurację usługi Front Door, która będzie kierować ruch użytkowników na podstawie najniższego opóźnienia pomiędzy dwoma zapleczami.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Front Door** > **Utwórz**.
2. W obszarze**Utwórz wystąpienie usługi Front Door** rozpocznij od dodania podstawowych informacji i podania subskrypcji, w której chcesz skonfigurować usługę Front Door. Tak jak w przypadku każdego innego zasobu platformy Azure musisz również podać grupę zasobów, a także region grupy zasobów, jeśli tworzysz nową grupę. Musisz też podać nazwę usługi Front Door.
3. Po wprowadzeniu podstawowych informacji pierwszym elementem, który należy zdefiniować podczas konfiguracji, jest **host frontonu**. Wynik powinien być prawidłową nazwą domeny, na przykład `myappfrontend.azurefd.net`. Nazwa hosta musi być globalnie unikatowa, ale zostanie to sprawdzone przez usługę Front Door. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Dodawanie zaplecza aplikacji i pul zaplecza

Następnie należy skonfigurować zaplecza aplikacji w puli zaplecza dla usługi Front Door, aby wiedzieć, gdzie znajduje się aplikacja. 

1. Kliknij ikonę „+”, aby dodać pulę zaplecza, a następnie podaj **nazwę** puli zaplecza, na przykład `myBackendPool`.
2. Następnie kliknij pozycję Dodawanie zapleczy, aby dodać wcześniej utworzone witryny.
3. W polu **Typ hosta docelowego** wybierz pozycję „App Service”, wybierz subskrypcję, w której utworzono witrynę internetową, a następnie wybierz pierwszą witrynę w obszarze **Nazwa hosta docelowego**, czyli  *myAppServicePlanEastUS.azurewebsites.net*.
4. W tym momencie pozostaw pozostałe pola bez zmian i kliknij polecenie **Dodaj**.
5. Powtórz kroki od 2 do 4, aby dodać drugą witrynę internetową, czyli *myAppServicePlanWestEurope.azurewebsites.net*
6. Opcjonalnie możesz zdecydować się na zaktualizowanie ustawień sond kondycji oraz równoważenia obciążenia dla puli zaplecza, ale domyślne wartości również powinny działać. Kliknij pozycję **Add** (Dodaj).


### <a name="c-add-a-routing-rule"></a>C. Dodawanie reguły rozsyłania
Na koniec kliknij ikonę „+” w obszarze Reguły routingu, aby skonfigurować regułę rozsyłania. Jest to niezbędne w celu mapowania hosta frontonu do puli zaplecza — mówiąc najprościej, ta konfiguracja określa, że jeśli żądanie przychodzi na adres `myappfrontend.azurefd.net`, to powinno zostać przekazane do puli zaplecza `myBackendPool`. Kliknij przycisk **Dodaj**, aby dodać regułę rozsyłania dla usługi Front Door. Teraz powinno być możliwe utworzenie usługi Front Door. Kliknij polecenie **Przejrzyj i utwórz**.

>[!WARNING]
> **Musisz** upewnić się, że każdy z hostów frontonu w usłudze Front Door ma regułę rozsyłania ze skojarzoną ścieżką domyślną („/\*”). Oznacza to, że wśród wszystkich reguł rozsyłania musi być co najmniej jedna reguła rozsyłania dla każdego z hostów frontonu zdefiniowana w ścieżce domyślnej („/\*”). Jeśli tak nie będzie, ruch użytkownika końcowego może nie być trasowany prawidłowo.

## <a name="view-front-door-in-action"></a>Usługa Front Door w działaniu
Po utworzeniu usługi Front Door globalne wdrożenie konfiguracji może zająć kilka minut. Gdy proces ten dobiegnie końca, uzyskaj dostęp do utworzonego hosta frontonu — czyli przejdź do adresu URL `myappfrontend.azurefd.net` w przeglądarce internetowej. Twoje żądanie zostanie automatycznie skierowane do najbliższego zaplecza spośród określonych zapleczy w puli zapleczy. 

### <a name="view-front-door-handle-application-failover"></a>Obsługa trybu failover dla aplikacji w usłudze Front Door
Jeśli chcesz przetestować natychmiastowe globalne działanie trybu failover usługi Front Door, przejdź do jednej z utworzonych witryn internetowych i zatrzymaj ją. Na podstawie ustawienia Sonda kondycji zdefiniowanego dla puli zaplecza ruch zostanie natychmiast skierowany do innego wdrożenia witryny internetowej, skonfigurowanego dla trybu failover. Możesz również przetestować działanie tej funkcji, wyłączając zaplecze w konfiguracji puli zaplecza dla usługi Front Door. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupy zasobów, aplikacje internetowe i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start została utworzona usługa Front Door, która pozwala kierować ruch użytkowników do aplikacji internetowych wymagających wysokiej dostępności i maksymalnej wydajności. Aby dowiedzieć się więcej na temat kierowania ruchu, zapoznaj się z [metodami rozsyłania](front-door-routing-methods.md) używanymi przez usługę Front Door.