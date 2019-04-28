---
title: Tworzenie maszyny Wirtualnej z systemem Windows na podstawie wyspecjalizowanego wirtualnego dysku twardego w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Utwórz nową maszynę Wirtualną Windows z poziomu dysku VHD w witrynie Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: b24435501ed1312e91ebec9b9c434971dbc94b55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402308"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Utwórz Maszynę wirtualną z poziomu dysku VHD za pomocą witryny Azure portal

Istnieje kilka sposobów, aby utworzyć maszynę wirtualną (VM) na platformie Azure: 

- Jeśli masz już wirtualny dysk twardy (VHD) do użycia lub chcesz skopiować wirtualny dysk twardy z istniejącej maszyny Wirtualnej do użycia, możesz utworzyć nową maszynę Wirtualną przez *dołączanie* wirtualnego dysku twardego do nowej maszyny Wirtualnej jako dysk systemu operacyjnego. 

- Możesz utworzyć nową maszynę Wirtualną z wirtualnego dysku twardego maszyny wirtualnej, która została usunięta. Na przykład jeśli masz maszyny Wirtualnej platformy Azure, która nie działa prawidłowo, możesz usunąć maszynę Wirtualną i użyj jej wirtualnego dysku twardego, aby utworzyć nową maszynę Wirtualną. Możesz ponownie użyć tego samego pliku VHD lub tworzenie kopii wirtualnego dysku twardego przez utworzenie migawki, a następnie utworzenie nowego dysku zarządzanego z migawki. Chociaż tworzenie migawek zajmuje kilka kroków, zachowuje oryginalny dysk VHD i zapewnia rezerwowe.

- Pobierz klasycznej maszyny Wirtualnej, a następnie użyj wirtualnego dysku twardego do tworzenia nowej maszyny Wirtualnej, który używa modelu wdrażania usługi Resource Manager i dysków zarządzanych. Aby uzyskać najlepsze wyniki **zatrzymać** klasycznej maszyny Wirtualnej w witrynie Azure portal, przed utworzeniem migawki.
 
- Możesz utworzyć Maszynę wirtualną platformy Azure z dysku VHD w środowisku lokalnym przez przekazanie dysku VHD w środowisku lokalnym i dołączania ich do nowej maszyny Wirtualnej. Użyj programu PowerShell lub innego narzędzia do przekazania dysku VHD do konta magazynu, a następnie tworzenie dysku zarządzanego z dysku VHD. Aby uzyskać więcej informacji, zobacz [przekazywanie wyspecjalizowanego wirtualnego dysku twardego](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Nie używaj wyspecjalizowanego dysku, jeśli chcesz utworzyć wiele maszyn wirtualnych. Zamiast tego w przypadku większych wdrożeń [utworzyć obraz](capture-image-resource.md) i następnie [umożliwia tworzenie wielu maszyn wirtualnych tego obrazu](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Kopiowanie dysku

Utwórz migawkę, a następnie utwórz dysku z migawki. Ta strategia pozwala zachować oryginalny dysk VHD jako rezerwowe:

1. Z [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz **wszystkich usług**.
2. W **wszystkich usług** polu wyszukiwania, wprowadź **dysków** , a następnie wybierz **dysków** Aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. **Dysku** ten dysk zostanie wyświetlona strona.
4. W menu u góry wybierz **Utwórz migawkę**. 
5. Wprowadź **nazwa** migawki.
6. Wybierz **grupy zasobów** migawki. Można użyć istniejącej grupy zasobów lub Utwórz nową.
7. Aby uzyskać **typ konta**, wybierają **standardowa (HDD)** lub **Premium (SSD)** magazynu.
8. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** można utworzyć migawki.
9. Po utworzeniu migawki, wybierz **Utwórz zasób** w menu po lewej stronie.
10. W polu wyszukiwania wprowadź **dysku zarządzanego** , a następnie wybierz **Managed Disks** z listy.
11. Na **Managed Disks** wybierz opcję **Utwórz**.
12. Wprowadź **nazwa** dla dysku.
13. Wybierz **grupy zasobów** dla dysku. Można użyć istniejącej grupy zasobów lub Utwórz nową. Zaznacz to pole wyboru zostanie również jako grupa zasobów której utworzono maszynę Wirtualną z dysku.
14. Aby uzyskać **typ konta**, wybierają **standardowa (HDD)** lub **Premium (SSD)** magazynu.
15. W **typ źródła**, upewnij się, **migawki** jest zaznaczone.
16. W **migawka źródła** listę rozwijaną, wybierz migawki, w której chcesz użyć.
17. Wprowadź wszelkie inne dostosowania, zgodnie z potrzebami, a następnie wybierz pozycję **Utwórz** do utworzenia dysku.

## <a name="create-a-vm-from-a-disk"></a>Tworzenie maszyny Wirtualnej z dysku

Po utworzeniu dysku zarządzanego wirtualny dysk twardy, którego chcesz używać, możesz utworzyć maszynę Wirtualną w portalu:

1. Z [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz **wszystkich usług**.
2. W **wszystkich usług** polu wyszukiwania, wprowadź **dysków** , a następnie wybierz **dysków** Aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. **Dysku** ten dysk zostanie otwarta strona.
4. W **Przegląd** strony, upewnij się, że **stan dysku** jest wymieniony jako **odłączonego**. Jeśli nie, może być konieczne Odłącz dysk od maszyny Wirtualnej lub Usuń maszynę Wirtualną, aby zwolnić dysku.
4. W menu w górnej części strony wybierz **Utwórz maszynę Wirtualną**.
5. Na **podstawy** strony dla nowej maszyny Wirtualnej, wprowadź **nazwę maszyny wirtualnej** i wybierz istniejącą **grupy zasobów** lub utworzyć nowy.
6. Dla **rozmiar**, wybierz opcję **Zmień rozmiar** dostęp do **rozmiar** strony.
7. Zaznacz wiersz rozmiar maszyny Wirtualnej, a następnie wybierz **wybierz**.
8. Na **sieć** strony, można albo pozwolić portalu tworzyć wszystkie nowe zasoby, lub wybrać istniejącą **sieć wirtualna** i **sieciowej grupy zabezpieczeń**. Portal zawsze tworzy nowy interfejs sieciowy i publiczny adres IP dla nowej maszyny Wirtualnej. 
9. Na **zarządzania** strony, wprowadź zmiany w opcji monitorowania.
10. Na **config gościa** strony, należy dodać wszystkie rozszerzenia, zgodnie z potrzebami.
11. Gdy wszystko będzie gotowe, wybierz pozycję **przeglądu + Utwórz**. 
12. Jeśli konfiguracja maszyny Wirtualnej pozytywnie przejdą weryfikację, wybierz opcję **Utwórz** rozpocząć wdrażanie.

## <a name="next-steps"></a>Kolejne kroki

Możesz również użyć programu PowerShell, aby [przekazywanie wirtualnego dysku twardego na platformie Azure i tworzenie wyspecjalizowanej maszyny Wirtualnej](create-vm-specialized.md).


