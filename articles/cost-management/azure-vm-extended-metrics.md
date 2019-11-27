---
title: Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia włączanie i konfigurowanie metrykę diagnostyki rozszerzonej na maszynach wirtualnych platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
ms.custom: seodec18
ms.openlocfilehash: ebbdd89d3ef41d4fb40197cbd83038b5cbc02073
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230142"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure

Rozwiązanie Cloudyn korzysta metryki danych platformy Azure z maszynami wirtualnymi portalu Azure, aby pokazać, że szczegółowe informacje na temat ich zasobów. Dane metryk, nazywany również liczników wydajności, jest używany przez firmę Cloudyn do generowania raportów. Jednak Cloudyn nie automatycznie zbierać wszystkie dane metryk platformy Azure z maszyny wirtualnej gościa, należy włączyć zbieranie metryk. Ten artykuł ułatwia włączanie i konfigurowanie diagnostyki dodatkowych metryk dla maszyn wirtualnych platformy Azure.

Po włączeniu zbierania metryk, możesz wykonywać następujące czynności:

- Wiadomo, kiedy maszyny wirtualne zbliżają się ich pamięci, dysku i limity procesora CPU.
- Wykrywanie trendów użycia i anomalii.
- Kontrolować ponoszone koszty według rozmiaru, od użycia.
- Uzyskaj koszt skuteczne, ustalanie rozmiaru zaleceń optymalizacji Cloudyn.

Na przykład możesz chcieć Monitoruj procent użycia procesora CPU i procent pamięci w maszynach wirtualnych platformy Azure. Metryki maszyny wirtualnej platformy Azure odpowiadają za _procent użycia procesora CPU_ i _\Memory\% zadeklarowanych bajtów_.

> [!NOTE]
> Zbieranie rozszerzonych danych metryk jest obsługiwany tylko przy użyciu funkcji platformy Azure monitorowania na poziomie gościa. Cloudyn nie jest zgodna z [agentem log Analytics](../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Czy są włączone rozszerzonych metryk

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. W obszarze **maszyny wirtualne**wybierz maszynę wirtualną, a następnie w obszarze **monitorowanie**wybierz pozycję **metryki**. Jest wyświetlana lista dostępnych metryk.
3. Wybierz niektóre metryki, a wykres przedstawia dane dla nich.  
    ![Przykładowe metryki — host procentowe użycie procesora CPU](./media/azure-vm-extended-metrics/metric01.png)

W powyższym przykładzie ograniczony zestaw standardowych metryk są dostępne dla hostów, ale metryk pamięci nie są. Metryki pamięci są częścią pakietu rozszerzonych metryk. W tym przypadku rozszerzonych metryk nie są włączone dla maszyny Wirtualnej. Należy wykonać dodatkowe kroki w celu włączenia rozszerzonych metryk. Poniższe informacje przeprowadzi Cię przez włączenie ich.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Włącz rozszerzone metryki w witrynie Azure portal

Metryki standardowe są metryki komputera hosta. _Procentową metryką procesora CPU_ jest jeden przykład. Dostępne są również podstawowe metryki dla maszyn wirtualnych gościa i są również nazywane rozszerzonych metryk. Przykłady rozszerzonych metryk obejmują _\Memory\% zadeklarowane bajty w użyciu_ i _\Memory\Available b_.

Włączanie rozszerzonych metryk jest bardzo proste. Dla każdej maszyny Wirtualnej Włącz monitorowanie na poziomie gościa. Po włączeniu monitorowania na poziomie gościa agent diagnostyki platformy Azure jest zainstalowany na maszynie Wirtualnej. Domyślnie są dodawane podstawowego zestawu rozszerzonych metryk. Następujący proces jest taki sam dla maszyn wirtualnych klasycznych i regularnego i taki sam dla Windows i maszyn wirtualnych systemu Linux.

Należy pamiętać, że zarówno platformy Azure, jak i Linux monitorowania na poziomie gościa wymagają konta magazynu. Po włączeniu monitorowania poziomie gościa, jeśli nie wybierzesz istniejące konto magazynu, a następnie jedną zostanie utworzony.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Włącz monitorowanie na poziomie dla gości na istniejących maszynach wirtualnych

1. W **Virtual Machines**Wyświetl listę maszyn wirtualnych, a następnie wybierz maszynę wirtualną.
2. W obszarze **monitorowanie**wybierz pozycję **Ustawienia diagnostyczne**.
3. Na stronie Ustawienia diagnostyki kliknij pozycję **Włącz monitorowanie na poziomie gościa**.  
    ![włączyć monitorowania na poziomie gościa na stronie przeglądu](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Po kilku minutach agenta funkcji Diagnostyka Azure jest zainstalowany na maszynie Wirtualnej. Podstawowy zestaw metryk są dodawane. Odśwież stronę. Dodano liczników są wyświetlane na karcie Przegląd.
5. W obszarze monitorowanie wybierz pozycję **metryki**.
6. Na wykresie metryk w obszarze **przestrzeń nazw Metryka**wybierz pozycję **gość (klasyczny)** .
7. Na liście metryki można wyświetlić wszystkich dostępnych liczników wydajności na maszynie Wirtualnej gościa.  
    ![Lista przykład rozszerzonych metryk](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aby włączyć monitorowanie poziomie gościa, na nowych maszynach wirtualnych

Podczas tworzenia nowych maszyn wirtualnych na karcie Zarządzanie wybierz pozycję **włączone** dla **diagnostyki gościa systemu operacyjnego**.

![Ustaw Diagnostyka systemu operacyjnego gościa na](./media/azure-vm-extended-metrics/new-enable-diag.png)

Aby uzyskać więcej informacji na temat włączania rozszerzonych metryk dla maszyn wirtualnych platformy Azure, zobacz artykuł Omówienie [i korzystanie z usługi Azure Linux Agent](../virtual-machines/extensions/agent-linux.md) i [agenta maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Poświadczenia usługi Resource Manager

Po włączeniu rozszerzonych metryk upewnij się, że Cloudyn ma dostęp do [poświadczeń Menedżer zasobów](activate-subs-accounts.md). Poświadczenia są wymagane do rozwiązania Cloudyn do zbierania i przedstawia dane wydajności dla maszyn wirtualnych. Są również używane do tworzenia zaleceń dotyczących optymalizacji kosztów. Cloudyn musi mieć co najmniej trzy dni danych dotyczących wydajności z wystąpienia, aby ustalić, czy jest kandydatem do zalecenia redukcję zatrudnienia.

## <a name="enable-vm-metrics-with-a-script"></a>Włącz metryki maszyny Wirtualnej przy użyciu skryptu

Można włączyć metryki maszyny Wirtualnej za pomocą skryptów programu Azure PowerShell. W przypadku wielu maszyn wirtualnych, które chcesz włączyć metryki na można użyć skryptu, aby zautomatyzować proces. Przykładowe skrypty są w serwisie GitHub na [platformie Azure — Włączanie diagnostyki](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobacz metryki wydajności platformy Azure

Aby wyświetlić metryki wydajności w wystąpieniach platformy Azure w portalu Cloudyn, przejdź do **zasobów** > **obliczeniowe** > **Eksplorator wystąpień**. Na liście wystąpień maszyn wirtualnych Rozwiń wystąpienie, a następnie rozwiń zasobów, aby wyświetlić szczegóły.

![Przykładowe informacje wyświetlane w Eksploratorze wystąpienia](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli nie włączono jeszcze Azure Resource Manager dostępu do interfejsu API dla swoich kont, wybierz pozycję [Aktywuj subskrypcje i konta platformy Azure](activate-subs-accounts.md).
