---
title: Zarządzanie interfejsów sieciowych w usłudze Azure Site Recovery do środowiska lokalnego do platformy Azure replikacji | Dokumentacja firmy Microsoft
description: Opisuje sposób zarządzania interfejsów sieciowych do środowiska lokalnego do platformy Azure replikacji za pomocą usługi Azure Site Recovery
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: cf16a0e67aaf2d2c73649adccabf3e5eb0540bd3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-replication"></a>Zarządzanie interfejsy sieciowe maszyny wirtualnej do środowiska lokalnego do platformy Azure replikacji

Maszyna wirtualna (VM) na platformie Azure, musi mieć co najmniej jeden interfejs sieciowy do niego dołączony. Może mieć jako wiele sieci interfejsy do niego dołączony jako obsługuje rozmiar maszyny Wirtualnej.

Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej platformy Azure jest zdefiniowany jako podstawowy interfejs sieciowy. Wszystkie inne interfejsy sieciowe w maszynie wirtualnej są dodatkowych interfejsów sieciowych. Również domyślnie cały ruch wychodzący z maszyny wirtualnej jest wysyłane adres IP, który jest przypisany do podstawowej konfiguracji IP interfejsu sieci podstawowej.

W środowisku lokalnym maszyn wirtualnych lub serwerach może mieć wiele interfejsów sieciowych do różnych sieci w środowisku. Różnych sieciach są zwykle używane do wykonywania określonej operacji, takich jak uaktualnień, konserwacją i dostępem do Internetu. Podczas migracji lub przechodzenie w tryb failover na platformie Azure ze środowiska lokalnego, należy pamiętać, że interfejsy sieciowe w tej samej maszyny wirtualnej musi połączone z tej samej sieci wirtualnej.

Domyślnie program Azure Site Recovery tworzy jako wiele sieci interfejsy na maszynie wirtualnej platformy Azure, jak są podłączone do serwera lokalnego. Można uniknąć, tworzenie interfejsów sieciowych nadmiarowe podczas migracji lub pracy awaryjnej, edytując ustawienia interfejsu sieciowego w obszarze Ustawienia dla zreplikowanej maszyny wirtualnej.

## <a name="select-the-target-network"></a>Wybierz sieć docelowa

VMware i maszyn fizycznych i maszyn wirtualnych funkcji Hyper-V (bez System Center Virtual Machine Manager) można określić docelowych sieci wirtualnej dla poszczególnych maszyn wirtualnych. Dla maszyn wirtualnych funkcji Hyper-V zarządzanych z programu Virtual Machine Manager, użyj [mapowania sieci](site-recovery-network-mapping.md) do mapowania sieci maszyn wirtualnych na serwerze źródłowym z programem Virtual Machine Manager i docelowymi sieciami platformy Azure.

1. W obszarze **elementy replikowane** w magazynie usług odzyskiwania, wybierz dowolny element replikowane, aby uzyskać dostęp do ustawień dla tego elementu replikowane.

2. Wybierz **obliczenia i sieć** kartę, aby uzyskać dostęp do ustawień sieci zreplikowanych elementów.

3. W obszarze **właściwości sieci**, wybierz sieć wirtualną z listy dostępnych interfejsów sieciowych.

    ![Ustawienia sieci](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modyfikowanie Sieć docelowa ma wpływ na wszystkie interfejsy sieciowe dla określonej maszyny wirtualnej.

Dla chmur programu Virtual Machine Manager modyfikowanie mapowania sieci ma wpływ na wszystkie maszyny wirtualne i ich interfejsów sieciowych.

## <a name="select-the-target-interface-type"></a>Wybierz docelowy typ interfejsu

W obszarze **interfejsy sieciowe** sekcji **obliczenia i sieć** okienku można wyświetlać i edytować ustawienia interfejsu sieciowego. Można również określić typ interfejsu sieci docelowej.

- A **głównej** interfejsu sieciowego jest wymagany w trybie failover.
- Interfejsy innych wybranej sieci, jeśli istnieje, **dodatkowej** interfejsów sieciowych.
- Wybierz **nie używaj** wykluczyć interfejsu sieciowego z tworzenia w trybie failover.

Domyślnie gdy w przypadku włączenia replikacji, Site Recovery wybiera wszystkie interfejsy sieciowe wykrytych na serwerze lokalnym. Oznacza je jako jeden **głównej** i wszystkie inne jako **dodatkowej**. Interfejsami kolejnych dodane na lokalnym serwerze są oznaczone **nie używaj** domyślnie. Podczas dodawania więcej interfejsów sieciowych, upewnij się, czy wybrano rozmiar docelowy poprawne maszyny wirtualnej platformy Azure, aby uwzględnić wszystkie interfejsy sieciowe wymagane.

## <a name="modify-network-interface-settings"></a>Zmodyfikuj ustawienia interfejsu sieciowego

Można zmodyfikować podsieć lub adres IP dla interfejsów sieciowych zreplikowanych elementów. Jeśli nie określono adresu IP, usługi Site Recovery przypisać następnego dostępnego adresu IP z podsieci do interfejsu sieciowego w trybie failover.

1. Wybierz interfejs dowolnej dostępnej sieci można otworzyć ustawienia interfejsu sieciowego.

2. Wybierz odpowiednią podsieć na liście dostępnych podsieci.

3. Wprowadź żądane adres IP (zgodnie z wymaganiami).

    ![Ustawienia interfejsu sieciowego](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Wybierz **OK** Zakończ edytowanie, a następnie wróć do **obliczenia i sieć** okienka.

5. Powtórz kroki 1 – 4 dla innych interfejsów sieciowych.

6. Wybierz **zapisać** można zapisać wszystkich zmian.

## <a name="next-steps"></a>Kolejne kroki
  [Dowiedz się więcej](../virtual-network/virtual-network-network-interface-vm.md) o interfejsów sieciowych do obsługi maszyn wirtualnych platformy Azure.
