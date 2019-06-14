---
title: Konfigurowanie metody routingu ruchu wydajności przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager na potrzeby kierowania ruchu do punktu końcowego z najniższym opóźnieniem
services: traffic-manager
manager: twooley
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 5e9b02a4145d86b86ea3ba0d509d06b7c148cc6d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048463"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurowanie metody routingu ruchu wydajności

Metody routingu ruchu wydajności pozwala kierować ruch do punktu końcowego zapewnia najniższe opóźnienie sieci klienta. Zazwyczaj centrum danych, która zapewnia najniższe opóźnienie jest najbardziej geograficznej odległości. Ta metoda routingu ruchu sieciowego nie można uwzględniać w czasie rzeczywistym zmian w konfiguracji sieci lub obciążenia.

##  <a name="to-configure-performance-routing-method"></a>Aby skonfigurować metody routingu opartego na wydajności

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profile usługi Traffic Manager** a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu dla.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy obecne są usługami w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**, a następnie w **konfiguracji** bloku, podaj następujące:
    1. Aby uzyskać **ustawienia metody routingu ruchu**, dla **Metoda routingu** wybierz **wydajności**.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego w ramach tego profilu, w następujący sposób:
        1. Wybierz odpowiedni **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik */* . Monitorowanie punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **Zapisz**.
5.  Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, a następnie kliknij pozycję profil usługi Traffic Manager w taki sposób, w wynikach, wyświetlana.
    2.  W **usługi Traffic Manager** bloku profilu, kliknij przycisk **Przegląd**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzonego profilu usługi Traffic Manager. Może to służyć przez dowolnego klienta (na przykład, przechodząc do niego w przeglądarce sieci web) Pobierz kierowane do prawego punktu końcowego jako ustalany na podstawie typu routingu. W takim przypadku wszystkie żądania są kierowane do punktu końcowego zapewnia najniższe opóźnienie sieci klienta.
6. Po profilu usługi Traffic Manager działa, należy edytować rekord DNS na autorytatywny serwer DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ruchu wydajności przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png