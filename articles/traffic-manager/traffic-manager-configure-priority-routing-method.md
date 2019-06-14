---
title: Konfigurowanie metody routingu ruchu priorytet przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak skonfigurować metodę routingu ruchu priorytet w usłudze Traffic Manager
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 259457a604727cba6e6964851ec4fcf4b13a20a6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048491"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu ruchu priorytet w usłudze Traffic Manager

Niezależnie od trybu witryny sieci Web witryn sieci Web platformy Azure zapewniają już funkcja pracy awaryjnej dla witryn sieci Web w centrum danych (region). Usługa Traffic Manager udostępnia trybu failover dla witryn sieci Web w różnych centrach danych.

Typowy wzorzec dla usługi w tryb failover jest wysyłania ruchu do podstawowej usługi i udostępniają zestaw identycznych usługi tworzenia kopii zapasowych dla trybu failover. Poniższe kroki wyjaśniają sposób konfigurowania priorytetów pracy w trybie failover przy użyciu usług Azure cloud services i witryny sieci Web:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Aby skonfigurować metodę routingu ruchu priorytet

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profile usługi Traffic Manager** a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu dla.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy obecne są usługami w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**, a następnie w **konfiguracji** bloku, podaj następujące:
    1. Aby uzyskać **ustawienia metody routingu ruchu**, sprawdź, czy metodę routingu ruchu **priorytet**. Jeśli nie, kliknij pozycję **priorytet** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego w ramach tego profilu, w następujący sposób:
        1. Wybierz odpowiedni **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik */* . Monitorowanie punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **Zapisz**.
5. W **ustawienia** kliknij **punktów końcowych**.
6. W **punktów końcowych** bloku, sprawdź kolejność priorytetów punktów końcowych. Po wybraniu **priorytet** metody routingu ruchu, kolejność jest ważna wybranych punktów końcowych. Sprawdź kolejność priorytetów punktów końcowych.  Podstawowy punkt końcowy jest u góry. Dokładnie sprawdź w kolejności, w której jest on wyświetlany. wszystkie żądania będą kierowane do pierwszego punktu końcowego i jeśli usługa Traffic Manager wykryje będącą w złej kondycji, ruch automatycznie przełącza się do następnego punktu końcowego. 
7. Aby zmienić kolejność priorytetów punktu końcowego, kliknij punkt końcowy, a następnie w **punktu końcowego** bloku, który jest wyświetlany, kliknij przycisk **Edytuj** i zmień **priorytet** wartość zgodnie z potrzebami. 
8. Kliknij przycisk **Zapisz** Aby zapisać zmiany ustawień punktu końcowego.
9. Po wprowadzeniu zmian konfiguracji, kliknij przycisk **Zapisz** w dolnej części strony.
10. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, a następnie kliknij pozycję profil usługi Traffic Manager w taki sposób, w wynikach, wyświetlana.
    2.  W **usługi Traffic Manager** bloku profilu, kliknij przycisk **Przegląd**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzonego profilu usługi Traffic Manager. Może to służyć przez dowolnego klienta (na przykład, przechodząc do niego w przeglądarce sieci web) Pobierz kierowane do prawego punktu końcowego jako ustalany na podstawie typu routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego, a jeśli usługa Traffic Manager wykryje będącą w złej kondycji, ruch automatycznie przełącza się do następnego punktu końcowego.
11. Po profilu usługi Traffic Manager działa, należy edytować rekord DNS na autorytatywny serwer DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu priorytet przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Kolejne kroki


- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metody routingu opartego na wydajności](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png