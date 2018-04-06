---
title: Dołączenie dysku danych do maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Użyj portalu można dołączyć danych do nowego lub istniejącego dysku do maszyny Wirtualnej systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 4acfe53d68db3192c1f6c3c9e5f91b55bd5df7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Dołączenie dysku danych do maszyny Wirtualnej systemu Linux za pomocą portalu 
W tym artykule przedstawiono sposób dołączyć istniejących i nowych dysków do maszyny wirtualnej systemu Linux za pośrednictwem portalu Azure. Możesz również [dołączenie dysku danych do maszyny Wirtualnej systemu Windows w portalu Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Przed dołączeniem dysków do maszyny Wirtualnej, przejrzyj następujące wskazówki:

* Rozmiar maszyny wirtualnej Określa, ile można dołączać dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Aby użyć magazyn w warstwie Premium, należy serii DS lub GS-series maszyny wirtualnej. Można użyć zarówno Premium i standardowa dysków z tych maszyn wirtualnych. Magazyn w warstwie Premium jest dostępna w pewnych regionach. Aby uzyskać więcej informacji, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dysków dołączonych do maszyn wirtualnych są faktycznie pliki VHD przechowywane na platformie Azure. Aby uzyskać więcej informacji, zobacz [o dyskach i wirtualne dyski twarde dla maszyn wirtualnych](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Znaleźć maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie kliknij **maszyn wirtualnych**.
3. Wybierz maszynę wirtualną z listy.
4. Do wirtualnej maszyny strony, w **Essentials**, kliknij przycisk **dysków**.
   
    ![Otwórz ustawienia dysku](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Dołączanie nowego dysku

1. Na **dysków** okienku, kliknij przycisk **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane dla **nazwa** i wybierz **Tworzenie dysku**:

    ![Utwórz Azure dysków zarządzanych](./media/attach-disk-portal/create-new-md.png)

3. Wprowadź nazwę dysku zarządzanego. Przejrzyj ustawienia domyślne, należy zaktualizować odpowiednio do potrzeb, a następnie kliknij **Utwórz**.
   
   ![Przejrzyj ustawienia dysku](./media/attach-disk-portal/create-new-md-settings.png)

4. Kliknij przycisk **zapisać** do tworzenia dysków zarządzanych i aktualizowania konfiguracji maszyny Wirtualnej:

   ![Zapisz nowy dysk zarządzane Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Po Azure utworzy dysk i dołącza go do maszyny wirtualnej, nowy dysk ma na liście ustawień dysku maszyny wirtualnej w obszarze **dysków z danymi**. Dyski zarządzane są zasobem najwyższego poziomu dysku pojawia się w katalogu głównym grupy zasobów:

   ![Dysku platformy Azure zarządzanych w grupie zasobów](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
1. Na **dysków** okienku, kliknij przycisk **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane dla **nazwa** Aby wyświetlić listę istniejących dysków zarządzanych dostępny dla Twojej subskrypcji platformy Azure. Wybierz zarządzane dysku, aby dołączyć:

   ![Dołączanie istniejącego dysku zarządzanego Azure](./media/attach-disk-portal/select-existing-md.png)

3. Kliknij przycisk **zapisać** dołączyć istniejącego dysku zarządzanego i zaktualizować konfiguracji maszyny Wirtualnej:
   
   ![Zapisanie aktualizacji dysku zarządzanego Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Po Azure dołącza dysk do maszyny wirtualnej, jest wymienione w ustawieniach dysku maszyny wirtualnej w obszarze **dysków z danymi**.



## <a name="next-steps"></a>Kolejne kroki
Możesz również [dołączenie dysku danych](add-disk.md) przy użyciu wiersza polecenia platformy Azure.
