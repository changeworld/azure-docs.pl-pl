---
title: Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) do oceny | Microsoft Docs
description: Dowiedz się, jak oszacować Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 3ab3164db7702566e62b2eedf2a9a03e9f7fb55c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109124"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) do oceny

Możesz oszacować Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) na niewielkiej liczbie maszyn wirtualnych platformy Azure lub na jednej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. Najłatwiejszym i najbardziej bezpośrednim sposobem na włączenie monitorowania jest Azure Portal. Twoje cele polegają na monitorowaniu maszyn wirtualnych i znalezieniu problemów z wydajnością lub dostępnością. 

Przed rozpoczęciem Sprawdź [wymagania wstępne](vminsights-enable-overview.md) i upewnij się, że Twoja subskrypcja i zasoby spełniają wymagania.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włączanie monitorowania pojedynczej maszyny wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **maszyn wirtualnych**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)** .

1. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w której wdrożono maszynę wirtualną w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z maszyny wirtualnej, zobacz [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne odczekanie około 10 minut, zanim będzie można wyświetlić metryki kondycji maszyny wirtualnej.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczego zestawu skalowania maszyn wirtualnych

Aby włączyć monitorowanie zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **Virtual Machine Scale Sets**.

3. Z listy wybierz zestaw skalowania maszyn wirtualnych.

4. Na stronie zestaw skalowania maszyn wirtualnych w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)** .

5. Na stronie **szczegółowe informacje (wersja zapoznawcza)** Jeśli chcesz użyć istniejącego obszaru roboczego log Analytics wybierz go z listy rozwijanej.

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której wdrożono maszynę wirtualną. 

    ![Włączanie Azure Monitor dla maszyn wirtualnych na potrzeby zestawu skalowania maszyn wirtualnych](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z zestawu skalowania maszyn wirtualnych, zobacz [tworzenie log Analytics obszaru roboczego](../learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne odczekanie około 10 minut, zanim będzie można wyświetlić dane monitorowania zestawu skalowania.

>[!NOTE]
>W przypadku korzystania z modelu skalowania ręcznego należy uaktualnić wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Po włączeniu monitorowania dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych informacje o monitorowaniu są dostępne do analizy w Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić odnalezione zależności aplikacji, zobacz [Use Azure monitor dla maszyn wirtualnych map](vminsights-maps.md). 
* Aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).