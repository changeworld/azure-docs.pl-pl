---
title: Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) do oceny | Microsoft Docs
description: Dowiedz się, jak oszacować Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/09/2019
ms.openlocfilehash: 1182f48d2d05c90cc90b1832f9305001dd2d1211
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553821"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) do oceny

Możesz oszacować Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) na niewielkiej liczbie maszyn wirtualnych platformy Azure lub na jednej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. Najłatwiejszym i najbardziej bezpośrednim sposobem na włączenie monitorowania jest Azure Portal. Twoje cele polegają na monitorowaniu maszyn wirtualnych i znalezieniu problemów z wydajnością lub dostępnością. 

Przed rozpoczęciem Sprawdź [wymagania wstępne](vminsights-enable-overview.md) i upewnij się, że Twoja subskrypcja i zasoby spełniają wymagania.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włączanie monitorowania pojedynczej maszyny wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz **Virtual Machines**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)** .

1. Na stronie **Insights (wersja zapoznawcza)** wybierz pozycję **Wypróbuj teraz**.

    ![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Na stronie Dołączanie do usługi **Azure monitor Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w której wdrożono maszynę wirtualną w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z maszyny wirtualnej, zobacz [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne odczekanie około 10 minut, zanim będzie można wyświetlić metryki kondycji maszyny wirtualnej.

![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczego zestawu skalowania maszyn wirtualnych

Aby włączyć monitorowanie zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wybierz **Virtual Machine Scale Sets**.

3. Z listy wybierz zestaw skalowania maszyn wirtualnych.

4. Na stronie zestaw skalowania maszyn wirtualnych w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)** .

5. Na stronie **szczegółowe informacje (wersja zapoznawcza)** Jeśli chcesz użyć istniejącego obszaru roboczego log Analytics wybierz go z listy rozwijanej.

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której wdrożono maszynę wirtualną. 

    ![Włączanie Azure Monitor dla maszyn wirtualnych na potrzeby zestawu skalowania maszyn wirtualnych](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z zestawu skalowania maszyn wirtualnych, zobacz [tworzenie log Analytics obszaru roboczego](../learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne odczekanie około 10 minut, zanim będzie można wyświetlić dane monitorowania zestawu skalowania.

>[!NOTE]
>W przypadku korzystania z modelu skalowania ręcznego należy uaktualnić wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .

![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Po włączeniu monitorowania dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych informacje o monitorowaniu są dostępne do analizy w Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak korzystać z funkcji kondycji, zobacz temat [Omówienie kondycji maszyn wirtualnych Azure monitor](vminsights-health.md). 
* Aby wyświetlić odnalezione zależności aplikacji, zobacz [Use Azure monitor dla maszyn wirtualnych map](vminsights-maps.md). 
* Aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).