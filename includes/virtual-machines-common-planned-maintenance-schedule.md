---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c7fe0d6f8e03501cca7a8b98f95286b6a21c0476
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232792"
---
## <a name="multi-and-single-instance-vms"></a>Obsługa wielu i jednego wystąpienia maszyn wirtualnych
Wielu klientów uruchomieniu na podstawie liczby z platformy Azure krytyczne, czy można zaplanować podczas planowanej konserwacji z powodu przestojów — uczestniczenia w swoich maszyn wirtualnych około 15 minut — które ma miejsce podczas konserwacji. Zestawy dostępności można użyć, które ułatwiają sterowanie tym, gdy maszyny wirtualne aprowizowane otrzymają planowanej konserwacji.

Istnieją dwie możliwe konfiguracje dla maszyn wirtualnych uruchomionych na platformie Azure. Maszyny wirtualne albo są konfigurowane jako obejmujące wiele wystąpień lub jednego wystąpienia. W przypadku maszyn wirtualnych w zestawie dostępności, następnie one są konfigurowane jako obejmujące wiele wystąpień. Należy pamiętać, nawet pojedynczy maszyny wirtualne można wdrażać w zestawie dostępności, tak że będą one traktowane jako wiele wystąpień. Jeśli maszyny wirtualne nie znajdują się w zestawie dostępności, następnie one są konfigurowane jako pojedynczego wystąpienia.  Aby uzyskać szczegółowe informacje dotyczące zestawów dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Planowana konserwacja aktualizacji do jednego wystąpienia i obejmujące wiele wystąpień maszyn wirtualnych nastąpić oddzielnie. Ponowne konfigurowanie maszyn wirtualnych z systemem jednego wystąpienia (jeśli są one obejmujące wiele wystąpień) lub jako wiele wystąpień (jeśli są one pojedynczego wystąpienia), można kontrolować, po ich maszyn wirtualnych komunikatu planowanej konserwacji. Zobacz [planowana Konserwacja maszyn wirtualnych z systemem Linux platformy Azure](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [planowana Konserwacja maszyn wirtualnych Windows Azure](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) szczegółowe informacje dotyczące planowanej konserwacji maszyn wirtualnych platformy Azure.

## <a name="for-multi-instance-configuration"></a>Dla konfiguracji o wielu wystąpieniach
Można wybrać podczas planowanej konserwacji wpływa na maszynach wirtualnych wdrożonych w konfiguracji zestawu dostępności, usuwając te maszyny wirtualne z zestawów dostępności.

1. Zostanie wysłana wiadomość e-mail do Ciebie siedmiu dni kalendarzowych przed planowana Konserwacja maszyn wirtualnych w konfiguracji o wielu wystąpieniach. Identyfikatory subskrypcji oraz nazwy objęte maszyny wirtualne w wielu wystąpieniach znajdują się w treści wiadomości e-mail.
2. Te siedem dni możesz wybrać czas wystąpień są aktualizowane przez usunięcie wielu wystąpień maszyn wirtualnych w danym regionie, z ich zestawu dostępności. Ta zmiana w konfiguracji powoduje, że ponowne uruchomienie komputera, ponieważ maszyna wirtualna jest przenoszona z jednego hosta fizycznego, przeznaczony dla konserwacji do innego hosta fizycznego, który nie jest przeznaczony dla obsługi.
3. Możesz usunąć maszynę Wirtualną z jej zestaw dostępności w portalu Azure.

   1. W portalu wybierz maszynę Wirtualną, aby usunąć z zestawu dostępności.  

   2. W obszarze **ustawienia**, kliknij przycisk **zestawu dostępności**.

      ![Wybór zestawu dostępności](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. W zestawie dostępności menu rozwijanym, wybierz opcję "Nie jest częścią zestawu dostępności."

      ![Usuń z zestawu](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. U góry strony, kliknij przycisk **Zapisz**. Kliknij przycisk **tak** można potwierdzić, że spowoduje to ponowne uruchomienie maszyny Wirtualnej.

   >[!TIP]
   >Możesz później ponowne konfigurowanie maszyny Wirtualnej do wielu wystąpień, wybierając jeden z zestawów dostępności uwzględnione na liście.

4. Usunięte z zestawów dostępności maszyny wirtualne zostaną przeniesione do jednego wystąpienia hostów i nie są aktualizowane podczas zaplanowanej konserwacji do konfiguracji zestawu dostępności.
5. Po aktualizacji do zestawu dostępności w maszyn wirtualnych (zgodnie z harmonogramem, opisane w oryginalnej wiadomości e-mail), należy dodać maszyny wirtualne do swoich zestawów dostępności. Staje się częścią zestawu dostępności ponownie konfiguruje maszyny wirtualne jako obejmujące wiele wystąpień i powoduje ponowne uruchomienie komputera. Zazwyczaj po wykonaniu wszystkich aktualizacji obejmujące wiele wystąpień w całe środowisko systemu Azure, następuje po konserwacji jednego wystąpienia.

Usuwanie maszyny Wirtualnej w zestawie dostępności również można osiągnąć przy użyciu programu Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Dla konfiguracji o jednym wystąpieniu
Możesz wybrać czas zaplanowanej konserwacji ma wpływ na możesz maszyny wirtualne w konfiguracji o jednym wystąpieniu, dodając tych maszyn wirtualnych w zestawach dostępności.

Krok po kroku

1. Zostanie wysłana wiadomość e-mail do Ciebie siedmiu dni przed zaplanowaną konserwacją, do maszyn wirtualnych w konfiguracji o jednym wystąpieniu. Identyfikatory subskrypcji i nazwy dotyczy jednego wystąpienia maszyn wirtualnych znajdują się w treści wiadomości e-mail.
2. Te siedem dni możesz wybrać czas wystąpienia ponowne uruchomienie przez dodanie jednego wystąpienia maszyn wirtualnych do zestaw dostępności w tym samym regionie. Ta zmiana w konfiguracji powoduje, że ponowne uruchomienie komputera, ponieważ maszyna wirtualna jest przenoszona z jednego hosta fizycznego, przeznaczony dla konserwacji do innego hosta fizycznego, który nie jest przeznaczony dla obsługi.
3. Postępuj zgodnie z instrukcjami w tym miejscu można dodać istniejących maszyn wirtualnych w zestawach dostępności przy użyciu witryny Azure portal i programu Azure PowerShell. (Zobacz przykładowy programu Azure PowerShell, który obejmuje następujące kroki).
4. Gdy te maszyny wirtualne są konfigurowane jako obejmujące wiele wystąpień, są wyłączone z planowanej konserwacji maszyn wirtualnych w jednym wystąpieniu.
5. Po zakończeniu aktualizacji maszyny Wirtualnej jednego wystąpienia (zgodnie z harmonogramem w oryginalnej wiadomości e-mail), maszyn wirtualnych możesz wrócić do jednego wystąpienia, usuwając maszyn wirtualnych z ich zestawów dostępności.

Dodawanie maszyny Wirtualnej można również zestaw dostępności, można osiągnąć przy użyciu programu Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
