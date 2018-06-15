---
title: Tworzenie niestandardowych sondowania — brama aplikacji w usłudze Azure - Azure Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe sondowania bramy aplikacji przy użyciu portalu
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
ms.openlocfilehash: 45737c1c378ec56a5e2bedec8c1f7b7bc7ba6225
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203919"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Tworzenie niestandardowych sondowania bramy aplikacji przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule możesz dodać niestandardowe sondowania istniejącą bramę aplikacji za pośrednictwem portalu Azure. Niestandardowe sond są przydatne dla aplikacji, które mają strona sprawdzania kondycji określonych lub aplikacji, które nie mają pomyślnej odpowiedzi na domyślnej aplikacji sieci web.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz już istniejącą bramę aplikacji, odwiedź stronę [Utwórz bramę aplikacji](application-gateway-create-gateway-portal.md) Aby utworzyć bramę aplikacji do pracy z.

## <a name="createprobe"></a>Utwórz sondy

Sondy są konfigurowane w dwóch etapach za pośrednictwem portalu. Pierwszym krokiem jest można utworzyć sondy. W drugim kroku możesz dodać sondy ustawienia http zaplecza bramy aplikacji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli nie masz już konto, możesz zarejestrować się w celu [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/free)

1. W okienku Azure Ulubione portalu kliknij pozycję wszystkie zasoby. Kliknij przycisk brama aplikacji w bloku wszystkich zasobów. Jeśli subskrypcja już ma kilka zasobów, możesz wprowadzić partners.contoso.net w filtrze według nazwy... aby łatwo uzyskać dostęp do bramy aplikacji.

1. Kliknij przycisk **sondy** i kliknij przycisk **Dodaj** przycisk, aby dodać badanie.

  ![Dodaj sondowania bloku z informacjami o wypełnione][1]

1. Na **sondy kondycji Dodaj** bloku, podaj wymagane informacje sondy, a po zakończeniu kliknij przycisk **OK**.

  |**Ustawienie** | **Wartość** | **Szczegóły**|
  |---|---|---|
  |**Nazwa**|customProbe|Ta wartość jest przyjazną nazwę sondowania, który jest dostępny w portalu.|
  |**Protokół**|HTTP lub HTTPS | Protokół, który używa sondy kondycji.|
  |**Host**|tj contoso.com|Ta wartość jest nazwa hosta jest używana do sondowania. Dotyczy tylko wtedy, gdy obejmujący wiele lokacji jest skonfigurowany dla bramy aplikacji, w przeciwnym razie użyj '127.0.0.1'. Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej.|
  |**Ścieżka**|/ lub inną ścieżkę|W pozostałej części pełny adres url dla niestandardowych sondy. Nieprawidłowa ścieżka zaczyna się od '/'. Ścieżki domyślnej http://contoso.com użyć "/" |
  |**Interwał (w sekundach)**|30|Jak często sondy jest uruchamiany do sprawdzenia kondycji. Nie zaleca się ustawić niższy niż 30 sekund.|
  |**Limit czasu (w sekundach)**|30|Ilość czasu sondy czeka przed przekroczeniem limitu czasu. Limit czasu musi być odpowiednio wysoka, że wywołanie http może również upewnić się, że strona kondycji wewnętrznej bazy danych jest dostępna.|
  |**Próg złej kondycji**|3|Liczba nieudanych prób jest określana jako zła. Próg 0 oznacza, że jeśli zaplecza niepowodzenia sprawdzania kondycji jest określany złej kondycji od razu.|

  > [!IMPORTANT]
  > Nazwa hosta nie jest taka sama jak nazwa serwera. Ta wartość jest nazwą hosta wirtualnego uruchomiony na serwerze aplikacji. Sonda są wysyłane do http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Dodaj sondy do bramy

Obecnie sondy został utworzony, jest czas, aby dodać go do bramy. Ustawienia http zaplecza bramy aplikacji są ustawienia sondy.

1. Kliknij przycisk **ustawienia HTTP** na bramie aplikacji, można wyświetlić kliknij bloku konfiguracji zaplecza bieżące ustawienia http listy w oknie.

  ![Okno ustawień protokołu HTTPS][2]

1. Na **appGatewayBackEndHttpSettings** bloku ustawienia wyboru **sondowania niestandardowych użyj** wyboru i wybierz polecenie sondowania utworzone w [utworzyć sondy](#createprobe) sekcję  **Niestandardowe sondowania** listy rozwijanej.
Po zakończeniu kliknij przycisk **zapisać** i ustawienia są stosowane.

Domyślnego badania sprawdza dostęp do domyślnej aplikacji sieci web. Teraz, gdy utworzono niestandardowe sondowania, brama aplikacji w używa niestandardowa ścieżka zdefiniowana do monitorowania kondycji dla serwerów zaplecza. Brama aplikacji w oparte na kryteriach, które zostało zdefiniowane, sprawdza ścieżka określona w sondy. Jeśli wywołanie hosta: Port / ścieżki nie może zwracać HTTP 200 399 stanu odpowiedzi, serwer jest wykluczony z cyklu po osiągnięciu progu złej kondycji. Sondowanie jest kontynuowane od zła wystąpienie do określania, kiedy dobrej kondycji zostanie ponownie. Po wystąpienie zostanie dodany do puli serwerów w dobrej kondycji, ruch, który rozpoczyna się przepływać do niej ponownie, a sondowanie do wystąpienia programu jest kontynuowane od użytkownika określonego interwału normalnie.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL z bramy aplikacji Azure, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

