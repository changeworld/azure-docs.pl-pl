---
title: Tworzenie niestandardowej sondy — Azure Application Gateway — witryna Azure Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie niestandardowej sondy dla bramy aplikacji przy użyciu portalu
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862169"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Tworzenie niestandardowej sondy dla bramy aplikacji przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule niestandardowej sondy jest dodać do istniejącej bramy aplikacji za pośrednictwem witryny Azure portal. Niestandardowe sondy są przydatne w przypadku aplikacji używających konkretnej strony kontroli kondycji lub dla aplikacji, które nie są oferowane pomyślnej odpowiedzi na domyślnej aplikacji sieci web.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz już bramę aplikacji, odwiedź stronę [utworzyć bramę aplikacji](application-gateway-create-gateway-portal.md) do utworzenia bramy aplikacji do pracy z.

## <a name="createprobe"></a>Tworzenie sondy

Sondy są konfigurowane w dwuetapowy proces za pośrednictwem portalu. Pierwszym krokiem jest, aby utworzyć sondę. W drugim kroku dodasz sondy do ustawienia http zaplecza bramy aplikacji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free)

1. W okienku Azure Ulubione portalu kliknij pozycję wszystkie zasoby. Kliknij przycisk application gateway w bloku wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wprowadzić "partners.contoso.NET" w polu Filtruj według nazwy... aby łatwo uzyskać dostęp do bramy aplikacji.

1. Kliknij przycisk **sondy** i kliknij przycisk **Dodaj** przycisk, aby dodać sondy.

   ![Dodawanie sondy bloku z informacjami o wypełnione][1]

1. Na **Dodaj sondę kondycji** bloku, podać wymagane informacje dla sondy, a po zakończeniu kliknij przycisk **OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe|Ta wartość jest przyjazna nazwa sondy, który jest dostępny w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół, który używa sondy kondycji.|
   |**Host**|i.e contoso.com|Ta wartość jest nazwę hosta, który jest używany dla sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowany w usłudze Application Gateway, w przeciwnym razie użyj "127.0.0.1". Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej.|
   |**Ścieżka**|/ lub inną ścieżkę|W pozostałej części pełny adres url dla niestandardowej sondy. Nieprawidłowa ścieżka zaczyna się od "/". Domyślne ścieżki http:\//contoso.com po prostu użyj "/" |
   |**Interwał (w sekundach)**|30|Jak często sondy jest uruchamiany pod kątem kondycji. Nie zaleca się ustawić niższy niż 30 sekund.|
   |**Limit czasu (w sekundach)**|30|Ilość czasu sondy czeka przed przekroczeniem limitu czasu. Interwał limitu czasu musi być wystarczająco wysoka, że wywołania http może również upewnić się, że strona kondycji wewnętrznej bazy danych jest dostępna.|
   |**Próg złej kondycji**|3|Liczba nieudanych prób jest uznawana za złą. Próg 0 oznacza, że jeśli kontrola kondycji nie powiedzie się zapleczem jest określana złej kondycji od razu.|

   > [!IMPORTANT]
   > Nazwa hosta nie jest taka sama jak nazwa serwera. Ta wartość jest nazwą hosta wirtualnego uruchomiony na serwerze aplikacji. Sonda jest wysyłana do http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Dodawanie sondy do bramy

Teraz, gdy została utworzona Sonda jest czas, aby dodać go do bramy. Ustawienia sondowania są ustawiane w ustawieniach protokołu http zaplecza bramy aplikacji.

1. Kliknij przycisk **ustawienia HTTP** w usłudze application gateway, wyświetlenie bloku kliknij pozycję Konfiguracja zaplecza bieżącego ustawienia http wymienionych w oknie.

   ![okno Ustawienia protokołu HTTPS][2]

1. Na **appGatewayBackEndHttpSettings** bloku ustawienia wyboru **użycia niestandardowej sondy** pole wyboru i wybierz sondę utworzoną w [Utwórz sondę](#createprobe) sekcji  **Niestandardowe sondy** listy rozwijanej...
   Po zakończeniu kliknij przycisk **Zapisz** i ustawienia są stosowane.

Domyślnej funkcji badania sprawdza, czy dostęp do domyślnej aplikacji sieci web. Teraz, niestandardowe sondy został utworzony, bramy application gateway używa niestandardową ścieżkę, zdefiniowane w celu monitorowania kondycji dla serwerów zaplecza. Na podstawie kryteriów, które zostało zdefiniowane, bramy application gateway sprawdza, czy ścieżka określona w sondy. Jeśli wywołanie host: Port / ścieżki nie może zwracać HTTP 200-399 stanu odpowiedzi, serwer jest wykluczana z rotacji, po osiągnięciu progu złej kondycji. Sondowanie jest kontynuowane po wystąpieniu złej kondycji do określania, kiedy dobrej kondycji zostanie ponownie. Po wystąpieniu jest dodawany do puli serwerów w dobrej kondycji, ruch, który rozpoczyna się przepływać do niej ponownie, a badania do wystąpienia jest kontynuowane po użytkownik określonym przedziale czasu, jak zwykle.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL za pomocą usługi Azure Application Gateway, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

