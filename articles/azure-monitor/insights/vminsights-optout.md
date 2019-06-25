---
title: Wyłącz monitorowanie w usłudze Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zatrzymać monitorowanie maszyn wirtualnych w usłudze Azure Monitor dla maszyn wirtualnych.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155686"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Wyłącz monitorowanie maszyn wirtualnych w usłudze Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Po włączeniu monitorowania maszyn wirtualnych (VM), możesz później wyłączyć monitorowanie w usłudze Azure Monitor dla maszyn wirtualnych. W tym artykule pokazano, jak wyłączyć monitorowanie dla przynajmniej jednej maszyny wirtualnej.  

Obecnie usługa Azure Monitor dla maszyn wirtualnych nie obsługuje selektywnego wyłączania monitorowania maszyn wirtualnych. Obszar roboczy usługi Log Analytics może obsługiwać usługi Azure Monitor dla maszyn wirtualnych i innych rozwiązań. Może ona również zbierać innych danych monitorowania. Obszar roboczy usługi Log Analytics udostępnia te usługi, należy zapoznać się z wpływu i wyłączanie monitorowania przed rozpoczęciem korzystania z metody.

Usługa Azure Monitor dla maszyn wirtualnych zależy od następujących składników, aby zapewnić jej obsługę:

* Obszaru roboczego usługi Log Analytics, która przechowuje dane monitorowania z maszyn wirtualnych i innych źródeł.
* Zbiór liczników wydajności, które skonfigurowano w obszarze roboczym. Kolekcja aktualizacji konfiguracji monitorowania na wszystkie maszyny wirtualne połączone z obszarem roboczym.
* `InfrastructureInsights` i `ServiceMap`, którego monitorowanie rozwiązań są skonfigurowane w obszarze roboczym. Te rozwiązania aktualizacji konfiguracji monitorowania na wszystkie maszyny wirtualne połączone z obszarem roboczym.
* `MicrosoftMonitoringAgent` i `DependencyAgent`, które są rozszerzenia maszyny Wirtualnej platformy Azure. Te rozszerzenia wysyłają i zbierają dane do obszaru roboczego.

Podczas przygotowywania Wyłącz monitorowanie maszyn wirtualnych, pamiętać o tych zagadnień:

* Jeśli obliczane przy użyciu pojedynczej maszyny Wirtualnej i używać wstępnie wybrane domyślnego obszaru roboczego usługi Log Analytics, można wyłączyć monitorowanie przez odinstalowanie agenta zależności z maszyny Wirtualnej i odłączanie agenta usługi Log Analytics z tego obszaru roboczego. To podejście jest odpowiednie, jeśli zamierzasz używać maszyny Wirtualnej do innych celów i później w celu ponownego nawiązania połączenia z innym obszarem roboczym.
* Jeśli wybrano opcję istniejąca obszar roboczy usługi Log Analytics, który obsługuje inne rozwiązania do monitorowania i zbierania danych z innych źródeł, możesz usunąć składniki rozwiązania z obszaru roboczego, bez zakłócania pracy lub wywierania wpływu na obszar roboczy.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego, może nadal wyświetlany stan kondycji z maszynami wirtualnymi portalu Azure; w szczególności będzie wydajności i mapowanie danych po przejściu na którymś z widoków w portalu. Dane po pewnym czasie zostanie zatrzymane, znajdujących się w **wydajności** i **mapy** widoków. Ale **kondycji** widoku będą w dalszym ciągu Pokaż stan kondycji dla maszyn wirtualnych. **Wypróbuj teraz usługę** opcja będzie dostępna w wybranej maszyny wirtualnej platformy Azure, dzięki czemu można ponownie włączyć monitorowania w przyszłości.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Całkowite usuwanie usługi Azure Monitor dla maszyn wirtualnych

Jeśli nadal potrzebujesz obszaru roboczego usługi Log Analytics, wykonaj następujące kroki, aby całkowicie usunąć usługi Azure Monitor dla maszyn wirtualnych. Możesz usunąć `InfrastructureInsights` i `ServiceMap` rozwiązania z obszaru roboczego.  

>[!NOTE]
>Jeśli używasz mapy usługi, zanim usługa Azure Monitor jest włączony dla maszyn wirtualnych i nadal polegać na niej rozwiązanie do monitorowania, nie usuwaj tego rozwiązania, zgodnie z opisem w ostatnim kroku poniższej procedury.  
>

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania, listy filtrów sugestii, w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy została wybrana opcja po włączeniu usługi Azure Monitor dla maszyn wirtualnych.
4. Po lewej stronie, wybierz **rozwiązania**.  
5. Na liście rozwiązania wybierz **InfrastructureInsights (nazwa obszaru roboczego)** . Na **Przegląd** strony dla rozwiązania wybierz opcję **Usuń**. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**.  
6. Na liście rozwiązania wybierz **ServiceMap (nazwa obszaru roboczego)** . Na **Przegląd** strony dla rozwiązania wybierz opcję **Usuń**. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**.  

Przed włączeniem usługi Azure Monitor w przypadku maszyn wirtualnych, jeśli nie [liczniki wydajności są zbierane](vminsights-enable-overview.md#performance-counters-enabled) dla maszyn wirtualnych Windows lub Linux w obszarze roboczym, [wyłączenie tych zasad](../platform/data-sources-performance-counters.md#configuring-performance-counters) dla Windows i Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Wyłącz monitorowanie i Zachowaj obszar roboczy  

Jeśli obszar roboczy usługi Log Analytics jest nadal musi do obsługi monitorowania z innych źródeł, wykonaj następujące czynności, aby wyłączyć monitorowanie na maszynę Wirtualną, która zostanie użyta do oceny usługi Azure Monitor dla maszyn wirtualnych. W przypadku maszyn wirtualnych platformy Azure, usuniesz agenta zależności rozszerzenia maszyny Wirtualnej i rozszerzenia maszyny Wirtualnej agenta usługi Log Analytics dla Windows lub Linux bezpośrednio z poziomu maszyny Wirtualnej. 

>[!NOTE]
>Nie usuwaj agenta usługi Log Analytics, jeśli: 
>
> * Usługa Azure Automation zarządza maszyny Wirtualnej do koordynowania procesów lub zarządzać konfiguracji lub aktualizacji. 
> * Usługa Azure Security Center zarządza maszyny Wirtualnej na potrzeby zabezpieczeń i wykrywanie zagrożeń. 
>
> Jeśli usuniesz agenta usługi Log Analytics, uniemożliwi tych usług i rozwiązań z proaktywne Zarządzanie maszyną Wirtualną. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
3. Z listy wybierz maszynę wirtualną. 
4. Po lewej stronie, wybierz **rozszerzenia**. Na **rozszerzenia** wybierz opcję **DependencyAgent**.
5. Na stronie właściwości rozszerzenia, wybierz **Odinstaluj**.
6. Na **rozszerzenia** wybierz opcję **MicrosoftMonitoringAgent**. Na stronie właściwości rozszerzenia, wybierz **Odinstaluj**.  
