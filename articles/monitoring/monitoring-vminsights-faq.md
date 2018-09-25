---
title: Usługa Azure Monitor dla maszyn wirtualnych — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, która łączy kondycji i wydajności monitorowania systemu operacyjnego maszyny Wirtualnej platformy Azure, a także automatycznie odnajdywania składników aplikacji i zależności z innymi zasobami oraz mapuje komunikację między je. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2018
ms.author: magoedte
ms.openlocfilehash: 308a447ff99cd11ad6a28df0bdb515764b0f546b
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063459"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>Usługa Azure Monitor dla maszyn wirtualnych — często zadawane pytania
Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Azure Monitor dla maszyn wirtualnych na platformie Microsoft Azure. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Mogę dołączyć do istniejącego obszaru roboczego?
W przypadku maszyn wirtualnych są już połączone z obszarem roboczym usługi Log Analytics, mogą nadal używać tego obszaru roboczego podczas dołączania do usługi Azure Monitor w przypadku maszyn wirtualnych pod warunkiem, że jest w jednym z obsługiwanych regionów na liście [tutaj](monitoring-vminsights-onboard.md#prerequisites).

Podczas dołączania, firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego, który spowoduje, że wszystkie maszyny wirtualne raportowania danych do obszaru roboczego, aby rozpocząć zbieranie tych informacji do wyświetlania i analizy w usłudze Azure Monitor dla maszyn wirtualnych.  W rezultacie zobaczysz dane dotyczące wydajności z wszystkich maszyn wirtualnych połączonych z wybranym obszarem roboczym.  Funkcje kondycji i mapy tylko są włączone dla maszyn wirtualnych, które wybrano dołączyć.

Aby uzyskać więcej informacji o wydajności, które są włączone liczniki, zobacz nasze [dołączania](monitoring-vminsights-onboard.md) artykułu.

## <a name="can-i-onboard-to-a-new-workspace"></a>Mogę dołączyć do nowego obszaru roboczego? 
Jeśli maszyny wirtualne nie są aktualnie połączone do istniejącego obszaru roboczego usługi Log Analytics, musisz utworzyć nowy obszar roboczy do przechowywania danych.  Tworzenie nowego domyślnego obszaru roboczego odbywa się automatycznie w przypadku skonfigurowania jednej maszyny Wirtualnej platformy Azure dla usługi Azure Monitor dla maszyn wirtualnych za pośrednictwem witryny Azure portal.

Jeśli zdecydujesz się użyć metody opartych na skryptach, te kroki są objęte [dołączania](monitoring-vminsights-onboard.md) artykułu. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co należy zrobić, jeśli Moja maszyna wirtualna jest już raportowania do istniejącego obszaru roboczego?
Jeśli są już zbieranie danych z maszyn wirtualnych, może już skonfigurowano je raportować dane do istniejącego obszaru roboczego usługi Log Analytics.  Tak długo, jak ten obszar roboczy znajduje się w jednym z naszych obsługiwane regiony, do tego istniejącego obszaru roboczego można włączyć usługi Azure Monitor dla maszyn wirtualnych.  Jeśli obszar roboczy, z którego korzystasz już nie jest w jednym z naszych obsługiwane regiony, będzie niemożliwe do dołączenia do usługi Azure Monitor dla maszyn wirtualnych w tej chwili.  Pracujemy nad do obsługi dodatkowych regionów.

>[!NOTE]
>Firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego, który ma wpływ na wszystkie maszyny wirtualne, które raportują do obszaru roboczego, czy została wybrana do dołączenia ich do usługi Azure Monitor dla maszyn wirtualnych. Aby uzyskać szczegółowe informacje na temat sposobu konfiguracji liczników wydajności dla obszaru roboczego, można znaleźć na naszej [dokumentacji](../log-analytics/log-analytics-data-sources-performance-counters.md). Informacji na temat liczników skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych, można znaleźć na naszej [dostęp do dokumentacji dołączania](monitoring-vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Dlaczego moja maszyna wirtualna nie dołączania?
Podczas dołączania maszyny Wirtualnej platformy Azure w witrynie Azure portal, wykonywane są następujące kroki:

* Domyślnego obszaru roboczego usługi Log Analytics zostanie utworzony, jeśli wybrano opcję.
* Liczniki wydajności są skonfigurowane dla wybranego obszaru roboczego. Jeśli ten krok nie powiedzie się, można zauważyć, że niektóre wykresy wydajności i tabele nie są wyświetlane dane dla maszyny Wirtualnej została dołączona. Można to naprawić, uruchamiając skrypt programu PowerShell udokumentowane [tutaj](monitoring-vminsights-onboard.md#enable-with-powershell).
* Agenta usługi Log Analytics jest zainstalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej, jeśli ustalił, że jest to wymagane.  
* Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych jest zainstalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, jeśli ustalił, że jest to wymagane.  
* Obsługa funkcji kondycji składników platformy Azure Monitor są skonfigurowane, jeśli to konieczne, a maszyna wirtualna jest skonfigurowana do danych raportu o kondycji.

Podczas procesu dołączania, możemy sprawdzić stan każdego z powyższych próbę zwrócenia stanu powiadomień do Ciebie w portalu.  Konfiguracja obszaru roboczego i podczas instalacji agenta zwykle trwa 5 – 10 minut.  Wyświetlanie danych monitorowania i kondycji w portalu wykonaj dodatkowe 5 do 10 minut.  

Jeśli zainicjowano dołączania i wyświetlić komunikaty wskazujący, że maszyna wirtualna musi być dołączona umożliwiają do 30 minut dla maszyny Wirtualnej ukończyć proces. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę żadnych danych w przypadku wykresów wydajności dla mojej maszyny Wirtualnej
Jeśli nie widzisz dane dotyczące wydajności w tabeli dysku lub niektóre wykresy wydajności liczniki wydajności nie może być skonfigurowany w obszarze roboczym. Aby rozwiązać problem, uruchom następujące polecenie [skrypt programu PowerShell](monitoring-vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Czym różni się usługi Azure Monitor dla funkcji mapy maszyn wirtualnych z mapy usługi?
Usługi Azure Monitor dla funkcji mapy maszyn wirtualnych jest oparty na mapie usługi, ale się w następujący sposób:

* Widok mapy jest możliwy z bloku maszyny Wirtualnej i z usługi Azure Monitor dla maszyn wirtualnych w ramach usługi Azure Monitor.
* Połączenia na mapie teraz kliknąć, aby wyświetlić dane metryk połączenia w panelu po stronie dla wybranego połączenia.
* Brak nowego interfejsu API, który jest używany do tworzenia mapy w celu skuteczniejszej obsługi bardziej złożone mapy.
* Monitorowane maszyny wirtualne znajdują się teraz w węźle grupy klienta i wykres pierścieniowy zawiera część monitorowanych vs niemonitorowanych maszyn wirtualnych w grupie.  Jego można również filtrować listę maszyn, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne znajdują się teraz w węzłach grupy portów serwera, a wykres pierścieniowy zawiera część monitorowanych vs niemonitorowanych maszyn w grupie.  Jego można również filtrować listę maszyn, gdy grupa jest rozwinięta.
* Zaktualizowano style mapy są bardziej spójne z mapy aplikacji z usługi Application insights.
* Panele po stronie zostały zaktualizowane, ale jeszcze nie masz pełny zestaw integracji firmy, były obsługiwane w rozwiązania Service Map — zarządzanie aktualizacjami, śledzenie zmian, zabezpieczeń i pomocy technicznej. 
* Opcja dotyczące wybierania grup i komputerów do mapowania została zaktualizowana i obsługuje teraz subskrypcji, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usług w chmurze.
* Nie można utworzyć nowych grup składników mapy usług maszyny w usłudze Azure Monitor dla funkcji mapy maszyn wirtualnych.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego mój wykresy wydajności są wyświetlane linie kropkowane?

Może to wystąpić z kilku powodów.  W przypadkach, w przypadku, gdy występuje przerwa w zbieraniu danych Firma Microsoft będzie przedstawiać wiersze jako kropkami.  Jeśli zmodyfikowano częstotliwość próbkowania danych liczników wydajności, włączone (ustawienie domyślne jest do zbierania danych co 60 sekund), można wyświetlić linii kropkowanej wykresu wybierz zakres czasu wąskie wykresu i Twoje częstotliwość próbkowania jest mniejsza niż użyte w wykresie rozmiaru przedziału (na przykład częstotliwość próbkowania wynosi 10 minut i każdego przedziału na wykresie wynosi 5 minut).  Wybieranie szerszy zakres czasu, aby wyświetlić powinno spowodować linii wykresu, w tym przypadku były wyświetlane jako linia ciągła, a nie kropki.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupy obsługujące z usługą Azure Monitor dla maszyn wirtualnych?
Funkcja wydajności obsługuje grupy na podstawie wyróżnione zasobów w ramach określonego obszaru roboczego, a także grupowanie oparte na zestawie skalowania określonej maszyny wirtualnej platformy Azure i usługą w chmurze.

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak wyświetlić szczegóły dla czego zależy używany 95. percentyl zagregowanego wydajności wykresy liniowe?
Domyślnie lista jest sortowana Wam maszyn wirtualnych, które mają najwyższą wartość 95. percentyl dla wybranej metryki, z wyjątkiem wykres dostępnej pamięci, który pokazuje maszyn o najniższej wartości 5. percentyl.  Kliknięcie na wykresie spowoduje otwarcie **lista N najlepszych** widok z odpowiednią wybraną metryką.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkcja mapy obsługuje zduplikowane adresy IP w różnych sieciach wirtualnych i podsieci?
Jeśli są duplikowania zakresów adresów IP przy użyciu maszyn wirtualnych lub maszyn wirtualnych zestawów skalowania w podsieci i sieci wirtualne, może to spowodować usługi Azure Monitor dla maszyn wirtualnych mapę, aby wyświetlić nieprawidłowe informacje. Jest to znany problem i Rozpatrujemy różne opcje, aby ulepszyć to środowisko.

## <a name="does-map-feature-support-ipv6"></a>Jest mapowany obsługi różnych funkcji IPv6?
Funkcja mapy aktualnie obsługuje tylko IPv4, dlatego Rozpatrujemy Obsługa protokołu IPv6. Obsługujemy również IPv4, który tunel wewnątrz protokołu IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Po załadowaniu mapę dla grupy zasobów lub innych dużych mapy jest trudny do wyświetlenia
Gdy wprowadziliśmy ulepszenia do mapy do obsługi dużych i złożonych konfiguracji Zdajemy sobie sprawę, mapy może mieć wiele węzłów, połączeń i języka node działa jako klaster.  Ponadto zobowiązujemy się do zwiększenia pomocy technicznej w celu zwiększenia skalowalności w dalszym ciągu.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego Wykres sieci na karcie Wydajność wygląda inaczej niż wykresu sieci, na stronie Omówienie maszyny Wirtualnej platformy Azure?

Strona przeglądu Maszynie wirtualnej platformy Azure Wyświetla wykresy oparte na hosta pomiaru działań w maszynie Wirtualnej gościa.  Wykresu sieci na omówienie maszyny Wirtualnej platformy Azure tylko Wyświetla ruch sieciowy będą naliczane.  Obejmuje to ruch między sieciami wirtualnymi.  Dane i wykresy wyświetlane dla usługi Azure Monitor dla maszyn wirtualnych na podstawie danych z maszyny Wirtualnej gościa i wykresach sieci są wyświetlane wszystkie TCP/IP ruchu przychodzącego i wychodzącego do tej maszyny Wirtualnej, w tym między sieciami wirtualnymi.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [dołączanie usługi Azure Monitor dla maszyn wirtualnych](monitoring-vminsights-onboard.md) , aby zrozumieć wymagania i metody, aby włączyć monitorowanie maszyn wirtualnych.
