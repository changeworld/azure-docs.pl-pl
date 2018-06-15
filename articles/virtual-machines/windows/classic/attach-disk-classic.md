---
title: Dołączanie dysku do klasycznej maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dołączenie dysku danych do maszyny wirtualnej systemu Windows utworzone z klasycznym modelu wdrażania i zainicjować go.
services: virtual-machines-windows, storage
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 48a6b96bd611fcc8fa8219aeef359419255b8cef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918595"
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Dołączanie dysku danych do maszyny wirtualnej systemu Windows przy użyciu klasycznego modelu wdrażania

W tym artykule pokazano, jak dołączyć dyski nowych i istniejących utworzone za pomocą klasycznego modelu wdrażania z maszyną wirtualną z systemem Windows przy użyciu portalu Azure.



Możesz również [dołączenie dysku danych do maszyny Wirtualnej systemu Linux, w portalu Azure](../../linux/attach-disk-portal.md).

Przed dołączeniem dysku, należy przejrzeć te wskazówki:

* Rozmiar maszyny wirtualnej Określa, ile można dołączać dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Aby użyć magazyn w warstwie Premium, należy serii DS lub GS-series maszyny wirtualnej. Z tych maszyn wirtualnych służy dysków z konta magazynu zarówno Premium i standardowa. Magazyn w warstwie Premium jest dostępna w pewnych regionach. Aby uzyskać więcej informacji, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Dla nowego dysku nie trzeba go najpierw utworzyć ponieważ Azure tworzy go po dołączeniu go.

Możesz również [dołączenie dysku danych przy użyciu programu Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Znaleźć maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz maszynę wirtualną z zasobów wymienionych na pulpicie nawigacyjnym.
3. W lewym okienku w obszarze **ustawienia**, kliknij przycisk **dysków**.

    ![Otwórz ustawienia dysku](./media/attach-disk/virtualmachinedisks.png)

Kontynuuj przez następujące instrukcje dotyczące dołączania albo [nowy dysk](#option-1-attach-a-new-disk) lub [istniejącego dysku](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opcja 1: Dołączać i zainicjuj nowego dysku

1. Na **dysków** bloku, kliknij przycisk **Dołącz nowy**.
2. Przejrzyj ustawienia domyślne, należy zaktualizować odpowiednio do potrzeb, a następnie kliknij **OK**.

   ![Przejrzyj ustawienia dysku](./media/attach-disk/attach-new.png)

3. Po Azure utworzy dysk i dołącza go do maszyny wirtualnej, nowy dysk ma na liście ustawień dysku maszyny wirtualnej w obszarze **dysków z danymi**.

### <a name="initialize-a-new-data-disk"></a>Zainicjuj nowy dysk danych

1. Połączenie z maszyną wirtualną. Aby uzyskać instrukcje, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Po zalogowaniu się do maszyny wirtualnej, należy otworzyć **Menedżera serwera**. W okienku po lewej stronie wybierz **usług plików i magazynowania**.

    ![Otwórz Menedżera serwera](../media/attach-disk-portal/fileandstorageservices.png)

3. Wybierz **dysków**.
4. **Dysków** sekcji znajduje się lista dysków. W większości przypadków maszyny wirtualnej ma dysk 0 na partycje, dysk 1 i 2 dysku. Dysk 0 na partycje jest dysk systemu operacyjnego, dysk 1 jest tymczasowy dysku i dysk 2 jest dyskiem danych nowo dołączony do maszyny wirtualnej. Dysk z danymi wyświetla partycję jako **nieznany**.

 Kliknij prawym przyciskiem myszy dysk, a następnie wybierz **zainicjować**.

5. Wiesz, że wszystkie dane zostaną wymazane, gdy dysk jest zainicjowany. Kliknij przycisk **tak** potwierdzić ostrzeżenia i Zainicjuj dysk. Po wykonaniu tych czynności partycji będzie wyświetlany jako **GPT**. Ponownie kliknij prawym przyciskiem myszy dysk, a następnie wybierz **nowy wolumin**.

6. Ukończ pracę kreatora, przy użyciu wartości domyślnych. Po zakończeniu pracy kreatora **woluminów** sekcja zawiera informacje o nowy wolumin. Dysk jest teraz w trybie online i jest gotowy do przechowywania danych.

    ![Wolumin został zainicjowany pomyślnie](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Opcja 2: Dołączanie istniejącego dysku
1. Na **dysków** bloku, kliknij przycisk **Attach istniejących**.
2. W obszarze **dołączyć istniejącego dysku**, kliknij przycisk **lokalizacji**.

   ![Dołączanie istniejącego dysku](./media/attach-disk/attachexistingdisksettings.png)
3. W obszarze **kont magazynu**, wybierz konto i kontener, który zawiera plik VHD.

   ![Znajdź lokalizację wirtualnego dysku twardego](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Wybierz plik VHD.
5. W obszarze **dołączyć istniejącego dysku**, po prostu wybrany plik jest wyświetlana w obszarze **pliku VHD**. Kliknij przycisk **OK**.
6. Po Azure dołącza dysk do maszyny wirtualnej, jest wymienione w ustawieniach dysku maszyny wirtualnej w obszarze **dysków z danymi**.

## <a name="use-trim-with-standard-storage"></a>PRZYCINANIE za pomocą magazynu w warstwie standardowa

Jeśli używasz standardowego magazynu (HDD), należy włączyć PRZYCINANIE. PRZYCINANIE odrzuca nieużywanych bloków na dysku dzięki rozliczenie jest przeprowadzane tylko dla magazynu, który faktycznie używasz. Korzystając z operacją PRZYCINANIA zapisać kosztów, w tym nieużywanych bloków wynikających z usunięcie dużych plików.

Można uruchomić tego polecenia, aby sprawdzić ustawienie PRZYCINANIA. Otwórz wiersz polecenia na Twojej maszyny Wirtualnej systemu Windows i wpisz:

```
fsutil behavior query DisableDeleteNotify
```

Jeśli polecenie zwróci wartość 0, PRZYCINANIE jest prawidłowo włączona. Jeśli zmienna zwraca 1, uruchom następujące polecenie, aby umożliwić PRZYCINANIE:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli aplikacja musi używać D: dysków do przechowywania danych, możesz [zmienić literę dysku tymczasowym systemu Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Zasoby dodatkowe
[O dyskach i wirtualne dyski twarde dla maszyn wirtualnych](../../virtual-machines-linux-about-disks-vhds.md)
