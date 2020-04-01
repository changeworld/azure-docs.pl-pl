---
title: Samouczek — konfigurowanie ważonego routingu ruchu okrężnego za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak zrównoważyć ruch przy użyciu metody okrężnego w usłudze Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938708"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody ważonego routingu ruchu w usłudze Traffic Manager

Typowy wzorzec metody routingu ruchu jest zapewnienie zestawu identycznych punktów końcowych, które obejmują usługi w chmurze i witryny sieci Web i wysłać ruch do każdego jednakowo. W poniższych krokach opisano sposób konfigurowania tego typu metody routingu ruchu.

> [!NOTE]
> Aplikacja Azure Web App udostępnia już funkcje równoważenia obciążenia okrężnego dla witryn sieci Web w regionie platformy Azure (które mogą obejmować wiele centrów danych). Usługa Traffic Manager umożliwia rozpowszechnianie ruchu w witrynach sieci Web w różnych centrach danych.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Aby skonfigurować metodę ważonego routingu ruchu

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu wyszukaj **profile usługi Traffic Manager,** a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **profilu usługi Traffic Manager** sprawdź, czy są obecne zarówno usługi w chmurze, jak i witryny sieci Web, które chcesz uwzględnić w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W przypadku **ustawień metody routingu ruchu**sprawdź, czy metoda routingu ruchu jest **ważona**. Jeśli tak nie jest, kliknij pozycję **Ważony** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w tym profilu w następujący sposób:
        1. Wybierz odpowiedni **protokół**i określ numer **portu.** 
        2. Dla **ścieżki** wpisz */* ukośnik do przodu . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik do przodu "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. U góry strony kliknij pozycję **Zapisz**.
5. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu wyszukaj nazwę profilu Usługi Traffic Manager i kliknij profil Usługi Traffic Manager w wyświetlanych wynikach.
    2.  W bloku profilu **usługi Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profilu usługi Traffic Manager** wyświetlana jest nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to być używane przez wszystkich klientów (na przykład, przechodząc do niego za pomocą przeglądarki sieci web), aby uzyskać kierowane do odpowiedniego punktu końcowego, zgodnie z typem routingu. W takim przypadku wszystkie żądania są kierowane do każdego punktu końcowego w sposób okrężny.
6. Gdy profil usługi Traffic Manager działa, edytuj rekord DNS na autorytatywnym serwerze DNS, aby skierować nazwę domeny firmy na nazwę domeny Usługi Traffic Manager.

![Konfigurowanie metody ważonego routingu ruchu przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metodzie routingu ruchu priorytetowego](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metodzie routingu ruchu wydajności](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [przetestować ustawienia Menedżera ruchu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
