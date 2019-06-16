---
title: Zarządzanie interfejsami sieciowymi w usłudze Azure Site Recovery dla lokalnego odzyskiwania po awarii na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania interfejsów sieciowych dla środowiska lokalnego odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 5d5dd7bc3f6b60c2f9d7c2179f2bd356ca101dc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471778"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Zarządzanie interfejsami sieciowymi maszyn wirtualnych do środowiska lokalnego odzyskiwania po awarii na platformie Azure
Maszynę wirtualną (VM) na platformie Azure musi mieć co najmniej jeden interfejs sieciowy dołączony do niego. Może mieć wiele sieci interfejsów dołączone do niego jako obsługuje rozmiar maszyny Wirtualnej.

Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej platformy Azure jest zdefiniowany jako podstawowy interfejs sieciowy. Wszystkie inne interfejsy sieciowe w maszynie wirtualnej są dodatkowych interfejsów sieciowych. Również domyślnie cały ruch wychodzący z maszyny wirtualnej jest wysyłane adres IP, który jest przypisany do podstawowa konfiguracja adresów IP głównego interfejsu sieciowego.

W środowisku lokalnych maszyn wirtualnych lub serwerach może mieć wiele interfejsów sieciowych do różnych sieci w środowisku. Różnych sieci są zwykle używane do wykonywania określonych operacji, takich jak uaktualnienia, konserwacją i dostępem do Internetu. Podczas migracji lub przejść w tryb failover na platformie Azure ze środowiska lokalnego, należy pamiętać o tym, że interfejsy sieciowe w tej samej maszyny wirtualnej musi połączone z tej samej sieci wirtualnej.

Domyślnie usługa Azure Site Recovery tworzy wiele sieci interfejsy na maszynie wirtualnej platformy Azure, jak są połączone z lokalnym serwerem. Można uniknąć, tworząc interfejsy sieciowe nadmiarowe podczas migracji lub pracy awaryjnej, edytując ustawienia interfejsu sieciowego, w obszarze Ustawienia dla zreplikowanej maszyny wirtualnej.

## <a name="select-the-target-network"></a>Wybierz sieć docelowa

Dla programu VMware i maszyn fizycznych i maszyn wirtualnych funkcji Hyper-V (bez System Center Virtual Machine Manager) można określić docelowa sieć wirtualna dla poszczególnych maszyn wirtualnych. W przypadku maszyn wirtualnych funkcji Hyper-V zarządzanych za pomocą programu Virtual Machine Manager, użyj [mapowania sieci](site-recovery-network-mapping.md) do mapowania sieci maszyn wirtualnych na serwerze źródłowym programu Virtual Machine Manager i docelowymi sieciami platformy Azure.

1. W obszarze **zreplikowane elementy** w magazynie usługi Recovery Services, wybierz dowolny replikowanych element, aby uzyskać dostęp do ustawień dla tego replikowanego elementu.

2. Wybierz **obliczenia i sieć** kartę, aby uzyskać dostęp do ustawień sieci dla replikowanego elementu.

3. W obszarze **właściwości sieci**, wybierz sieć wirtualną z listy dostępnych interfejsów sieciowych.

    ![Ustawienia sieci](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modyfikowanie Sieć docelowa ma wpływ na wszystkie interfejsy sieciowe dla tej konkretnej maszyny wirtualnej.

Dla chmur programu Virtual Machine Manager modyfikując mapowanie sieci ma wpływ na wszystkie maszyny wirtualne oraz ich interfejsów sieciowych.

## <a name="select-the-target-interface-type"></a>Wybierz docelowy typ interfejsu

W obszarze **interfejsy sieciowe** części **obliczenia i sieć** okienku można wyświetlać i edytować ustawienia interfejsu sieciowego. Można również określić docelowy typ interfejsu sieciowego.

- A **głównej** interfejs sieciowy jest wymagany dla trybu failover.
- Interfejsy innej wybranej sieci, jeśli dowolny, **dodatkowej** interfejsy sieciowe.
- Wybierz **nie należy używać** do wykluczenia z interfejsem sieciowym z tworzenia w trybie failover.

Domyślnie gdy w przypadku włączenia replikacji, Site Recovery wybiera wszystkie interfejsy sieciowe wykryte na lokalnym serwerze. Oznacza jedną jako **podstawowego** i wszystkie inne jako **dodatkowej**. Żadnych kolejnych interfejsów, które dodano na serwer w środowisku lokalnym są oznaczone **nie należy używać** domyślnie. Podczas dodawania większej liczby interfejsów sieciowych, upewnij się, że wybrano rozmiar docelowej poprawne maszyny wirtualnej platformy Azure, aby pomieścić wszystkie interfejsy sieciowe wymagane.

## <a name="modify-network-interface-settings"></a>Zmodyfikuj ustawienia interfejsu sieciowego

Można zmodyfikować podsieci i adresu IP dla interfejsów sieciowych replikowanego elementu. Jeśli nie określono adresu IP, Usługa Site Recovery przypisze następnym dostępnym adresem IP z podsieci interfejsu sieciowego w trybie failover.

1. Zaznacz każdy interfejs sieciowy dostępne, aby otworzyć ustawienia interfejsu sieciowego.

2. Wybierz odpowiednią podsieć z listy dostępnych podsieci.

3. Wprowadź żądane adres IP (w zależności od potrzeb).

    ![Ustawienia interfejsu sieciowego](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Wybierz **OK** Zakończ edycję, a następnie wróć do **obliczenia i sieć** okienka.

5. Powtórz kroki 1 – 4 dla innych interfejsów sieciowych.

6. Wybierz **Zapisz** Aby zapisać wszystkie zmiany.

## <a name="next-steps"></a>Kolejne kroki
  [Dowiedz się więcej](../virtual-network/virtual-network-network-interface-vm.md) o interfejsach sieciowych maszyn wirtualnych platformy Azure.
