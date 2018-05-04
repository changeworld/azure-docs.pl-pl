---
title: Dodaj rozszerzoną metryki dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga włączyć i skonfigurować rozszerzone diagnostyki metryki na maszynach wirtualnych platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Dodaj rozszerzoną metryki dla maszyn wirtualnych platformy Azure

Kosztów zarządzania wykorzystuje metryki danych Azure w maszynach wirtualnych platformy Azure, aby pokazać szczegółowe informacje na temat ich zasobów. Metryki danych, nazywane również liczniki wydajności, jest używane przez koszt zarządzania do generowania raportów. Jednak koszt zarządzania nie automatycznie zebranie wszystkich danych metryki Azure z maszyn wirtualnych gościa, należy włączyć metryki kolekcji. Ten artykuł pomaga Włączanie i konfigurowanie dodatkowych diagnostyczne metryki na maszynach wirtualnych platformy Azure.

Po włączeniu kolekcji metryki, można:

- Wiedzieć, kiedy maszyn wirtualnych zbliżają się ich pamięci, dysku i procesora CPU limity.
- Wykryj trendy użycia i anomalii.
- Kontrolowanie kosztów za zmiany rozmiaru w zależności od użycia.
- Pobierz koszt wprowadzenia zmiany rozmiaru zaleceń optymalizację kosztów zarządzania.

Na przykład można monitorować procent użycia procesora CPU i pamięci % maszynach wirtualnych platformy Azure. Metryki maszyny Wirtualnej Azure odpowiadają _[Host] procent Procesora_ i _procent pamięci [gościa]_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Sprawdź, czy metryki są włączone na maszynach wirtualnych

1. Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.
2. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną, a następnie w obszarze **monitorowanie**, wybierz pozycję **metryki**. Jest wyświetlana lista dostępnych metryk.
3. Wybierz niektóre metryki, a wykres przedstawia dane dla nich.  
    ![Przykład metryka — host procent Procesora](./media/azure-vm-extended-metrics/metric01.png)

W powyższym przykładzie ograniczony zestaw standardowych metryki są dostępne dla hostów, ale metryk pamięci nie są. Metryki pamięci są częścią rozszerzonej metryki. Należy wykonać kilka dodatkowych kroków, aby włączyć rozszerzoną metryki. Poniższe informacje przeprowadzi Cię przez ich włączeniem.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Włączanie rozszerzonej metryki w portalu Azure

Standardowa metryki są metryki komputera hosta. _[Host] procent Procesora_ metryka jest jednym z przykładów. Istnieją również podstawowe metryki dla maszyn wirtualnych gościa i są również nazywane rozszerzonej metryki. Przykłady rozszerzonej metryki _procent pamięci [gościa]_ i _dostępnej pamięci [gościa]_.

Włączanie rozszerzonej metryki jest prosta. Dla każdej maszyny Wirtualnej Aby włączyć monitorowanie poziomie gościa. Po włączeniu monitorowania na poziomie gościa agent Azure diagnostics jest zainstalowany na maszynie Wirtualnej. Następujący proces jest taka sama dla klasycznego i regularnego maszyn wirtualnych i taki sam dla systemu Windows i maszyn wirtualnych systemu Linux.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Aby włączyć monitorowanie poziomie gościa na istniejące maszyny wirtualne

1. W **maszyn wirtualnych**, wyświetlić listę maszyn wirtualnych, a następnie wybierz Maszynę wirtualną.
2. W obszarze **monitorowanie**, wybierz pozycję **metryki**.
3. Kliknij przycisk **ustawień diagnostycznych**.
4. Na stronie ustawienia diagnostyki kliknij **Włącz monitorowanie na poziomie gościa**. Maszyny wirtualne systemu Linux wymaga istniejącego konta magazynu. Jeśli nie wybierzesz konta magazynu dla maszyny Wirtualnej systemu Windows, co tworzona jest dla Ciebie.  
    ![Włącz monitorowanie na poziomie gościa](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Po kilku minutach agent Azure diagnostics jest zainstalowany na maszynie Wirtualnej. Odśwież stronę i na liście dostępne metryki został zaktualizowany o metryki gościa.  
    ![Rozszerzone metryk](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aby włączyć monitorowanie poziomie gościa na nowe maszyny wirtualne

Podczas tworzenia nowych maszyn wirtualnych, upewnij się, że wybrano **Diagnostyka systemu operacyjnego gościa**. Maszyny wirtualne systemu Linux wymaga istniejącego konta magazynu. Jeśli nie wybierzesz konta magazynu dla maszyny Wirtualnej systemu Windows, co tworzona jest dla Ciebie.

![Włącz diagnostykę systemu operacyjnego gościa](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Poświadczenia usługi Resource Manager

Po włączeniu rozszerzonej metryki, upewnij się, że koszt zarządzania ma dostęp do Twojego [poświadczenia Menedżera zasobów](activate-subs-accounts.md). Poświadczenia są wymagane do zarządzania koszt do gromadzenia i wyświetlić dane wydajności dla maszyn wirtualnych. Są również używane do tworzenia zaleceń optymalizację kosztów. Co najmniej trzy dni danych wydajności z wystąpienia w celu określenia, czy jest kandydatem do zalecenie redukcję zatrudnienia wymagania w zakresie zarządzania kosztów.

## <a name="enable-vm-metrics-with-a-script"></a>Włączyć metryki maszyny Wirtualnej za pomocą skryptu

Można włączyć metryki maszyny Wirtualnej za pomocą skryptów programu PowerShell systemu Azure. Jeśli masz wiele maszyn wirtualnych, które chcesz włączyć metryki na służy skrypt do automatyzacji procesu. Przykładowe skrypty znajdują się w witrynie GitHub pod [włączyć diagnostyki Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Metryki wydajności Azure widoku

Aby wyświetlić metryki wydajności na swoich wystąpień Azure w portalu Cloudyn, przejdź do **zasoby** > **obliczeniowe** > **Explorer wystąpienia**. Na liście wystąpień maszyn wirtualnych rozwiń węzeł wystąpienia, a następnie rozwiń zasobów, aby wyświetlić szczegóły.

![Wystąpienie Eksploratora](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie zostało jeszcze włączone dostępu do interfejsu API Menedżera zasobów Azure dla kont, przejdź do [Azure aktywowania subskrypcji i konta](activate-subs-accounts.md)
