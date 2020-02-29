---
title: Dołączanie zarządzanego dysku danych do maszyny wirtualnej z systemem Windows — Azure
description: Jak dołączyć dysk danych zarządzanych do maszyny Wirtualnej z systemem Windows przy użyciu witryny Azure portal.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919383"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Dołączanie dysku danych zarządzanych do maszyny Wirtualnej z systemem Windows przy użyciu witryny Azure portal

W tym artykule pokazano, jak dołączyć nowy dysk danych zarządzanych na maszynę wirtualną (VM) Windows przy użyciu witryny Azure portal. Rozmiar maszyny Wirtualnej Określa, jak wiele dysków z danymi można dołączać. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md).


## <a name="add-a-data-disk"></a>Dodawanie dysku danych

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby dodać dysk danych. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na stronie **maszyna wirtualna** wybierz pozycję **dyski**.
4. Na stronie **dyski** wybierz pozycję **Dodaj dysk danych**.
5. Na liście rozwijanej dla nowego dysku wybierz pozycję **Utwórz dysk**.
6. Na stronie **Utwórz dysk zarządzany** wpisz nazwę dysku i dostosuj inne ustawienia w razie potrzeby. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.
7. Na stronie **dyski** wybierz pozycję **Zapisz** , aby zapisać nową konfigurację dysków dla maszyny wirtualnej.
8. Po utworzeniu dysku przez platformę Azure i dołączeniu go do maszyny wirtualnej nowy dysk zostanie wyświetlony na liście ustawień dyskowych maszyny wirtualnej w obszarze **dyski danych**.


## <a name="initialize-a-new-data-disk"></a>Inicjowanie nowy dysk danych

1. Nawiąż połączenie z maszyną wirtualną.
1. Wybierz menu **Start** systemu Windows w ramach URUCHOMIONEJ maszyny wirtualnej i w polu wyszukiwania wpisz **diskmgmt. msc** . Zostanie otwarta konsola **Zarządzanie dyskami** .
2. Funkcja zarządzania dyskami rozpoznaje, że masz nowy, niezainicjowany dysk i zostanie wyświetlone okno **Inicjowanie dysku** .
3. Sprawdź, czy wybrano nowy dysk, a następnie wybierz przycisk **OK** , aby go zainicjować.
4. Nowy dysk zostanie wyświetlony jako **przydzielony**. Kliknij prawym przyciskiem myszy w dowolnym miejscu na dysku i wybierz pozycję **Nowy wolumin prosty**. Zostanie otwarte okno **Kreator nowych woluminów prostych** .
5. Wykonaj czynności wykonywane przez kreatora, zachowując wszystkie ustawienia domyślne i po zakończeniu wybierz pozycję **Zakończ**.
6. Zamknij **przystawkę Zarządzanie dyskami**.
7. Pojawi się okno podręczne, informujący o tym, należy sformatować nowy dysk przed jego użyciem. Wybierz pozycję **Formatuj dysk**.
8. W oknie **Formatuj nowy dysk** Sprawdź ustawienia, a następnie wybierz pozycję **Uruchom**.
9. Zostanie wyświetlone ostrzeżenie z informacją, że formatowanie dyski na partycje powoduje usunięcie wszystkich danych. Kliknij przycisk **OK**.
10. Po zakończeniu formatowania wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

- [Dysk danych można również dołączyć przy użyciu programu PowerShell](attach-disk-ps.md).
- Jeśli aplikacja musi używać dysku *D:* do przechowywania danych, można [zmienić literę dysku na dysku tymczasowym systemu Windows](change-drive-letter.md).
