---
title: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w regionie podstawowym przy użyciu Azure Site Recovery | Microsoft Docs
description: Opisuje sposób ponownego włączania ochrony maszyn wirtualnych platformy Azure po przejściu w tryb failover, do regionu podstawowego, przy użyciu Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954183"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ponowne włączanie ochrony maszyn wirtualnych platformy Azure zakończonych niepowodzeniem w regionie podstawowym


Po przełączeniu maszyn wirtualnych platformy Azure w [tryb failover](site-recovery-failover.md) z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md)maszyny wirtualne są uruchamiane w regionie pomocniczym w stanie niechronionym. W przypadku powrotu po awarii maszyn wirtualnych do regionu podstawowego należy wykonać następujące czynności:

- Ponownie Chroń maszyny wirtualne w regionie pomocniczym, tak aby rozpoczynać replikację do regionu podstawowego.
- Po zakończeniu ponownych prób ochrony, gdy maszyny wirtualne są replikowane, można przełączać je w tryb failover z poziomu pomocniczego do regionu podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne
1. Należy zatwierdzić tryb failover maszyny wirtualnej z regionu podstawowego do pomocniczego.
2. Podstawowa lokacja docelowa powinna być dostępna i powinna mieć możliwość uzyskiwania dostępu do zasobów lub tworzenia ich w tym regionie.

## <a name="reprotect-a-vm"></a>Ponowne włączanie ochrony maszyny wirtualnej

