---
title: Zarządzanie ruchem w aplikacjach wielodostępnych takich jak usługi App service web apps za pomocą usługi Azure Application Gateway — Portal
description: Ten artykuł zawiera wskazówki dotyczące sposobu konfigurowania sieci web Azure aplikacji usługi App service jako elementów członkowskich w puli zaplecza w istniejącej lub nowej bramie aplikacji.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831217"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurowanie usługi App Service z usługą Application Gateway

Bramy Application gateway umożliwia aplikacji sieci Web usługi Azure App service lub inne usługi wielodostępnej jako składowej puli zaplecza. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Tworzenie puli wewnętrznej bazy danych i dodać usługi App Service do niego
> - Utwórz ustawienia protokołu HTTP i niestandardowe sondy z przełącznikami "Wybierz nazwy hosta" włączone

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Application gateway: Jeśli nie masz istniejącej bramy aplikacji, zobacz temat jak [Tworzenie bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Usługa App service: Jeśli nie masz istniejącej usługi App service, zobacz [dokumentacja usługi App service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Dodaj usługi App service jako pula zaplecza

1. W witrynie Azure portal Otwórz widok konfiguracji usługi application gateway.

2. W obszarze **pule zaplecza**, kliknij pozycję **Dodaj** Aby utworzyć nową pulę zaplecza.

3. Podaj nazwę odpowiedniej do puli zaplecza. 

4. W obszarze **cele**, kliknij listę rozwijaną i wybierz polecenie **App Services** jako opcję.

5. Poniżej listy rozwijanej **cele** listy rozwijanej będą wyświetlane, które będzie zawierać listę usług aplikacji. Z tej listy rozwijanej wybierz usługi App Service, aby dodać jako składową puli zaplecza, a następnie kliknij przycisk Dodaj.

   ![Usługi aplikacji wewnętrznej bazy danych](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Utwórz ustawienia protokołu HTTP dla usługi App service

1. W obszarze **ustawienia HTTP**, kliknij przycisk **Dodaj** Aby utworzyć nowe ustawienie protokołu HTTP.

2. Wprowadź nazwę dla ustawienia protokołu HTTP i można włączyć lub wyłączyć koligacja na podstawie plików Cookie zgodnie z wymaganiami.

3. Wybierz protokół HTTP lub HTTPS, zgodnie z danego przypadku użycia. 

4. Pole wyboru dla **użycia dla usługi App Service** i spowoduje włączenie **Utwórz sondę z nazwą hosta pobranie z adresów zaplecza** i **pobrania nazwy hosta z adresu zaplecza** opcje. Ta opcja spowoduje również automatycznie Utwórz sondę z przełącznikiem, który został włączony i skojarzyć ją z tym ustawieniem protokołu HTTP.

5. Kliknij przycisk **OK** Aby utworzyć ustawienie protokołu HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Tworzenie reguły, aby powiązać puli zaplecza, odbiornik i ustawienie protokołu HTTP

1. W obszarze **reguły**, kliknij przycisk **podstawowe** Aby utworzyć nową regułę podstawowe.

2. Podaj odpowiednią nazwę, a następnie wybierz odbiornik, który będzie akceptować żądań przychodzących dla usługi App service.

3. W **puli zaplecza** listy rozwijanej wybierz pulę zaplecza, utworzonego powyżej.

4. W **ustawienie protokołu HTTP** listy rozwijanej wybierz pozycję HTTP, ustawienie utworzonego powyżej.

5. Kliknij przycisk **OK** Aby zapisać tę regułę.

   ![Reguła](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Ograniczanie dostępu

Aplikacje sieci web wdrażane w tych przykładach używania publicznych adresów IP, które są dostępne bezpośrednio z Internetu. Ułatwia to Rozwiązywanie problemów podczas nauki obsługi nowych funkcji i podjęcie próby nowe obiekty. Ale jeśli zamierzasz wdrożyć funkcję w środowisku produkcyjnym, należy dodać więcej ograniczeń.

Jednym ze sposobów, możesz ograniczyć dostęp do aplikacji sieci web jest użycie [ograniczenia adresów IP statycznych w usłudze Azure App Service](../app-service/app-service-ip-restrictions.md). Na przykład aplikacja sieci web można ograniczyć tak, aby tylko odbiera ruch z bramy aplikacji. Użyj funkcji ograniczeń adresów IP usługi aplikacji, aby wyświetlić listę adresów VIP bramy aplikacji jako adres tylko z dostępem.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi App service i innych Obsługa wielu dzierżaw z usługą application gateway, zobacz [obsługi wielu dzierżawców z usługą application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

W przypadku, gdy odpowiedź z usługi App service jest przekierowanie do adresu URL usługi App service, zobacz instrukcje [Rozwiązywanie problemów z przekierowania do wystawienia adres URL usługi App service](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
