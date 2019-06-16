---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) w wersji ewaluacyjnej | Dokumentacja firmy Microsoft
description: Informacje dotyczące oceny usługi Azure Monitor dla maszyn wirtualnych, na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122497"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) w wersji ewaluacyjnej

Możesz ocenić usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) na niewielką liczbę maszyn wirtualnych (VM) lub w jednej skali maszyny Wirtualnej lub maszyny wirtualnej. Jest najłatwiejszym i najbardziej bezpośrednim sposobem Włączanie monitorowania w witrynie Azure portal. Celem użytkownika jest monitorowanie maszyn wirtualnych i odnajdywanie problemów dotyczących jakości lub problemy z dostępnością. 

Przed rozpoczęciem należy przejrzeć [wymagania wstępne](vminsights-enable-overview.md) i upewnić się, że Twoja subskrypcja i zasobów i wymagań.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włącz monitorowanie dla pojedynczej maszyny Wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny Wirtualnej platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **maszyn wirtualnych**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)** .

1. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Lista preselects domyślnego obszaru roboczego i lokalizację, w którym wdrożono maszynę Wirtualną w subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, zobacz [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy usługi Log Analytics musi należeć do jednej z [obsługiwane regiony](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne Odczekaj około 10 minut, zanim będzie można wyświetlić metryki kondycji dla maszyny Wirtualnej.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczej maszyny wirtualnej zestawu skalowania

Aby włączyć monitorowanie zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **Virtual Machine Scale Sets**.

3. Z listy wybierz zestaw skalowania maszyn wirtualnych.

4. Na maszynie wirtualnej zestawu skalowania strony w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)** .

5. Na **Insights (wersja zapoznawcza)** strony, jeśli chcesz użyć istniejącego obszaru roboczego usługi Log Analytics, wybierz go na liście rozwijanej.

    Lista preselects domyślnego obszaru roboczego i lokalizacji, w której wdrożono maszynę Wirtualną do w ramach subskrypcji. 

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla zestawu skalowania maszyn wirtualnych](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z zestawu skalowania maszyn wirtualnych, zobacz [Utwórz obszar roboczy usługi Log Analytics](../learn/quick-create-workspace.md). Obszar roboczy usługi Log Analytics musi należeć do jednej z [obsługiwane regiony](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne Odczekaj około 10 minut, zanim będzie można wyświetlić dane monitorowania dla zestawu skalowania.

>[!NOTE]
>Jeśli używasz ręczne modelu uaktualniania dla zestawu skalowania należy uaktualnić wystąpienia, aby ukończyć instalację. Można uruchomić uaktualnienia z **wystąpień** stronie **ustawienia** sekcji.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Teraz, gdy zostało włączone, monitorowanie dla maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania, monitorowania informacje są dostępne dla analizy w usłudze Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [poznanie kondycji maszynach wirtualnych platformy Azure Monitor](vminsights-health.md). 
* Aby obejrzeć zależności odnalezionych aplikacji, zobacz [użycia usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). 
* Aby zidentyfikować wąskie gardła, ogólnego użycia i wydajności maszyny Wirtualnej, zobacz [maszyny Wirtualnej platformy Azure w widoku wydajności](vminsights-performance.md).