1. W obszarze **magazyn** > **zreplikowane elementy**kliknij prawym przyciskiem myszy maszynę wirtualną w trybie failover, a następnie wybierz pozycję **Włącz ponownie ochronę**. Kierunek ochrony powinien być pokazywany z poziomu pomocniczego do podstawowego.

   ![Ponowne włączanie ochrony](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Przejrzyj grupę zasobów, Sieć, magazyn i zestawy dostępności. Następnie kliknij przycisk **OK**. Jeśli istnieją jakieś zasoby oznaczone jako nowe, są one tworzone w ramach procesu ponownego ochrony.
3. Zadanie odochrony odsiewa lokację docelową z najnowszymi danymi. Po zakończeniu tego procesu następuje replikacja różnicowa. Następnie można przejść w tryb failover z powrotem do lokacji głównej. Można wybrać konto magazynu lub sieć, która ma być używana podczas ponownego włączania ochrony, przy użyciu opcji Dostosuj.

   ![Opcja dostosowywania](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Dostosuj ustawienia ponownego włączania ochrony

Podczas ponownej ochrony można dostosować następujące właściwości docelowego VMe.

![Dostosuj](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Właściwość |Uwagi  |
|---------|---------|
|Docelowa Grupa zasobów     | Zmodyfikuj docelową grupę zasobów, w której jest tworzona maszyna wirtualna. W ramach ochrony, docelowa maszyna wirtualna jest usuwana. Możesz wybrać nową grupę zasobów, w której ma zostać utworzona maszyna wirtualna po zakończeniu pracy w trybie failover.        |
|Docelowa sieć wirtualna     | Nie można zmienić sieci docelowej podczas zadania ponownego włączania ochrony. Aby zmienić sieć, wykonaj ponownie mapowanie sieci.         |
|Magazyn docelowy (pomocnicza maszyna wirtualna nie używa dysków zarządzanych)     | Można zmienić konto magazynu używane przez maszynę wirtualną po zakończeniu pracy w trybie failover.         |
|Dyski zarządzane repliki (pomocnicza maszyna wirtualna korzysta z dysków zarządzanych)    | Site Recovery tworzy dyski zarządzane repliki w regionie podstawowym w celu dublowania dysków zarządzanych pomocniczej maszyny wirtualnej.         |
|Cache Storage     | Możesz określić konto magazynu pamięci podręcznej, które ma być używane podczas replikacji. Domyślnie zostanie utworzone nowe konto magazynu pamięci podręcznej, jeśli nie istnieje.         |
|Zestaw dostępności     |Jeśli maszyna wirtualna w regionie pomocniczym jest częścią zestawu dostępności, możesz wybrać zestaw dostępności dla docelowej maszyny wirtualnej w regionie podstawowym. Domyślnie program Site Recovery próbuje znaleźć istniejący zestaw dostępności w regionie podstawowym i korzystać z niego. Podczas dostosowywania można określić nowy zestaw dostępności.         |


### <a name="what-happens-during-reprotection"></a>Co się stanie w trakcie ochrony?

Domyślnie są wykonywane następujące czynności:

1. Konto magazynu pamięci podręcznej jest tworzone w regionie, w którym działa maszyna wirtualna w trybie failover.
2. Jeśli docelowe konto magazynu (oryginalne konto magazynu w regionie podstawowym) nie istnieje, zostanie utworzony nowy. Nazwa przypisanego konta magazynu to nazwa konta magazynu używanego przez pomocniczą maszynę wirtualną z sufiksem "ASR".
3. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, dyski zarządzane repliki są tworzone w regionie podstawowym w celu przechowywania danych replikowanych z dysków pomocniczej maszyny wirtualnej.
4. Jeśli docelowy zestaw dostępności nie istnieje, w razie potrzeby zostanie utworzony nowy program w ramach zadania ponownego włączania ochrony. Jeśli ustawienia ponownej ochrony zostały dostosowane, wybrany zestaw jest używany.

Gdy Wyzwalasz zadanie ponownego włączania ochrony i istnieje docelowa maszyna wirtualna, występują następujące sytuacje:

1. Maszyna wirtualna po stronie docelowej jest wyłączona, jeśli jest uruchomiona.
2. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, kopia oryginalnych dysków zostanie utworzona z sufiksem "-ASRReplica". Oryginalne dyski zostaną usunięte. Kopie "-ASRReplica" są używane na potrzeby replikacji.
3. Jeśli maszyna wirtualna używa dysków niezarządzanych, docelowe dyski danych maszyny wirtualnej są odłączone i używane do replikacji. Kopia dysku systemu operacyjnego jest tworzona i dołączona do maszyny wirtualnej. Oryginalny dysk systemu operacyjnego zostanie odłączony i użyty do replikacji.
4. Synchronizowane są tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Różnice są obliczane przez porównanie dysków, a następnie przetransferowane. Aby znaleźć szacowane informacje o czasie poniżej.
5. Po zakończeniu synchronizacji zostanie rozpoczęta replikacja różnicowa i zostanie utworzony punkt odzyskiwania w wierszu z zasadami replikacji.

Gdy Wyzwalasz zadanie ponownego włączania ochrony, a docelowa maszyna wirtualna i dyski nie istnieją, występują następujące sytuacje:
1. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, dyski repliki są tworzone przy użyciu sufiksu "-ASRReplica". Kopie "-ASRReplica" są używane na potrzeby replikacji.
2. Jeśli maszyna wirtualna korzysta z dysków niezarządzanych, na docelowym koncie magazynu są tworzone dyski replik.
3. Wszystkie dyski są kopiowane z obszaru przełączenia w tryb failover do nowego regionu docelowego.
4. Po zakończeniu synchronizacji zostanie rozpoczęta replikacja różnicowa i zostanie utworzony punkt odzyskiwania w wierszu z zasadami replikacji.

#### <a name="estimated-time--to-do-the-reprotection"></a>Szacowany czas na przeprowadzenie ochrony 

W większości przypadków Azure Site Recovery nie replikuje kompletnych danych do regionu źródłowego. Poniżej znajdują się warunki, które określają, ile danych zostanie zreplikowanych:

1.  Jeśli źródłowe dane maszyny wirtualnej zostaną usunięte, uszkodzone lub niedostępne z powodu pewnej przyczyny, takich jak zmiana/usunięcie grupy zasobów, a następnie po zakończeniu ponownej ochrony zostanie wykonane żadne dane, które nie są dostępne w regionie źródłowym do użycia.
2.  Jeśli źródłowe dane maszyny wirtualnej są dostępne, tylko różnice są obliczane przez porównanie dysków, a następnie przetransferowane. Zapoznaj się z poniższą tabelą, aby uzyskać szacowany czas 

|\* * Przykładowa sytuacja * * | \* * Czas poświęcony na ponowne włączenie ochrony * * |
|--- | --- |
|Region źródłowy ma 1 maszynę wirtualną z 1 TB dysku standardowego<br/>-Są używane tylko dane 127 GB, a reszta dysku jest pusta<br/>-Typ dysku jest standardem dla przepływności MiB/S 60<br/>-Brak zmian danych po przejściu w tryb failover| Przybliżony czas 45 minut – 1,5 godz.<br/> -Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną całych danych, co zajmie 127 GB/45 MB ~ 45 minut<br/>— Aby Site Recovery do skalowania automatycznego o 20-30 minut, wymagany jest pewien czas dodatkowy<br/>-Brak opłat za ruch wychodzący |
|Region źródłowy ma 1 maszynę wirtualną z 1 TB dysku standardowego<br/>-Są używane tylko dane 127 GB, a reszta dysku jest pusta<br/>-Typ dysku jest standardem dla przepływności MiB/S 60<br/>-45 GB zmian danych po przejściu w tryb failover| Przybliżony czas 1 godz. — 2 godziny<br/>-Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną całych danych, co zajmie 127 GB/45 MB ~ 45 minut<br/>-Czas transferu, aby zastosować zmiany z 45 GB, czyli 45 GB/45 MB/s ~ 17 minut<br/>-Opłaty za ruch wychodzący byłyby tylko dla danych 45 GB, a nie dla sumy kontrolnej|
 



## <a name="next-steps"></a>Następne kroki

Po włączeniu ochrony maszyny wirtualnej można zainicjować pracę w trybie failover. Tryb failover zamyka maszynę wirtualną w regionie pomocniczym, a następnie tworzy i uruchamia maszynę wirtualną w regionie podstawowym z niewielkim przestojem. Zalecamy wybranie czasu odpowiednio i przeprowadzenie testowego przejścia w tryb failover przed zainicjowaniem pełnej pracy w trybie failover w lokacji głównej. [Dowiedz się więcej](site-recovery-failover.md) o trybie failover.
