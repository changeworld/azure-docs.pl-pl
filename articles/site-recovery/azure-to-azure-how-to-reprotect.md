---
title: Ponownej ochrony nie powiodła się na maszynach wirtualnych platformy Azure, do regionu podstawowego Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Opisuje sposób Włącz ponownie ochronę maszyn wirtualnych Azure w regionie pomocniczym, po przejściu w tryb failover regionu podstawowego, za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: rajanaki
ms.openlocfilehash: ccec4262297314bad261a852bb5db25c428ce0a0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ponownej ochrony nie powiodła się na maszynach wirtualnych platformy Azure, w regionie podstawowym


>[!NOTE]
>
> Replikacja usługi Site Recovery na potrzeby maszyn wirtualnych platformy Azure jest obecnie dostępna w wersji zapoznawczej.



Gdy możesz [awaryjnie](site-recovery-failover.md) maszynach wirtualnych platformy Azure z jednego regionu przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md), rozruchu maszyn wirtualnych w regionie pomocniczym, w stanie niechronionym. Niepowodzenie maszyn wirtualnych z powrotem do regionu podstawowego, należy wykonać następujące czynności:

- Włącz ponownie ochronę maszyn wirtualnych w regionie pomocniczym, dzięki czemu rozpoczęciu replikowanie w regionie podstawowym. 
- Po zakończeniu przełączonej i replikowania maszyn wirtualnych, użytkownik może przełączyć je z lokacji dodatkowej do regionu podstawowego.

