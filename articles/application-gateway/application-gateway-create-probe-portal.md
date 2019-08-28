---
title: Tworzenie niestandardowej sondy — Azure Application Gateway — Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć niestandardową sondę dla Application Gateway przy użyciu portalu
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
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061789"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Tworzenie niestandardowej sondy dla Application Gateway przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule opisano Dodawanie niestandardowej sondy do istniejącej bramy aplikacji za pomocą Azure Portal. Niestandardowe sondy są przydatne w przypadku aplikacji, które mają konkretną kontrolę kondycji lub dla aplikacji, które nie zapewniają prawidłowej odpowiedzi w domyślnej aplikacji sieci Web.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz jeszcze bramy aplikacji, odwiedź stronę [tworzenie Application Gateway](application-gateway-create-gateway-portal.md) , aby utworzyć bramę aplikacji do pracy.

## <a name="createprobe"></a>Tworzenie sondy

Sondy są konfigurowane w procesie dwuetapowym za pomocą portalu. Pierwszym krokiem jest utworzenie sondy. W drugim kroku należy dodać sondę do ustawień protokołu HTTP zaplecza bramy aplikacji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu uzyskania bezpłatnej [miesięcznej wersji próbnej](https://azure.microsoft.com/free)

1. W okienku Ulubione Azure Portal kliknij pozycję wszystkie zasoby. Kliknij pozycję Brama aplikacji w bloku wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać partners.contoso.net w polu Filtruj według nazwy... aby łatwo uzyskać dostęp do bramy aplikacji.

1. Kliknij pozycję sondy i kliknij przycisk **Dodaj** , aby dodać sondę.

   ![Dodaj blok sondy z informacjami wypełnionymi][1]

1. W bloku **Dodawanie sondy kondycji** wprowadź wymagane informacje dotyczące sondy, a po zakończeniu kliknij przycisk **OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe|Ta wartość jest przyjazną nazwą sondy, która jest dostępna w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół używany przez sondę kondycji.|
   |**Host**|co contoso.com|Ta wartość jest nazwą hosta używaną dla sondy. Dotyczy tylko sytuacji, gdy wiele witryn jest skonfigurowanych na Application Gateway, w przeciwnym razie użyj "127.0.0.1". Ta wartość różni się od nazwy hosta maszyny wirtualnej.|
   |**Path**|/lub inną ścieżkę|Pozostała część pełnego adresu URL dla sondy niestandardowej. Prawidłowa ścieżka zaczyna się od znaku "/". Dla domyślnej ścieżki http:\//contoso.com samo użycie "/" |
   |**Interwał (s)**|30|Częstotliwość uruchamiania sondy w celu sprawdzenia kondycji. Nie zaleca się ustawiania wartości mniejszej niż 30 sekund.|
   |**Limit czasu (w sekundach)**|30|Czas, przez jaki sonda czeka przed upływem limitu czasu. Interwał limitu czasu musi być wystarczająco duży, aby można było wykonać wywołanie http, aby zapewnić dostępność strony kondycji zaplecza.|
   |**Próg złej kondycji**|3|Liczba nieudanych prób, które mają być uznawane za złej kondycji. Próg można ustawić na wartość 1 lub większą.|

   > [!IMPORTANT]
   > Nazwa hosta nie jest taka sama jak nazwa serwera. Ta wartość jest nazwą hosta wirtualnego uruchomionego na serwerze aplikacji. Sonda jest wysyłana do http://(nazwa hosta):(port z httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Dodawanie sondy do bramy

Teraz, gdy sonda została utworzona, należy ją dodać do bramy. Ustawienia sondy są ustawiane w ustawieniach protokołu HTTP zaplecza bramy aplikacji.

1. Kliknij pozycję **Ustawienia http** w bramie aplikacji, aby wyświetlić blok konfiguracja, a następnie kliknij bieżące ustawienia protokołu HTTP zaplecza wymienione w oknie.

   ![okno ustawień https][2]

1. W bloku ustawienia **appGatewayBackEndHttpSettings** zaznacz pole wyboru **Użyj sondy niestandardowej** i wybierz sondę utworzoną w sekcji [Tworzenie sondy](#createprobe) na liście rozwijanej **sonda** niestandardowa.
   Po zakończeniu kliknij przycisk **Zapisz** , a ustawienia zostaną zastosowane.

Sonda domyślna sprawdza domyślny dostęp do aplikacji sieci Web. Po utworzeniu niestandardowej sondy Brama aplikacji korzysta ze ścieżki niestandardowej zdefiniowanej do monitorowania kondycji serwerów wewnętrznej bazy danych. Na podstawie zdefiniowanych kryteriów Brama aplikacji sprawdza ścieżkę określoną w sondie. Jeśli wywołanie hosta: Port/ścieżka nie zwróci odpowiedzi o stanie HTTP 200-399, po osiągnięciu progu złej kondycji serwer jest wyłączany. Sondowanie jest kontynuowane w wystąpieniu złej kondycji, aby określić, kiedy znów stanie się w dobrej kondycji. Po dodaniu wystąpienia z powrotem do odpowiedniej puli serwerów, ruch przychodzący do niego ponownie i sondowanie do wystąpienia jest kontynuowane przez określony przez użytkownika interwał jako normalny.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL za pomocą usługi Azure Application Gateway, zobacz [Konfigurowanie odciążania protokołu SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

