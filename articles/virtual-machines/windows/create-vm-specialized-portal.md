---
title: Tworzenie maszyny Wirtualnej systemu Windows z dysku VHD specjalne w portalu Azure | Dokumentacja firmy Microsoft
description: Utwórz nową maszynę Wirtualną systemu Windows z dysku VHD w portalu Azure.
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
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 428003747b7c746a2849a042e54647e86361c562
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716564"
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Utwórz maszynę Wirtualną z wirtualnego dysku twardego za pomocą portalu Azure


Istnieje kilka sposobów tworzenia maszyn wirtualnych na platformie Azure. Jeśli masz już wirtualnego dysku twardego lub skopiować wirtualnego dysku twardego ze i istniejącą maszynę Wirtualną do użycia, można utworzyć nowej maszyny Wirtualnej, dołączając wirtualny dysk twardy jako dysk systemu operacyjnego. Ten proces *dołącza* dysk VHD do nowej maszyny Wirtualnej jako dysk systemu operacyjnego.

Można również utworzyć nową maszynę Wirtualną z wirtualnego dysku twardego maszyny wirtualnej, która została usunięta. Na przykład jeśli masz maszyny Wirtualnej platformy Azure nie działa prawidłowo, należy usunąć maszynę Wirtualną i umożliwia utworzenie nowej maszyny Wirtualnej wirtualny dysk twardy. Można ponownie użyć tego samego dysku VHD lub utworzyć kopię wirtualnego dysku twardego przez tworzenie migawek, a następnie utworzenie nowego dysku zarządzanego z migawki. Trwa kilka większej liczby kroków, ale zapewnia można zachować oryginalny dysk VHD, a także tworzy migawkę wrócił do w razie potrzeby.

Masz lokalnej maszyny Wirtualnej, który ma być używana do tworzenia maszyny Wirtualnej na platformie Azure. Można przekazać wirtualny dysk twardy i dołączenie go do nowej maszyny Wirtualnej. W celu przekazania dysku VHD, należy użyć programu PowerShell lub inne narzędzie do przekaż go do konta magazynu, a następnie utworzyć dysków zarządzanych z dysku VHD. Aby uzyskać więcej informacji, zobacz [przekazanie specjalne dysku VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Jeśli chcesz utworzyć wiele maszyn wirtualnych za pomocą maszyny Wirtualnej lub wirtualnego dysku twardego, nie można używać tej metody. W przypadku większych wdrożeń, wykonaj następujące czynności [Tworzenie obrazu](capture-image-resource.md) , a następnie [umożliwia tworzenie wielu maszyn wirtualnych obrazu](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Kopiowanie dysku

Utwórz migawkę, a następnie utwórz dysk z migawki. Dzięki temu można zachować oryginalny dysk VHD jest ponownie.

1. W menu po lewej stronie kliknij **wszystkie zasoby**.
2. W **wszystkie typy** listy rozwijanej, usuń zaznaczenie pola wyboru **Zaznacz wszystko** , a następnie przewiń w dół i wybierz **dysków** można znaleźć dostępne dyski.
3. Kliknij dysk, który chcesz użyć. **Omówienie** strony dla otwiera dysku.
4. Na stronie przeglądu, w menu u góry, kliknij przycisk **+ Utwórz migawkę**. 
5. Wpisz nazwę migawki.
6. Wybierz **grupy zasobów** dla migawki. Można użyć istniejącej grupy zasobów lub Utwórz nową.
7. Określ, czy używać (HDD) standard lub Premium (SDD) magazynu.
8. Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz** do utworzenia migawki.
9. Po utworzeniu migawki, wybierz polecenie **+ Utwórz zasób** w lewym menu.
10. Na pasku wyszukiwania wpisz **dysków zarządzanych w** i wybierz **dysków zarządzanych** z listy.
11. Na **dysków zarządzanych** kliknij przycisk **Utwórz**.
12. Wpisz nazwę dysku.
13. Wybierz **grupy zasobów** dla dysku. Można użyć istniejącej grupy zasobów lub Utwórz nową. Są także to grupa zasobów gdzie utworzyć maszynę Wirtualną z dysku.
14. Określ, czy używać (HDD) standard lub Premium (SDD) magazynu.
15. W **typ źródła**, upewnij się, że **migawki** jest zaznaczone.
16. W **migawki źródła** listy rozwijanej, wybierz migawki, którego chcesz użyć.
17. Wprowadź wszelkie inne dostosowania, zgodnie z potrzebami, a następnie kliknij przycisk **Utwórz** można utworzyć na dysku.

## <a name="create-a-vm-from-a-disk"></a>Tworzenie maszyny Wirtualnej z dysku

Po utworzeniu zarządzanych dysku wirtualnego dysku twardego, który ma być używany, można utworzyć maszyny Wirtualnej w portalu.

1. W menu po lewej stronie kliknij **wszystkie zasoby**.
2. W **wszystkie typy** listy rozwijanej, usuń zaznaczenie pola wyboru **Zaznacz wszystko** , a następnie przewiń w dół i wybierz **dysków** można znaleźć dostępne dyski.
3. Kliknij dysk, który chcesz użyć. **Omówienie** strony dla otwiera dysku.
Upewnij się, że na stronie przeglądu **stan dysku** jest wymienione jako **odłączonego**. Jeśli nie, konieczne może odłączyć dysku od maszyny Wirtualnej albo Usuń maszynę Wirtualną, aby zwolnić dysku.
4. W menu u góry okienka kliknij **+ Utwórz wirtualna**.
5. Na **podstawy** strony dla nowej maszyny Wirtualnej, wpisz nazwę i wybierz istniejącą grupę zasobów lub Utwórz nową.
6. Na **rozmiar** strony, wybierz stronę rozmiar maszyny Wirtualnej, a następnie kliknij przycisk **wybierz**.
7. Na **ustawienia** strony, albo poczekać na portalu, tworzenie wszystkich nowych zasobów lub wybrać istniejącą **sieci wirtualnej** i **sieciowej grupy zabezpieczeń**. Portalu Zawsze twórz nowe karty Sieciowej i publiczny adres IP dla nowej maszyny Wirtualnej. 
8. Zmiany opcji monitorowania i w razie potrzeby dodaj wszystkich rozszerzeń.
9. Gdy skończysz, kliknij przycisk **OK**. 
10. Konfiguracja maszyny Wirtualnej przeszedł sprawdzania poprawności, kliknij przycisk **OK** do wdrożenia.

## <a name="next-steps"></a>Kolejne kroki

Można również użyć programu PowerShell do [przekazania dysku VHD na platformie Azure i utworzyć Maszynę wirtualną specjalne](create-vm-specialized.md).


