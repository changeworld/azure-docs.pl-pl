---
title: Konfigurowanie metody routingu ruchu priorytet przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak skonfigurować priorytet metodę routingu ruchu w usłudze Traffic Manager
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
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876635"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu ruchu priorytet w usłudze Traffic Manager

Niezależnie od trybu witryny sieci Web witryn sieci Web Azure już udostępniają funkcje trybu failover dla witryn sieci Web w centrum danych (regionu). Menedżer ruchu zapewnia tryb failover dla witryn sieci Web w różnych centrach danych.

Wzorzec wspólnej pracy awaryjnej usługi jest przesyłają dane do głównej usługi i udostępniają zestaw identyczne usługi tworzenia kopii zapasowych w trybie failover. W poniższych krokach opisano sposób konfigurowania tej priorytetów trybu failover z usług w chmurze Azure i witryn sieci Web:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Aby skonfigurować metody routingu ruchu o priorytecie

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profilów usługi Traffic Manager** , a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy są obecne usługi w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**i w **konfiguracji** bloku ukończenia w następujący sposób:
    1. Dla **ustawienia metody routingu ruchu**, sprawdź, czy metody routingu ruchu **priorytet**. Jeśli nie, kliknij przycisk **priorytet** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego, w tym profilu w następujący sposób:
        1. Wybierz odpowiednie **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik  */* . Aby monitorować punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **zapisać**.
5. W **ustawienia** kliknij **punkty końcowe**.
6. W **punkty końcowe** bloku, przejrzyj kolejność priorytetów punktów końcowych. Po wybraniu **priorytet** metody routingu ruchu, kolejność sprawach wybranych punktów końcowych. Sprawdź kolejność priorytetów punktów końcowych.  Podstawowy punkt końcowy jest u góry. Dokładnie sprawdź zlecenia, który jest wyświetlany. wszystkie żądania będą kierowane do pierwszego punktu końcowego, a w przypadku wykrycia przez Menedżera ruchu jest zła, ruch automatycznie przełącza do następnego punktu końcowego. 
7. Aby zmienić kolejność priorytetów punktu końcowego, kliknij punkt końcowy i w **punktu końcowego** bloku, który jest wyświetlany, kliknij przycisk **Edytuj** i zmienić **priorytet** wartości zgodnie z potrzebami. 
8. Kliknij przycisk **zapisać** Zapisz zmiany ustawień punktu końcowego.
9. Po zakończeniu zmiany w konfiguracji, kliknij przycisk **zapisać** w dolnej części strony.
10. Testowanie zmian w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu, wyszukaj nazwę profilu Menedżera ruchu, a następnie kliknij przycisk profilu usługi Traffic Manager w wynikach który wyświetlone.
    2.  W **Traffic Manager** bloku, kliknij opcję **omówienie**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzony profil Menedżera ruchu. Może to służyć przez wszystkich klientów (na przykład, przechodząc do niej przy użyciu przeglądarki sieci web) do pobrania kierowane do prawej punktu końcowego jako określana przez typ routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego, a w przypadku wykrycia przez Menedżera ruchu jest zła, ruch automatycznie przełącza do następnego punktu końcowego.
11. Po działa profilu Menedżera ruchu, należy edytować rekord DNS na z autorytatywnego serwera DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu priorytet przy użyciu Menedżera ruchu][1]

## <a name="next-steps"></a>Następne kroki


- Dowiedz się więcej o [ważone metody routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metody routingu wydajności](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficzne metody routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [Menedżera ruchu ustawienia testu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png