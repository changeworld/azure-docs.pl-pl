---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) w wersji ewaluacyjnej | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć usługi Azure Monitor dla maszyn wirtualnych dla pojedynczej maszyny wirtualnej platformy Azure lub zestawu do celów ewaluacyjnych skalowania maszyn wirtualnych.
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524088"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) w wersji ewaluacyjnej

Do oceny usługi Azure Monitor w przypadku maszyn wirtualnych (wersja zapoznawcza) na niewielką liczbę maszyn wirtualnych platformy Azure lub jedną maszynę wirtualną lub zestawu skalowania maszyn wirtualnych, najprostszy i najbardziej bezpośrednim sposobem Włącz monitorowanie jest w witrynie Azure portal. Po zakończeniu tego procesu zostanie pomyślnie rozpoczęto monitorowanie ich i Dowiedz się, jeśli występują one wszelkie problemy z wydajnością lub dostępnością. 

Było wprowadzenie pracę, zapoznaj się z [wymagania wstępne](vminsights-enable-overview.md) i sprawdź, subskrypcji i zasobów spełniają wymagania.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włącz monitorowanie dla pojedynczej maszyny Wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **maszyn wirtualnych**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.

1. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Lista preselects domyślnego obszaru roboczego i lokalizacji, która została wdrożona maszyna wirtualna, aby w ramach subskrypcji. 

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) w jednym z obsługiwanych regionów na liście [tutaj](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić metryki kondycji dla maszyny wirtualnej.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczej maszyny wirtualnej zestawu skalowania

Aby włączyć monitorowanie usługi Azure zestawu skalowania maszyn wirtualnych w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **Virtual Machine Scale Sets**.

3. Z listy wybierz zestaw skalowania maszyn wirtualnych.

4. Na maszynie wirtualnej zestawu skalowania strony w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.

5. Na **Insights (wersja zapoznawcza)** strony, jeśli masz istniejący obszar roboczy usługi Log Analytics chcesz użyć, wybierz go na liście rozwijanej.

    Lista preselects domyślnego obszaru roboczego i lokalizacji, która została wdrożona maszyna wirtualna, aby w ramach subskrypcji. 

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla zestawu skalowania maszyn wirtualnych](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../learn/quick-create-workspace.md) w jednym z obsługiwanych regionów na liście [tutaj](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić dane monitorowania dla zestawu skalowania.

>[!NOTE]
>Jeśli używasz ręczne modelu uaktualniania dla zestawu skalowania należy uaktualnić wystąpienia, aby ukończyć instalację.  Można to zrobić na stronie wystąpień, w obszarze **ustawienia** sekcji.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy monitorowanie jest włączone dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). Aby zidentyfikować wąskie gardła i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [wydajność maszyny Wirtualnej platformy Azure w widoku](vminsights-performance.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md).