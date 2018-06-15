---
title: Konfigurowanie metody routingu ruchu wydajności przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager można kierować ruchem do punktu końcowego o najniższym opóźnieniu
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
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876313"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurowanie metody routingu ruchu wydajności

Metody routingu ruchu wydajności umożliwia kierować ruch do punktu końcowego o najniższym opóźnieniu sieci klienta. Zazwyczaj centrum danych z najniższym opóźnieniu jest najbardziej w odległości geograficznych. Tej metody routingu ruchu nie można uwzględnić w czasie rzeczywistym zmiany w konfiguracji sieci lub obciążenia.

##  <a name="to-configure-performance-routing-method"></a>Aby skonfigurować metody routingu wydajności

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profilów usługi Traffic Manager** , a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy są obecne usługi w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**i w **konfiguracji** bloku ukończenia w następujący sposób:
    1. Dla **ustawienia metody routingu ruchu**, dla **metody routingu** wybierz **wydajności**.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego, w tym profilu w następujący sposób:
        1. Wybierz odpowiednie **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik  */* . Aby monitorować punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem dla ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **zapisać**.
5.  Testowanie zmian w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu, wyszukaj nazwę profilu Menedżera ruchu, a następnie kliknij przycisk profilu usługi Traffic Manager w wynikach który wyświetlone.
    2.  W **Traffic Manager** bloku, kliknij opcję **omówienie**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzony profil Menedżera ruchu. Może to służyć przez wszystkich klientów (na przykład, przechodząc do niej przy użyciu przeglądarki sieci web) do pobrania kierowane do prawej punktu końcowego jako określana przez typ routingu. W takim przypadku wszystkie żądania są kierowane do punktu końcowego o najniższym opóźnieniu sieci klienta.
6. Po działa profilu Menedżera ruchu, należy edytować rekord DNS na z autorytatywnego serwera DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu wydajności przy użyciu Menedżera ruchu][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważone metody routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metody routingu priorytet](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficzne metody routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [Menedżera ruchu ustawienia testu](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png