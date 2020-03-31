---
title: Wyłączanie monitorowania w usłudze Azure Monitor dla maszyn wirtualnych
description: W tym artykule opisano sposób zatrzymania monitorowania maszyn wirtualnych w usłudze Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480525"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Wyłączanie monitorowania maszyn wirtualnych w usłudze Azure Monitor dla maszyn wirtualnych

Po włączeniu monitorowania maszyn wirtualnych (maszyny wirtualne), można później wyłączyć monitorowanie w usłudze Azure Monitor dla maszyn wirtualnych. W tym artykule pokazano, jak wyłączyć monitorowanie dla co najmniej jednej maszyny wirtualnej.  

Obecnie usługa Azure Monitor dla maszyn wirtualnych nie obsługuje selektywnego wyłączania monitorowania maszyn wirtualnych. Obszar roboczy usługi Log Analytics może obsługiwać usługę Azure Monitor dla maszyn wirtualnych i innych rozwiązań. Może również zbierać inne dane monitorowania. Jeśli obszar roboczy usługi Usługi Log Analytics udostępnia te usługi, należy zrozumieć efekt i metody wyłączania monitorowania przed rozpoczęciem.

Usługa Azure Monitor dla maszyn wirtualnych opiera się na następujących składnikach, aby zapewnić jego środowisko:

* Obszar roboczy usługi Log Analytics, który przechowuje dane monitorowania z maszyn wirtualnych i innych źródeł.
* Kolekcja liczników wydajności skonfigurowanych w obszarze roboczym. Kolekcja aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych połączonych z obszarem roboczym.
* `VMInsights`, który jest rozwiązaniem do monitorowania skonfigurowanym w obszarze roboczym. To rozwiązanie aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `MicrosoftMonitoringAgent`i `DependencyAgent`, które są rozszerzeniami maszyn wirtualnych platformy Azure. Te rozszerzenia zbierają i wysyłają dane do obszaru roboczego.

Przygotowując się do wyłączenia monitorowania maszyn wirtualnych, należy pamiętać o następujących kwestiach:

* Jeśli została oceniona przy użyciu pojedynczej maszyny Wirtualnej i użyta wstępnie wybrana domyślna usługa Log Analytics, można wyłączyć monitorowanie, odinstalowując agenta zależności z maszyny Wirtualnej i odłączając agenta usługi Log Analytics od tego obszaru roboczego. Takie podejście jest odpowiednie, jeśli zamierzasz używać maszyny Wirtualnej do innych celów i później zdecydować, aby ponownie połączyć ją z innym obszarem roboczym.
* Jeśli wybrano istniejący wcześniej obszar roboczy usługi Log Analytics, który obsługuje inne rozwiązania do monitorowania i zbieranie danych z innych źródeł, można usunąć składniki rozwiązania z obszaru roboczego bez przerywania lub wpływu na obszar roboczy.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego, może nadal widzieć dane wydajności i mapy dla maszyn wirtualnych platformy Azure. Dane po pewnym czasie przestaną pojawiać się w widokach **Wydajność** i **Mapa.** Włącz **Enable** opcję będzie dostępna z wybranej maszyny Wirtualnej platformy Azure, dzięki czemu można ponownie włączyć monitorowanie w przyszłości.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Całkowite usunięcie usługi Azure Monitor dla maszyn wirtualnych

Jeśli nadal potrzebujesz obszaru roboczego usługi Log Analytics, wykonaj następujące kroki, aby całkowicie usunąć usługę Azure Monitor dla maszyn wirtualnych. Usuniesz `VMInsights` rozwiązanie z obszaru roboczego.  

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania lista filtruje sugestie na podstawie danych wejściowych. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy wybrany po włączeniu usługi Azure Monitor dla maszyn wirtualnych.
4. Po lewej stronie wybierz pozycję **Rozwiązania**.  
5. Na liście rozwiązań wybierz pozycję **VMInsights(nazwa obszaru roboczego).** Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Wyłączanie monitorowania i utrzymywanie obszaru roboczego  

Jeśli obszar roboczy usługi Log Analytics nadal musi obsługiwać monitorowanie z innych źródeł, wykonując następujące kroki, aby wyłączyć monitorowanie na maszynie wirtualnej, która została użyta do oceny usługi Azure Monitor dla maszyn wirtualnych. W przypadku maszyn wirtualnych platformy Azure usuniesz rozszerzenie maszyny Wirtualnej agenta zależności i rozszerzenie maszyny Wirtualnej agenta usługi Log Analytics dla systemu Windows lub Linux bezpośrednio z maszyny Wirtualnej. 

>[!NOTE]
>Nie usuwaj agenta usługi Log Analytics, jeśli: 
>
> * Usługa Azure Automation zarządza maszyną Wirtualną w celu organizowania procesów lub zarządzania konfiguracją lub aktualizacjami. 
> * Usługa Azure Security Center zarządza maszyną wirtualną w celu wykrywania zabezpieczeń i zagrożeń. 
>
> Jeśli usuniesz agenta usługi Log Analytics, uniemożliwisz tym usługom i rozwiązaniom proaktywne zarządzanie maszyną wirtualną. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. W portalu Azure wybierz pozycję **Maszyny wirtualne**. 
3. Z listy wybierz maszynę wirtualną. 
4. Po lewej stronie wybierz pozycję **Rozszerzenia**. Na stronie **Rozszerzenia** wybierz pozycję **DependencyAgent**.
5. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.
6. Na stronie **Rozszerzenia** wybierz pozycję **MicrosoftMonitoringAgent**. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.  
