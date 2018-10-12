---
title: Dołączanie dysku danych zarządzanych do maszyny Wirtualnej Windows — Azure | Dokumentacja firmy Microsoft
description: Jak dołączyć dysk danych zarządzanych do maszyny Wirtualnej z systemem Windows przy użyciu witryny Azure portal.
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
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 8d83af114ebb5e5ff78372897d3e08ed592d4012
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093905"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Dołączanie dysku danych zarządzanych do maszyny Wirtualnej z systemem Windows przy użyciu witryny Azure portal

W tym artykule pokazano, jak dołączyć nowy dysk danych zarządzanych na maszynę wirtualną (VM) Windows przy użyciu witryny Azure portal. Rozmiar maszyny Wirtualnej Określa, jak wiele dysków z danymi można dołączać. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md).


## <a name="add-a-data-disk"></a>Dodawanie dysku danych

1. W [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną z listy.
3. Na **maszyny wirtualnej** wybierz opcję **dysków**.
4. Na **dysków** wybierz opcję **Dodaj dysk danych**.
5. Z listy rozwijanej dla nowego dysku, wybierz **Utwórz dysk**.
6. W **Tworzenie dysku zarządzanego** stronie, wpisz nazwę dla dysku i Dostosuj inne ustawienia zgodnie z potrzebami. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.
7. W **dysków** wybierz opcję **Zapisz** Aby zapisać nową konfigurację dysku dla maszyny Wirtualnej.
8. Po platforma Azure utworzy dysk i dołącza go do maszyny wirtualnej, nowy dysk ma na liście ustawień dysku maszyny wirtualnej w obszarze **dysków z danymi**.


## <a name="initialize-a-new-data-disk"></a>Inicjowanie nowy dysk danych

1. Nawiąż połączenie z maszyną wirtualną.
1. Wybierz pozycję Windows **Start** menu w uruchomionej maszyny Wirtualnej i wprowadź **diskmgmt.msc** w polu wyszukiwania. **Przystawki Zarządzanie dyskami** otwarta konsola.
2. Zarządzanie dyskami rozpoznaje, że masz nowe, niezainicjowanych dysków i **Zainicjuj dysk** zostanie wyświetlone okno.
3. Sprawdź nowy dysk jest wybrany, a następnie wybierz pozycję **OK** zainicjować go.
4. Nowy dysk pojawi się jako **nieprzydzielone**. Kliknij prawym przyciskiem myszy w dowolnym miejscu na dysku i wybierz **nowy wolumin prosty**. **Kreatorze nowych woluminów prostych** zostanie otwarte okno.
5. Postępuj zgodnie z instrukcjami kreatora, zapewniają, że wszystkie ustawienia domyślne, a po zakończeniu wybierz **Zakończ**.
6. Zamknij **Zarządzanie dyskami**.
7. Pojawi się okno podręczne, informujący o tym, należy sformatować nowy dysk przed jego użyciem. Wybierz **Format dysku**.
8. W **Formatuj nowy dysk** , sprawdź ustawienia, a następnie wybierz pozycję **Start**.
9. Zostanie wyświetlone ostrzeżenie z informacją, że formatowanie dyski na partycje powoduje usunięcie wszystkich danych. Kliknij przycisk **OK**.
10. Po zakończeniu formatowania wybierz **OK**.

## <a name="use-trim-with-standard-storage"></a>TRIM za pomocą magazynu w warstwie standardowa

Jeśli używasz magazynu w warstwie standardowa (HDD), należy włączyć **TRIM** polecenia. **TRIM** polecenia odrzuca nieużywanych bloków na dysku, dlatego, że opłaty są naliczane tylko za magazyn, który rzeczywiście używasz. Za pomocą **TRIM**, można zmniejszyć koszty Jeśli tworzenie dużych plików, a następnie później usunąć je. 

Aby sprawdzić **TRIM** ustawienie, otwórz wiersz polecenia na maszynie Wirtualnej Windows, a następnie wprowadź następujące polecenie:

```
fsutil behavior query DisableDeleteNotify
```

Jeśli polecenie zwraca 0, **TRIM** prawidłowo włączona. W przeciwnym razie, jeśli zwraca wartość 1, uruchom następujące polecenie, aby włączyć **TRIM**:

```
fsutil behavior set DisableDeleteNotify 0
```

Po usunięciu danych z dysku, można zapewnić **TRIM** operacji opróżniania poprawnie, uruchamiając defragmentowania z **TRIM**:

```
defrag.exe <volume:> -l
```

Możesz również sformatować wolumin, aby upewnić się, że cały wolumin są spacje.

## <a name="next-steps"></a>Kolejne kroki

- Możesz również [dołączanie dysku danych przy użyciu programu PowerShell](attach-disk-ps.md).
- Jeśli aplikacja wymaga użyć *D:* dysku do przechowywania danych, możesz [zmienić literę dysku tymczasowego Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
