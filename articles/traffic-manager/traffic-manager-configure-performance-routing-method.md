---
title: Konfigurowanie metody routingu ruchu wydajności przy użyciu usługi Azure Traffic Manager | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak skonfigurować Menedżera ruchu do kierowania ruchu do punktu końcowego o najniższym opóźnieniu
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938756"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurowanie metody routingu ruchu wydajności

Metoda routingu ruchu wydajności umożliwia kierowanie ruchu do punktu końcowego z najniższym opóźnieniem z sieci klienta. Zazwyczaj centrum danych o najniższym opóźnieniu jest najbliżej odległości geograficznej. Ta metoda routingu ruchu nie może uwzględniać zmian konfiguracji lub obciążenia sieci w czasie rzeczywistym.

##  <a name="to-configure-performance-routing-method"></a>Aby skonfigurować metodę routingu wydajności

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu wyszukaj **profile usługi Traffic Manager,** a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **profilu usługi Traffic Manager** sprawdź, czy są obecne zarówno usługi w chmurze, jak i witryny sieci Web, które chcesz uwzględnić w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W przypadku **ustawień metody routingu ruchu**dla metody **Routing wybierz** pozycję **Wydajność**.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w tym profilu w następujący sposób:
        1. Wybierz odpowiedni **protokół**i określ numer **portu.** 
        2. Dla **ścieżki** wpisz */* ukośnik do przodu . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik do przodu "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. U góry strony kliknij pozycję **Zapisz**.
5.  Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu wyszukaj nazwę profilu Usługi Traffic Manager i kliknij profil Usługi Traffic Manager w wyświetlanych wynikach.
    2.  W bloku profilu **usługi Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profilu usługi Traffic Manager** wyświetlana jest nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to być używane przez wszystkich klientów (na przykład przez przejście do niego za pomocą przeglądarki sieci web), aby uzyskać kierowane do odpowiedniego punktu końcowego, zgodnie z typem routingu. W takim przypadku wszystkie żądania są kierowane do punktu końcowego z najniższym opóźnieniem z sieci klienta.
6. Gdy profil usługi Traffic Manager działa, edytuj rekord DNS na autorytatywnym serwerze DNS, aby skierować nazwę domeny firmy na nazwę domeny Usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu wydajności przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [przetestować ustawienia Menedżera ruchu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png