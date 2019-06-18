---
title: Jak wyłączyć monitorowanie za pomocą usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wycofana monitorowanie maszyn wirtualnych z usługą Azure Monitor dla maszyn wirtualnych.
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
ms.openlocfilehash: 0f35ea3e35277ee7f1afd8278a31f45ed20c6995
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522119"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Jak wyłączyć monitorowanie maszyn wirtualnych z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Jeśli po włączeniu monitorowania maszyn wirtualnych zdecydujesz, że nie chcesz monitorować je z usługą Azure Monitor dla maszyn wirtualnych, można wyłączyć monitorowanie. Ten artykuł pokazuje, jak to zrobić dla jednego lub wielu maszyn wirtualnych.  

Obecnie usługa Azure Monitor dla maszyn wirtualnych nie obsługuje selektywnego wyłączania monitorowania maszyn wirtualnych. Jeśli obszar roboczy usługi Log Analytics jest skonfigurowany do obsługi tego rozwiązania i innych rozwiązań, a także zbierać innych danych monitorowania, ważne jest, że rozumiesz wpływ i metod opisanych poniżej przed kontynuowaniem.

Usługa Azure Monitor dla maszyn wirtualnych zależy od następujących składników, aby zapewnić jej obsługę:

* Obszar roboczy usługi Log Analytics, która przechowuje dane monitorowania zbierane z maszyn wirtualnych i innych źródeł.
* Zbiór liczników wydajności, które skonfigurowano w obszarze roboczym aktualizacji konfiguracji monitorowania dla wszystkich maszyn wirtualnych połączone z obszarem roboczym.
* Dwa rozwiązania do monitorowania skonfigurowanych w obszarze roboczym - **InfrastructureInsights** i **ServiceMap**, ponieważ aktualizacja konfiguracji monitorowania dla wszystkich maszyn wirtualnych połączonych z obszarem roboczym.
* Dwa rozszerzenia maszyny wirtualnej platformy Azure, **MicrosoftMonitoringAgent** i **DependencyAgent**, które wysyłają i zbierają dane do obszaru roboczego.

Podczas przygotowywania wyłączyć monitorowanie maszyn wirtualnych z usługą Azure Monitor dla maszyn wirtualnych, należy wziąć pod uwagę następujące czynności:

* Jeśli oceniasz z jednej maszyny Wirtualnej i zaakceptowane wstępnie wybrane domyślnego obszaru roboczego usługi Log Analytics, można wyłączyć monitorowanie przez odinstalowanie agenta zależności z maszyny Wirtualnej i odłączanie agenta usługi Log Analytics z tego obszaru roboczego. To podejście jest odpowiednie, jeśli mają zostać przy użyciu maszyny Wirtualnej do innych celów, a później w celu ponownego nawiązania połączenia z innym obszarem roboczym.
* Jeśli obszar roboczy usługi Log Analytics używają do obsługi innych rozwiązań monitorowania i zbierania danych z innych źródeł, można usunąć usługi Azure Monitor składników rozwiązania maszyn wirtualnych w obszarze roboczym, bez przerw i wpływ do swojego obszaru roboczego.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego, może być nadal widoczny stan kondycji z maszynami wirtualnymi portalu Azure; w szczególności mapy danych o wydajności i po przejściu do widoku, albo w portalu. Dane po pewnym czasie zostanie zatrzymane, pojawiają się w widoku wydajności i mapy po pewnym czasie; Widok kondycji będą jednak nadal pokazują stan kondycji dla maszyn wirtualnych. **Wypróbuj teraz usługę** opcja będzie dostępna w wybranej maszyny wirtualnej platformy Azure, aby możliwe było ponowne włączenie monitorowania w przyszłości.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Całkowite usunięcie monitora platformy Azure dla maszyn wirtualnych

Poniżej opisano sposób całkowicie usunąć usługi Azure Monitor dla maszyn wirtualnych, jeśli nadal potrzebujesz obszaru roboczego usługi Log Analytics. Zamierzasz usunąć **InfrastructureInsights** i **ServiceMap** rozwiązania z obszaru roboczego.  

>[!NOTE]
>Jeśli były używane rozwiązanie przed włączeniem usługi Azure Monitor dla maszyn wirtualnych do monitorowania mapy usługi i nadal polegać na niej, nie usuwaj tego rozwiązania zgodnie z opisem w kroku 6 poniżej.  
>

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz usługi Log Analytics. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych usługi Log Analytics, wybierz obszar roboczy, gdy wybrano dołączania do usługi Azure Monitor dla maszyn wirtualnych.
4. W okienku po lewej stronie wybierz **rozwiązania**.  
5. Na liście rozwiązań, wybierz **InfrastructureInsights (nazwa obszaru roboczego)** , a następnie na **Przegląd** stronie rozwiązania, kliknij przycisk **Usuń**.  Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.  
6. Wybierz z listy rozwiązań **ServiceMap (nazwa obszaru roboczego)** , a następnie na **Przegląd** stronie rozwiązania, kliknij przycisk **Usuń**.  Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.  

Jeśli przed dołączania do usługi Azure Monitor dla maszyn wirtualnych, możesz nie [zbierania liczników wydajności, włączone](vminsights-enable-overview.md#performance-counters-enabled) Windows lub Linux maszyn wirtualnych z systemem w obszarze roboczym, należy wyłączyć te zasady, wykonując kroki opisane [tutaj](../platform/data-sources-performance-counters.md#configuring-performance-counters) dla Windows i Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Wyłącz monitorowanie dla maszyny Wirtualnej platformy Azure i zachowywanie obszaru roboczego  

W poniższych krokach opisano sposób wyłączania monitorowania dla maszyny wirtualnej, który został włączony do oceny usługi Azure Monitor w przypadku maszyn wirtualnych, ale obszar roboczy usługi Log Analytics jest nadal wymagane do obsługi monitorowania z innych źródeł. Jeśli jest to Maszynie wirtualnej platformy Azure, zamierzasz usunąć agenta zależności rozszerzenia maszyny Wirtualnej i agenta usługi Log Analytics rozszerzenia maszyny Wirtualnej dla systemu Windows/Linux bezpośrednio z poziomu maszyny Wirtualnej. 

>[!NOTE]
>Jeśli maszyna wirtualna jest zarządzana przez usługi Azure Automation do koordynowania procesów, zarządzanie konfiguracją, zarządzanie aktualizacjami lub zarządzanych przez usługę Azure Security Center dla zabezpieczeń zarządzania i wykrywania zagrożeń, nie można usunąć agenta usługi Log Analytics. W przeciwnym razie możesz uniemożliwi tych usług i rozwiązań proaktywne Zarządzanie maszyną Wirtualną. 

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 
2. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
3. Z listy wybierz maszynę wirtualną. 
4. W okienku po lewej stronie wybierz **rozszerzenia** i **rozszerzenia** wybierz opcję **DependencyAgent**.
5. Na stronie właściwości rozszerzenia, kliknij **Odinstaluj**.
6. Na **rozszerzenia** wybierz opcję **MicrosoftMonitoringAgent** i na stronie właściwości rozszerzenia, kliknij **Odinstaluj**.  
