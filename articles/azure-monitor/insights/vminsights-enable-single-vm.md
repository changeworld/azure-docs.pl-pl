---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych w witrynie Azure portal
description: Dowiedz się, jak oceniać usługę Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub na zestawie skalowania maszyny wirtualnej.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480712"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych w witrynie Azure portal

W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych na niewielkiej liczbie maszyn wirtualnych platformy Azure przy użyciu witryny Azure portal. Twoim celem jest monitorowanie maszyn wirtualnych i odkryć wszelkie problemy z wydajnością lub dostępnością. 

Przed rozpoczęciem zapoznaj się z [wymaganiami wstępnymi](vminsights-enable-overview.md) i upewnij się, że subskrypcja i zasoby spełniają wymagania.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włączanie monitorowania dla pojedynczej maszyny Wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny Wirtualnej platformy Azure:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz **opcję Maszyny wirtualne**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie Maszyny Wirtualnej w sekcji **Monitorowanie** wybierz pozycję **Insights,** a następnie **Włącz**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na stronie **Dołączania usługi Azure Monitor Insights,** jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Lista wstępnieselektywa domyślnego obszaru roboczego i lokalizacji, w której maszyna wirtualna jest wdrażana w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, zobacz [Tworzenie obszaru roboczego usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy usługi Log Analytics musi należeć do jednego z [obsługiwanych regionów.](vminsights-enable-overview.md#log-analytics)

6. Podczas wykonywania konfiguracji będą odbierane komunikaty o stanie.

    ![Włącz usługę Azure Monitor dla przetwarzania wdrażania maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczego zestawu skalowania maszyny wirtualnej

Aby włączyć monitorowanie zestawu skalowania maszyny wirtualnej platformy Azure:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Wybierz pozycję **Zestawy skalowania maszyny wirtualnej**.

3. Z listy wybierz zestaw skalowania maszyny wirtualnej.

4. Na stronie zestaw skalowania maszyny wirtualnej w sekcji **Monitorowanie** wybierz pozycję **Insights,** a następnie **Włącz**.

5. Jeśli **Insights** chcesz użyć istniejącego obszaru roboczego usługi Log Analytics, wybierz go na liście rozwijanej.

    Lista wstępnieselektywa domyślnego obszaru roboczego i lokalizacji, do których maszyna wirtualna jest wdrażana w ramach subskrypcji. 

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla zestawu skalowania maszyny wirtualnej](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z zestawu skalowania maszyny [wirtualnej,](../learn/quick-create-workspace.md)zobacz Tworzenie obszaru roboczego usługi Log Analytics . Obszar roboczy usługi Log Analytics musi należeć do jednego z [obsługiwanych regionów.](vminsights-enable-overview.md#log-analytics)

6. Podczas wykonywania konfiguracji będą odbierane komunikaty o stanie.

    >[!NOTE]
    >Jeśli używasz modelu ręcznego uaktualniania dla zestawu skalowania, uaktualnij wystąpienia, aby zakończyć konfigurację. Uaktualnienia można uruchomić na stronie **Wystąpienia** w sekcji **Ustawienia.**
    
    ![Włącz usługę Azure Monitor dla przetwarzania wdrażania maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Teraz, gdy włączono monitorowanie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, informacje o monitorowaniu są dostępne do analizy w usłudze Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić odnalezione zależności aplikacji, zobacz [Korzystanie z usługi Azure Monitor dla map maszyn wirtualnych](vminsights-maps.md). 
* Aby zidentyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny Wirtualnej platformy Azure.](vminsights-performance.md)