> [!WARNING]
> Jeśli znasz [migracji](migrate-overview.md#what-do-we-mean-by-migration) maszyny z serwera podstawowego w regionie pomocniczym, przenieść maszynę Wirtualną do innej grupy zasobów albo usunąć maszyny Wirtualnej Azure, nie Włącz ponownie ochronę maszyny Wirtualnej lub jej wykonaj powrót po awarii.


## <a name="prerequisites"></a>Wymagania wstępne
1. Tryb failover maszyny Wirtualnej z serwera podstawowego do regionu pomocniczego muszą być zatwierdzone.
2. Lokacji głównej docelowej powinny być dostępne, i powinno być możliwe na uzyskiwanie dostępu lub tworzenie zasobów w tym regionie.

## <a name="reprotect-a-vm"></a>Włącz ponownie ochronę maszyny Wirtualnej

1. W **magazynu** > **elementy replikowane**, kliknij prawym przyciskiem myszy nieudane za pośrednictwem maszyny Wirtualnej i wybierz **ponownego włączenia ochrony**. Kierunek przełączonej powinny być wyświetlane z podstawowym dodatkowej. 

  ![Włącz ponownie ochronę](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Przejrzyj zestawy grupy, sieci, magazynu i dostępności zasobów. Następnie kliknij przycisk **OK**. Jeśli istnieją wszystkie zasoby oznaczone jako nowe, są one tworzone w ramach procesu zastosowania.
3. Zadanie przełączonej nasion lokacji docelowej przy użyciu najnowszych danych. Po zakończeniu który odbywa się replikacja różnicowa. Następnie możesz w trybie Failover do lokacji głównej. Można wybrać konta magazynu lub sieci, który ma być używany podczas Włącz ponownie ochronę, przy użyciu opcji dostosowywania.

  ![Dostosowywanie opcji](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Dostosowywanie ustawień ponownej ochrony

Można dostosować następujące właściwości elementu docelowego VMe podczas zastosowania.

![Dostosowywanie](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Właściwość |Uwagi  |
|---------|---------|
|Docelowa grupa zasobów     | Zmodyfikuj docelowa grupa zasobów, w którym utworzono maszynę Wirtualną. W ramach przełączonej docelowej maszyny Wirtualnej został usunięty. Można wybrać nową grupę zasobów, w którym chcesz utworzyć maszynę Wirtualną po pracy awaryjnej.        |
|Docelowy sieci wirtualnej     | Nie można zmienić sieci docelowej podczas wykonywania zadania ponownej ochrony. Aby zmienić sieci, ponów mapowania sieci.         |
|Docelowy magazyn (dodatkowej maszyny Wirtualnej nie używać dysków zarządzane)     | Można zmienić konta magazynu, które korzysta z maszyny Wirtualnej po pracy awaryjnej.         |
|Repliki zarządzane dysków (dodatkowej maszyna wirtualna używa dysków zarządzane)    | Usługa Site Recovery tworzy dyski replik zarządzanych w regionie podstawowym dublowanego dysków zarządzanych dodatkowej maszyny Wirtualnej.         | 
|Cache Storage     | Można określić konto magazynu pamięci podręcznej do użycia podczas replikacji. Domyślnie nowe konto magazynu pamięci podręcznej jest możliwe, jeśli nie istnieje.         |
|Zestaw dostępności     |Jeśli maszyna wirtualna w regionie pomocniczym jest częścią zestawu dostępności, możesz wybrać zbiór dostępności dla docelowej maszyny Wirtualnej w regionie podstawowym. Domyślnie usługi Site Recovery podejmie próbę odnalezienia istniejącego zestawu w regionie podstawowym dostępności i użyć go. Podczas dostosowywania można określić nowego zestawu dostępności.         |


### <a name="what-happens-during-reprotection"></a>Co się dzieje podczas zastosowania?

Domyślnie są następujące operacje:

1. Konto magazynu pamięci podręcznej jest tworzony w regionie podstawowym
2. Jeśli docelowe konto magazynu (oryginalnego konta magazynu w regionie podstawowym) nie istnieje, zostanie utworzony nowy. Nazwa konta magazynu przypisany jest nazwa konta magazynu używanego przez dodatkowej maszynę Wirtualną z "asr" sufiksem.
3. Jeśli maszyna wirtualna używa dysków zarządzanych, zarządzać repliki dyski są tworzone w regionie podstawowym do przechowywania danych replikowane z dysków dodatkowej maszyny Wirtualnej. 
4. Jeśli zestaw dostępności docelowy nie istnieje, nowy jest tworzony jako część zadania ponownej ochrony, w razie potrzeby. Jeśli dostosowano ustawienia przełączonej wybrany zestaw jest używany.

Gdy użytkownik zainicjuje zadanie ponownej ochrony i obiekt docelowy, który istnieje maszyna wirtualna, są następujące operacje:

1. Wymagane składniki są tworzone w ramach ponownej ochrony. Jeśli już istnieją, są ponownie.
2. Po stronie docelowej maszyny Wirtualnej jest wyłączone w przypadku działa.
3. Dysk maszyny Wirtualnej po stronie docelowej jest kopiowany przez usługę Site Recovery do kontenera, jako inicjatora obiektu blob.
4. Następnie jest usuwany po stronie docelowej maszyny Wirtualnej.
5. Inicjatora obiektu blob jest używane przez bieżące źródło po stronie (pomocniczy) maszyny Wirtualnej do replikacji. Dzięki temu, że są replikowane tylko różnice.
6. Istotne zmiany między dysku źródłowego i inicjatora obiektu blob są synchronizowane. Może to zająć trochę czasu.
7. Po zakończeniu zadania ponownej ochrony rozpoczyna się replikacja różnicowa i tworzy punkt odzyskiwania zgodnie z zasadami replikacji.
8. Po pomyślnym zainicjowaniu zadań ponownej ochrony, maszyna wirtualna przechodzi chronionych stanu.

## <a name="next-steps"></a>Kolejne kroki

Po włączeniu ochrony maszyny Wirtualnej, można zainicjować trybu failover. Tryb failover wyłączania maszyny Wirtualnej w regionie pomocniczym i tworzy i rozruchu maszyny Wirtualnej w regionie podstawowym z przestój mała. Firma Microsoft zaleca odpowiednio wybierz godzinę i uruchomienie test trybu failover, ale inicjowanie pełne trybu failover do lokacji głównej. [Dowiedz się więcej](site-recovery-failover.md) o pracy awaryjnej.

