---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 55e46e058bddca717929df61b2bc766b89e0f885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485308"
---
![Maszyny wirtualne w autonomicznej usługi w chmurze](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Jeśli umieścisz maszyn wirtualnych w sieci wirtualnej, można określić, ile chcesz użyć dla usług w chmurze obciążenia równoważenia i zestawy dostępności. Ponadto można organizować maszyny wirtualne w podsieci w taki sam sposób, jak i lokalną sieć i połączyć sieć wirtualną z siecią lokalną. Oto przykład:

![Maszyny wirtualne w sieci wirtualnej](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Sieci wirtualne są zalecanym sposobem łączenia maszyn wirtualnych na platformie Azure. Najlepszym rozwiązaniem jest skonfigurowanie każdej warstwy aplikacji w usłudze w chmurze oddzielne. Jednak może być konieczne łączenie niektórych maszyn wirtualnych z warstw aplikacji jest inny w tej samej usługi w chmurze do maksymalnie 200 usług w chmurze na subskrypcję. Aby zapoznać się z tym i innych ograniczeń, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Łączenie maszyn wirtualnych w sieci wirtualnej
Połączenie maszyn wirtualnych w sieci wirtualnej:

1. Tworzenie sieci wirtualnej w [witryny Azure portal](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) i określ "wdrożenie klasyczne".
2. Utwórz zestaw usług w chmurze dla danego wdrożenia odzwierciedlić projektu dla zestawów dostępności i równoważenia obciążenia. W witrynie Azure portal kliknij pozycję **Utwórz zasób > Compute > Usługa w chmurze** dla każdej usługi w chmurze.

   Podczas wypełniania szczegóły dotyczące usług w chmurze wybierz taką samą _grupy zasobów_ używany z siecią wirtualną.

3. Aby utworzyć każdej nowej maszyny wirtualnej, kliknij przycisk **Utwórz zasób > Compute**, następnie wybierz odpowiedni obraz maszyny Wirtualnej z **polecane aplikacje**.

   Na maszynie wirtualnej **podstawy** bloku wybierz taką samą _grupy zasobów_ używany z siecią wirtualną.

   ![Blok podstawowe maszyny Wirtualnej, korzystając z sieci wirtualnej](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Podczas wypełniania maszyny Wirtualnej **ustawienia**, wybierz poprawny _usługi w chmurze_ lub _sieci wirtualnej_ dla maszyny Wirtualnej.

   Azure wybierze inny element, na podstawie dokonanego wyboru.

   ![Blok ustawień maszyny Wirtualnej, korzystając z sieci wirtualnej](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Łączenie maszyn wirtualnych w autonomicznej usłudze w chmurze
Połączenie maszyn wirtualnych w autonomicznej usłudze w chmurze:

1. Utwórz usługę w chmurze w [witryny Azure portal](http://portal.azure.com). Kliknij przycisk **nowy > obliczenia > Usługa w chmurze**. Alternatywnie można utworzyć usługi w chmurze dla danego wdrożenia po utworzeniu pierwszej maszyny wirtualnej.
2. Podczas tworzenia maszyn wirtualnych, należy wybrać tej samej grupie zasobów, które są używane z usługą w chmurze.

   ![Dodaj maszynę wirtualną do istniejącej usługi w chmurze](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3. Podczas wypełniania szczegóły maszyny Wirtualnej wybierz nazwę utworzonego w pierwszym kroku usługi w chmurze.

   ![Wybieranie usługi w chmurze dla maszyny wirtualnej](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
