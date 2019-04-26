---
title: Dołączanie dysku danych zarządzanych do maszyny Wirtualnej Windows — Azure | Dokumentacja firmy Microsoft
description: Jak dołączyć dysk danych zarządzanych do maszyny Wirtualnej z systemem Windows przy użyciu witryny Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 81fbf650597813baaf6618f82ef55a937f1870d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478178"
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

## <a name="next-steps"></a>Kolejne kroki

- Możesz również [dołączanie dysku danych przy użyciu programu PowerShell](attach-disk-ps.md).
- Jeśli aplikacja wymaga użyć *D:* dysku do przechowywania danych, możesz [zmienić literę dysku tymczasowego Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
