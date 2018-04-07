---
title: Dołączenie dysku danych zarządzanych do maszyny Wirtualnej systemu Windows - Azure | Dokumentacja firmy Microsoft
description: Jak można dołączyć nowego dysku danych zarządzanych do maszyny Wirtualnej systemu Windows w portalu Azure przy użyciu modelu wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 14721b2f2bc7913c2b7eadfc5ee801a223201ea9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Jak można dołączyć dysku danych zarządzanych do maszyny Wirtualnej systemu Windows, w portalu Azure

W tym artykule przedstawiono sposób dołączyć nowego dysku danych zarządzanych do maszyn wirtualnych systemu Windows w portalu Azure. Zanim to zrobisz, przejrzyj następujące wskazówki:

* Rozmiar maszyny wirtualnej Określa, ile można dołączać dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md).
* Dla nowego dysku nie trzeba go najpierw utworzyć ponieważ Azure tworzy go po dołączeniu go.

Możesz również [dołączenie dysku danych przy użyciu programu Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Dodaj dysk danych
1. W menu po lewej stronie kliknij **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną z listy.
3. Na stronie maszyny wirtualnej, kliknij przycisk **dysków**.
4. Na **dysków** kliknij przycisk **+ Dodaj dysk danych**.
5. Z listy rozwijanej dla nowego dysku, wybierz **Tworzenie dysku**.
6. W **dysków zarządzanych w Utwórz** strony, wpisz nazwę dla dysku i odpowiednio inne ustawienia w razie potrzeby. Po zakończeniu kliknij przycisk **Utwórz**.
7. W **dysków** kliknij przycisk **zapisać** Aby zapisać nową konfigurację dysku dla maszyny Wirtualnej.
6. Po Azure utworzy dysk i dołącza go do maszyny wirtualnej, nowy dysk ma na liście ustawień dysku maszyny wirtualnej w obszarze **dysków z danymi**.


## <a name="initialize-a-new-data-disk"></a>Zainicjuj nowy dysk danych

1. Połączenie z maszyną Wirtualną.
1. Kliknij menu start wewnątrz maszyny Wirtualnej i typ **diskmgmt.msc** i trafień **Enter**. Zostanie otwarta przystawka Zarządzanie dyskami.
2. Zarządzanie dyskami rozpoznaje, że znajduje się dysk nowy, które nie zostały zainicjowane i **Inicjowanie dysku** wyświetlone okno podręczne.
3. Upewnij się, że wybrano nowy dysk i kliknij przycisk **OK** można go zainicjować.
4. Nowy dysk zostanie wyświetlony jako **nieprzydzielonego**. Kliknij prawym przyciskiem myszy w dowolnym miejscu na dysku i wybierz **nowy wolumin prosty**. **Kreatorze nowych woluminów prostych** otwiera.
5. Przejdź przez kreatora, przechowywanie ustawień domyślnych, po zakończeniu wybierz **Zakończ**.
6. Zamknij program Zarządzanie dyskami.
7. Możesz uzyskać okno podręczne, które należy sformatować nowy dysk przed jego użyciem. Kliknij przycisk **Format dysku**.
8. W **Format nowy dysk** okna dialogowego, sprawdź ustawienia, a następnie kliknij przycisk **Start**.
9. Zostanie wyświetlone ostrzeżenie, że formatowanie dysków na partycje powoduje usunięcie wszystkich danych, kliknij przycisk **OK**.
10. Po zakończeniu format kliknij **OK**.

## <a name="use-trim-with-standard-storage"></a>PRZYCINANIE za pomocą magazynu w warstwie standardowa

Jeśli używasz standardowego magazynu (HDD), należy włączyć PRZYCINANIE. PRZYCINANIE odrzuca nieużywanych bloków na dysku dzięki rozliczenie jest przeprowadzane tylko dla magazynu, który faktycznie używasz. Można to zapisanie kosztów, jeśli Tworzenie dużych plików, a następnie usuń je. 

Można uruchomić tego polecenia, aby sprawdzić ustawienie PRZYCINANIA. Otwórz wiersz polecenia na Twojej maszyny Wirtualnej systemu Windows i wpisz:

```
fsutil behavior query DisableDeleteNotify
```

Jeśli polecenie zwróci wartość 0, PRZYCINANIE jest prawidłowo włączona. Jeśli zmienna zwraca 1, uruchom następujące polecenie, aby umożliwić PRZYCINANIE:
```
fsutil behavior set DisableDeleteNotify 0
```

Po usunięciu danych z dysku, można zapewnić PRZYCINANIA operacje opróżniania poprawnie, uruchamiając defragmentowania z operacją PRZYCINANIA:

```
defrag.exe <volume:> -l
```

Ponadto upewnij się, że cały wolumin jest ograniczona przez formatowania woluminu.

## <a name="next-steps"></a>Kolejne kroki
Jeśli aplikacja musi używać D: dysków do przechowywania danych, możesz [zmienić literę dysku tymczasowym systemu Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
