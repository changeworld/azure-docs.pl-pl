---
title: Samouczek — konfigurowanie routingu ruchu priorytetowego za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu priorytetowego w usłudze Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938730"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu priorytetowego w usłudze Traffic Manager

Niezależnie od trybu witryny sieci Web witryny sieci Web witryny sieci Web platformy Azure już zapewniają funkcje pracy awaryjnej dla witryn sieci Web w centrum danych (znany również jako region). Usługa Traffic Manager udostępnia trybu failover dla witryn sieci Web w różnych centrach danych.

Typowy wzorzec pracy awaryjnej usługi jest wysyłanie ruchu do usługi podstawowej i zapewnić zestaw identycznych usług tworzenia kopii zapasowych dla pracy awaryjnej. W poniższych krokach wyjaśniono, jak skonfigurować tę priorytetową usługę pracy awaryjnej za pomocą usług w chmurze i witryn sieci Web platformy Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Aby skonfigurować metodę routingu ruchu priorytetowego

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu wyszukaj **profile usługi Traffic Manager,** a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **profilu usługi Traffic Manager** sprawdź, czy są obecne zarówno usługi w chmurze, jak i witryny sieci Web, które chcesz uwzględnić w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W przypadku **ustawień metody routingu ruchu**sprawdź, czy metodą routingu ruchu jest **priorytet**. Jeśli tak nie jest, kliknij pozycję **Priorytet** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w tym profilu w następujący sposób:
        1. Wybierz odpowiedni **protokół**i określ numer **portu.** 
        2. Dla **ścieżki** wpisz */* ukośnik do przodu . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik do przodu "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. U góry strony kliknij pozycję **Zapisz**.
5. W sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**.
6. W **punktach końcowych** bloku, przejrzyj kolejność priorytetów dla punktów końcowych. Po wybraniu metody routingu ruchu **priorytetowego** ma znaczenie kolejność wybranych punktów końcowych. Sprawdź kolejność priorytetów punktów końcowych.  Podstawowy punkt końcowy znajduje się na górze. Sprawdź dokładnie kolejność, w jakiej jest wyświetlany. wszystkie żądania zostaną skierowane do pierwszego punktu końcowego, a jeśli usługa Traffic Manager wykryje, że jest w złej kondycji, ruch automatycznie przejdzie w stan failed do następnego punktu końcowego. 
7. Aby zmienić kolejność priorytetów punktu końcowego, kliknij punkt końcowy, a w wyświetlonym bloku **punktu końcowego** kliknij pozycję **Edytuj** i zmień wartość **priorytetu** w razie potrzeby. 
8. Kliknij **przycisk Zapisz,** aby zapisać zmienić ustawienia punktu końcowego.
9. Po zakończeniu zmian konfiguracji kliknij przycisk **Zapisz** u dołu strony.
10. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu wyszukaj nazwę profilu Usługi Traffic Manager i kliknij profil Usługi Traffic Manager w wyświetlanych wynikach.
    2.  W bloku profilu **usługi Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profilu usługi Traffic Manager** wyświetlana jest nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to być używane przez wszystkich klientów (na przykład przez przejście do niego za pomocą przeglądarki sieci web), aby uzyskać kierowane do odpowiedniego punktu końcowego, zgodnie z typem routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego i jeśli usługa Traffic Manager wykryje, że jest w złej kondycji, ruch automatycznie działa w sieci w następnej kolejności.
11. Gdy profil usługi Traffic Manager działa, edytuj rekord DNS na autorytatywnym serwerze DNS, aby skierować nazwę domeny firmy na nazwę domeny Usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu priorytetowego przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki


- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metodzie routingu wydajności](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [przetestować ustawienia Menedżera ruchu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png