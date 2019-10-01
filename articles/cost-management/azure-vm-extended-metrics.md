---
title: Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure | Microsoft Docs
description: Ten artykuł ułatwia włączanie i Konfigurowanie rozszerzonych metryk diagnostyki dla maszyn wirtualnych platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: e1d0beb6ced0d582166d556c1ae2fc17b375dddf
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695365"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Dodawanie rozszerzonych metryk dla usługi Azure Virtual Machines

Cloudyn używa danych metryk platformy Azure z maszyn wirtualnych platformy Azure, aby wyświetlić szczegółowe informacje na temat ich zasobów. Dane metryk, nazywane również licznikami wydajności, są używane przez Cloudyn do generowania raportów. Jednak Cloudyn nie zbiera automatycznie wszystkich danych metryk platformy Azure z maszyn wirtualnych gościa — należy włączyć zbieranie metryk. Ten artykuł pomaga w włączaniu i konfigurowaniu dodatkowych metryk diagnostyki dla maszyn wirtualnych platformy Azure.

Po włączeniu zbierania metryk można:

- Dowiedz się, kiedy maszyny wirtualne zbliżają się do pamięci, dysku i ograniczeń procesora CPU.
- Wykrywaj trendy użycia i anomalie.
- Kontroluj swoje koszty, zmieniając wielkość liter zgodnie z użyciem.
- Uzyskaj oszczędne zalecenia dotyczące optymalizacji rozmiarów z Cloudyn.

Na przykład może być konieczne monitorowanie procesora CPU% i pamięci maszyn wirtualnych platformy Azure. Metryki maszyny wirtualnej platformy Azure odpowiadają za użycie _procesora CPU_ i _\Memory @ No__t-2 zadeklarowanych bajtów_.

> [!NOTE]
> Rozszerzona zbieranie danych o metrykach jest obsługiwana tylko w przypadku monitorowania na poziomie gościa platformy Azure. Cloudyn nie jest zgodna z [agentem log Analytics](../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Ustalanie, czy rozszerzone metryki są włączone

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. W obszarze **maszyny wirtualne**wybierz maszynę wirtualną, a następnie w obszarze **monitorowanie**wybierz pozycję **metryki**. Zostanie wyświetlona lista dostępnych metryk.
3. Wybierz pewne metryki i Wykres wyświetla dla nich dane.  
    ![Przykładowa Metryka — procentowy procesor CPU hosta](./media/azure-vm-extended-metrics/metric01.png)

W powyższym przykładzie jest dostępny ograniczony zestaw metryk standardowych dla hostów, ale metryki pamięci nie są. Metryki pamięci są częścią rozszerzonych metryk. W takim przypadku rozszerzone metryki nie są włączone dla maszyny wirtualnej. Aby włączyć rozszerzone metryki, należy wykonać kilka dodatkowych kroków. Poniższe informacje przeprowadzi Cię przez ich włączenie.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Włącz rozszerzone metryki w Azure Portal

Metryki standardowe to metryki komputera hosta. _Procentową metryką procesora CPU_ jest jeden przykład. Istnieją również podstawowe metryki maszyn wirtualnych gościa i są one również nazywane metrykami rozszerzonymi. Przykłady rozszerzonych metryk obejmują _\Memory @ no__t-1 zadeklarowane bajty w użyciu_ i _\Memory\Available bajtów_.

Włączenie rozszerzonych metryk jest proste. Dla każdej maszyny wirtualnej Włącz monitorowanie na poziomie gościa. Po włączeniu monitorowania na poziomie gościa na maszynie wirtualnej jest instalowany Agent usługi Azure Diagnostics. Domyślnie zostanie dodany podstawowy zestaw rozszerzonych metryk. Następujący proces jest taki sam dla maszyn wirtualnych z systemami Windows i Linux.

Należy pamiętać, że monitorowanie na poziomie gościa na platformie Azure i Linux wymaga konta magazynu. Jeśli włączysz monitorowanie na poziomie gościa, jeśli nie wybierzesz istniejącego konta magazynu, zostanie ono utworzone dla Ciebie.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Włącz monitorowanie na poziomie gościa na istniejących maszynach wirtualnych

1. W **Virtual Machines**Wyświetl listę maszyn wirtualnych, a następnie wybierz maszynę wirtualną.
2. W obszarze **monitorowanie**wybierz pozycję **Ustawienia diagnostyczne**.
3. Na stronie Ustawienia diagnostyki kliknij pozycję **Włącz monitorowanie na poziomie gościa**.  
    0Enable monitorowanie poziomu gościa na stronie Przegląd @ no__t-1 @no__t
4. Po kilku minutach Agent diagnostyki Azure zostanie zainstalowany na maszynie wirtualnej. Zostanie dodany podstawowy zestaw metryk. Odśwież stronę. Dodane liczniki wydajności są wyświetlane na karcie Przegląd.
5. W obszarze monitorowanie wybierz pozycję **metryki**.
6. Na wykresie metryk w obszarze **przestrzeń nazw Metryka**wybierz pozycję **gość (klasyczny)** .
7. Na liście metryk można wyświetlić wszystkie dostępne liczniki wydajności dla maszyny wirtualnej gościa.  
    ![Lista przykładowych metryk rozszerzonych](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Włącz monitorowanie na poziomie gościa na nowych maszynach wirtualnych

Podczas tworzenia nowych maszyn wirtualnych na karcie Zarządzanie wybierz pozycję **włączone** dla **diagnostyki gościa systemu operacyjnego**.

![Ustaw diagnostykę systemu operacyjnego gościa na włączone](./media/azure-vm-extended-metrics/new-enable-diag.png)

Aby uzyskać więcej informacji na temat włączania rozszerzonych metryk dla maszyn wirtualnych platformy Azure, zobacz artykuł Omówienie [i korzystanie z usługi Azure Linux Agent](../virtual-machines/extensions/agent-linux.md) i [agenta maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Poświadczenia Menedżer zasobów

Po włączeniu rozszerzonych metryk upewnij się, że Cloudyn ma dostęp do [poświadczeń Menedżer zasobów](activate-subs-accounts.md). Twoje poświadczenia są wymagane do Cloudyn zbierania i wyświetlania danych wydajności dla maszyn wirtualnych. Są one również używane do tworzenia zaleceń dotyczących optymalizacji kosztów. Cloudyn potrzebuje co najmniej trzech danych wydajności z wystąpienia, aby określić, czy jest kandydatem do rekomendacji downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Włącz metryki maszyny wirtualnej za pomocą skryptu

Metryki maszyn wirtualnych można włączyć za pomocą skryptów Azure PowerShell. Jeśli masz wiele maszyn wirtualnych, na których chcesz włączyć metryki, możesz użyć skryptu w celu zautomatyzowania procesu. Przykładowe skrypty są w serwisie GitHub na [platformie Azure — Włączanie diagnostyki](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Wyświetl metryki wydajności platformy Azure

Aby wyświetlić metryki wydajności w wystąpieniach platformy Azure w portalu Cloudyn, przejdź do **zasobów** > **COMPUTE**@no__t-**3.** Na liście wystąpień maszyn wirtualnych rozwiń wystąpienie, a następnie rozwiń zasób, aby wyświetlić szczegóły.

![przykładowe informacje wyświetlane w Eksploratorze wystąpień](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli nie włączono jeszcze Azure Resource Manager dostępu do interfejsu API dla swoich kont, wybierz pozycję [Aktywuj subskrypcje i konta platformy Azure](activate-subs-accounts.md).
