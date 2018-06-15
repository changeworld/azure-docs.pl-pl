---
title: Konfigurowanie metody routingu ruchu okrężnego ważoną przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób równoważenia obciążenia ruchu przy użyciu metody okrężnego w usłudze Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876544"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu ruchu ważoną w usłudze Traffic Manager

Wzorzec typowe metody routingu ruchu jest udostępniają zestaw identyczne punkty końcowe, które obejmują usługi w chmurze i witryn sieci Web, i przesyłają dane do każdej z nich w działaniu okrężnym. Poniższe kroki przedstawiają sposób konfigurowania tego typu metody routingu ruchu.

> [!NOTE]
> Witryn sieci Web Azure zapewniają już obciążenia okrężnego równoważenia funkcji dla witryn sieci Web w centrum danych (regionu). Traffic Manager służy do określenia metody routingu ruchu okrężnego dla witryn sieci Web w różnych centrach danych.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Aby skonfigurować metody routingu ruchu ważoną

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profilów usługi Traffic Manager** , a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy są obecne usługi w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**i w **konfiguracji** bloku ukończenia w następujący sposób:
    1. Aby uzyskać **ustawienia metody routingu ruchu**, sprawdź, czy metody routingu ruchu **ważone**. Jeśli nie, kliknij przycisk **ważone** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego, w tym profilu w następujący sposób:
        1. Wybierz odpowiednie **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik  */* . Aby monitorować punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **zapisać**.
5. Testowanie zmian w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu, wyszukaj nazwę profilu Menedżera ruchu, a następnie kliknij przycisk profilu usługi Traffic Manager w wynikach który wyświetlone.
    2.  W **Traffic Manager** bloku, kliknij opcję **omówienie**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzony profil Menedżera ruchu. Może to służyć przez wszystkich klientów (na przykład, przechodząc do niej przy użyciu przeglądarki sieci web) do pobrania kierowane do prawej punktu końcowego jako określana przez typ routingu. W takim przypadku wszystkie żądania są kierowane każdego punktu końcowego w działaniu okrężnym.
6. Po działa profilu Menedżera ruchu, należy edytować rekord DNS na z autorytatywnego serwera DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu ważoną przy użyciu Menedżera ruchu][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Metoda routingu ruchu priorytet](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [wydajności Metoda routingu ruchu](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficzne metody routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [Menedżera ruchu ustawienia testu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
