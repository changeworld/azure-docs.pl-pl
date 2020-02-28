---
title: Wyłącz monitorowanie w Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano sposób zatrzymania monitorowania maszyn wirtualnych w programie Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: fb4347e610920380792a17bb620e6d97a7d72505
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669509"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Wyłącz monitorowanie maszyn wirtualnych w Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Po włączeniu monitorowania maszyn wirtualnych można później wyłączyć monitorowanie w programie Azure Monitor dla maszyn wirtualnych. W tym artykule pokazano, jak wyłączyć monitorowanie dla co najmniej jednej maszyny wirtualnej.  

Obecnie Azure Monitor dla maszyn wirtualnych nie obsługuje selektywnego wyłączania monitorowania maszyn wirtualnych. Obszar roboczy Log Analytics może obsługiwać Azure Monitor dla maszyn wirtualnych i inne rozwiązania. Może również zbierać inne dane monitorowania. Jeśli obszar roboczy Log Analytics zawiera te usługi, należy zrozumieć efekt i metody wyłączenia monitorowania przed rozpoczęciem.

Azure Monitor dla maszyn wirtualnych opiera się na następujących składnikach, aby zapewnić ich środowisko pracy:

* Obszar roboczy Log Analytics, w którym są przechowywane dane monitorowania z maszyn wirtualnych i innych źródeł.
* Kolekcja liczników wydajności skonfigurowanych w obszarze roboczym. Kolekcja aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `InfrastructureInsights` i `ServiceMap`, które są rozwiązaniami monitorującymi skonfigurowanymi w obszarze roboczym. Te rozwiązania aktualizują konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `MicrosoftMonitoringAgent` i `DependencyAgent`, które są rozszerzeniami maszyn wirtualnych platformy Azure. Te rozszerzenia zbierają i przesyłają dane do obszaru roboczego.

Przygotowując się do wyłączenia monitorowania maszyn wirtualnych, pamiętaj o następujących kwestiach:

* Jeśli oceniasz pojedynczą maszynę wirtualną i korzystasz z prewybieranego domyślnego obszaru roboczego Log Analytics, możesz wyłączyć monitorowanie przez odinstalowanie agenta zależności z maszyny wirtualnej i odłączenie agenta Log Analytics z tego obszaru roboczego. Ta metoda jest odpowiednia, jeśli zamierzasz użyć maszyny wirtualnej do innych celów i podjąć decyzję później, aby ponownie połączyć ją z innym obszarem roboczym.
* W przypadku wybrania istniejącego obszaru roboczego Log Analytics, który obsługuje inne rozwiązania monitorowania i zbieranie danych z innych źródeł, można usunąć składniki rozwiązania z obszaru roboczego bez przerywania ani wpływu na obszar roboczy.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego może być nadal widoczny stan kondycji maszyn wirtualnych platformy Azure. w tym celu zobaczysz dane dotyczące wydajności i mapy po przejściu do dowolnego widoku w portalu. Dane zostaną ostatecznie zatrzymane w widokach **wydajność** i **Mapa** . Jednak widok **kondycji** będzie nadal przedstawiał stan kondycji maszyn wirtualnych. Opcja **Wypróbuj teraz** będzie dostępna na wybranej maszynie wirtualnej platformy Azure, dzięki czemu można ponownie włączyć monitorowanie w przyszłości.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Usuń Azure Monitor dla maszyn wirtualnych całkowicie

Jeśli nadal potrzebujesz obszaru roboczego Log Analytics, wykonaj następujące kroki, aby całkowicie usunąć Azure Monitor dla maszyn wirtualnych. W obszarze roboczym zostaną usunięte rozwiązania `InfrastructureInsights` i `ServiceMap`.  

>[!NOTE]
>Jeśli korzystasz z rozwiązania do monitorowania Service Map przed włączeniem Azure Monitor dla maszyn wirtualnych i nadal polegasz na nim, nie usuwaj tego rozwiązania zgodnie z opisem w ostatnim kroku poniższej procedury.  
>

1. Zaloguj się do [Azure portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy wybrany podczas włączania Azure Monitor dla maszyn wirtualnych.
4. Po lewej stronie wybierz pozycję **rozwiązania**.  
5. Na liście rozwiązań wybierz pozycję **InfrastructureInsights (nazwa obszaru roboczego)** . Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.  
6. Na liście rozwiązań wybierz pozycję **ServiceMap (nazwa obszaru roboczego)** . Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.  

Przed włączeniem Azure Monitor dla maszyn wirtualnych, jeśli nie [zebrano liczników wydajności](vminsights-enable-overview.md#performance-counters-enabled) dla maszyn wirtualnych opartych na systemie Windows lub Linux w obszarze roboczym, [Wyłącz te reguły](../platform/data-sources-performance-counters.md#configuring-performance-counters) dla systemów Windows i Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Wyłącz monitorowanie i Zachowaj obszar roboczy  

Jeśli obszar roboczy Log Analytics nadal musi obsługiwać monitorowanie z innych źródeł, wykonaj następujące kroki, aby wyłączyć monitorowanie na maszynie wirtualnej użytej do oszacowania Azure Monitor dla maszyn wirtualnych. W przypadku maszyn wirtualnych platformy Azure należy usunąć rozszerzenie maszyny wirtualnej agenta zależności oraz rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Windows lub Linux bezpośrednio z maszyny wirtualnej. 

>[!NOTE]
>Nie usuwaj agenta Log Analytics, jeśli: 
>
> * Azure Automation zarządza maszyną wirtualną w celu organizowania procesów lub zarządzania konfiguracją lub aktualizacjami. 
> * Azure Security Center zarządza maszyną wirtualną w celu zapewnienia bezpieczeństwa i wykrywania zagrożeń. 
>
> Jeśli usuniesz agenta Log Analytics, uniemożliwisz tym usługom i rozwiązaniom aktywne zarządzanie maszyną wirtualną. 

1. Zaloguj się do [Azure portal](https://portal.azure.com). 
2. W Azure Portal wybierz pozycję **Virtual Machines**. 
3. Z listy wybierz maszynę wirtualną. 
4. Po lewej stronie wybierz pozycję **rozszerzenia**. Na stronie **rozszerzenia** wybierz pozycję **DependencyAgent**.
5. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.
6. Na stronie **rozszerzenia** wybierz pozycję **MicrosoftMonitoringAgent**. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.  
