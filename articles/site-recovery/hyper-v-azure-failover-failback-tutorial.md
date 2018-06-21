---
title: Tryb failover, a kończyć się niepowodzeniem z powrotem maszyn wirtualnych funkcji Hyper-V replikowany do platformy Azure z usługą Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działają maszyny wirtualne funkcji Hyper-V do platformy Azure i powrót po awarii do lokacji lokalnej, z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294296"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Tryb failover i powrotu po awarii maszyn wirtualnych funkcji Hyper-V zreplikowanej w systemie Azure

Ten przewodnik opisuje sposób trybu failover maszyny Wirtualnej funkcji Hyper-V do platformy Azure. Po możesz już tryb pracy awaryjnej, należy na powrót po awarii do lokacji lokalnej gdy jest ona dostępna. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny Wirtualnej funkcji Hyper-V, aby sprawdzić, który jest zgodny z wymaganiami platformy Azure
> * Przełączanie do trybu failover na platformie Azure
> * Powrót po awarii z platformy Azure do środowiska lokalnego
> * Replikacja odwrotna lokalnych maszyn wirtualnych, aby uruchomić ponownie replikację do platformy Azure

W tym samouczku jest piąty samouczek w serii. Przyjęto założenie, zostały już wykonane zadania w poprzednim samouczki.    

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md), lub [maszyn wirtualnych funkcji Hyper-V zarządzane w chmurach programu System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Przygotowanie do trybu failover i powrotu po awarii

Upewnij się, na Maszynie wirtualnej nie ma żadnych migawek i że lokalnej maszyny Wirtualnej jest wyłączona podczas powrotu po awarii. Pomaga zagwarantować spójność danych podczas replikacji. Nigdy nie włączaj lokalnych maszyn wirtualnych podczas powrotu po awarii. 

Tryb failover i powrotu po awarii ma trzy etapy:

1. **Tryb failover na platformie Azure**: maszyn wirtualnych funkcji Hyper-V trybu Failover z lokacji lokalnej na platformie Azure.
2. **Powrót po awarii do środowiska lokalnego**: pracy awaryjnej maszyn wirtualnych platformy Azure do lokacji lokalnej, gdy jest ona dostępna. Rozpoczyna replikację maszyn wirtualnych z powrotem do lokalnych maszyn wirtualnych funkcji Hyper-V. Po początkowej danych synchronizacji trybu failover maszyny wirtualne platformy Azure do lokacji lokalnej.  
3. **Replikacja odwrotna lokalnych maszyn wirtualnych**: po danych nie powiodło się ponownie, wstecznego replikowanie maszyn wirtualnych lokalnie, aby rozpocząć replikację je do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed trybu failover Sprawdź właściwości maszyny Wirtualnej i upewnij się, że maszyna wirtualna spełnia z [wymagania dotyczące usługi Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. W **chronione elementy**, kliknij przycisk **elementy replikowane** >< nazwę maszyny Wirtualnej >.

2. W **elementu zreplikowane** okienku przejrzyj informacje maszyny Wirtualnej, stan kondycji i najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
     - W **obliczenia i sieć**, można zmodyfikować ustawienia maszyny Wirtualnej i ustawień sieci, takich jak sieć/podsieć, w której maszyny Wirtualnej Azure zostaną umieszczone po trybu failover, a adres IP, które zostaną przypisane do niego. Dyski zarządzane nie są obsługiwane dla powrotu po awarii z platformy Azure do funkcji Hyper-V.
      - W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="failover-to-azure"></a>Tryb failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.
2. W **pracy awaryjnej** wybierz **najnowsze** punktu odzyskiwania. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje zamknięcie źródłowe maszyny wirtualne przed wyzwolenie pracy awaryjnej. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Po zweryfikowaniu trybu failover kliknij **zatwierdzić**. Usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie Anuluj trybu failover w toku**: Jeśli anulujesz w toku, zatrzymuje pracy awaryjnej, ale maszyny Wirtualnej będą replikowane ponownie.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Maszyny Wirtualnej Azure powrotu po awarii do lokalnego i odwrotnie replikować lokalne maszyny Wirtualnej

Operacja powrotu po awarii jest zasadniczo trybu failover z platformy Azure do lokacji lokalnej i w replikacja odwrotna ponownie rozpoczyna replikację maszyn wirtualnych z lokacji lokalnej do platformy Azure.

1. W **ustawienia** > **elementy replikowane**, kliknij maszynę Wirtualną > **planowanego trybu Failover**.
2. W **potwierdzić planowanego trybu Failover**Sprawdź kierunek pracy awaryjnej (na platformie Azure), a wybierz lokalizacja źródłowa i docelowa.
3. Wybierz **synchronizować dane przed pracy awaryjnej (należy zsynchronizować tylko zmiany różnicowe)**. Ta opcja minimalizuje przestojów maszyn wirtualnych, ponieważ synchronizacji bez zamykania maszyny Wirtualnej.
4. Inicjuj tryb failover. Możesz śledzić postęp trybu failover **zadania** kartę.
5. Po początkowej danych odbywa się synchronizacja i wszystko jest gotowe do zamykania maszyn wirtualnych platformy Azure kliknij **zadania** > planowanego trybu failover zadania nazwa-> **ukończenia pracy awaryjnej**. Go wyłączania maszyny Wirtualnej Azure, przesyła najnowsze zmiany w lokalnej instalacji programu i uruchamia lokalnej maszyny Wirtualnej.
6. Zaloguj się na lokalnej maszynie Wirtualnej, aby Sprawdź, czy jest dostępna, zgodnie z oczekiwaniami.
7. Lokalnej maszyny Wirtualnej znajduje się teraz w **zatwierdzania oczekującej** stanu. Kliknij przycisk **zatwierdzić**. Usuwa maszynach wirtualnych platformy Azure i jego dysków i przygotowuje lokalnej maszyny Wirtualnej do replikacji odwrotnej.
Aby rozpocząć replikację lokalnej maszyny Wirtualnej do platformy Azure, należy włączyć **odwrócić replikację**. Wyzwala replikacji zmiany różnicowe, które wystąpiły od maszyny Wirtualnej platformy Azure został wyłączony.  
