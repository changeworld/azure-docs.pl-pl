---
title: Dołączanie zarządzanego dysku danych do maszyny Wirtualnej systemu Windows — Azure
description: Jak dołączyć zarządzany dysk danych do maszyny Wirtualnej systemu Windows przy użyciu witryny Azure portal.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919383"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Dołączanie zarządzanego dysku danych do maszyny Wirtualnej systemu Windows przy użyciu portalu Azure

W tym artykule pokazano, jak dołączyć nowy dysk danych zarządzanych do maszyny wirtualnej systemu Windows przy użyciu portalu Azure. Rozmiar maszyny Wirtualnej określa, ile dysków z danymi można dołączyć. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](sizes.md).


## <a name="add-a-data-disk"></a>Dodawanie dysku danych

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby dodać dysk danych. Wyszukaj i wybierz **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na stronie **Maszyna wirtualna** wybierz pozycję **Dyski**.
4. Na stronie **Dyski** wybierz pozycję **Dodaj dysk danych**.
5. Z listy rozwijanej nowego dysku wybierz pozycję **Utwórz dysk**.
6. Na stronie **Tworzenie dysku zarządzanego** wpisz nazwę dysku i w razie potrzeby dostosuj inne ustawienia. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.
7. Na stronie **Dyski** wybierz pozycję **Zapisz,** aby zapisać nową konfigurację dysku dla maszyny Wirtualnej.
8. Po tym, jak platforma Azure utworzy dysk i dołączy go do maszyny wirtualnej, nowy dysk zostanie wyświetlony w ustawieniach dysku maszyny wirtualnej w obszarze **Dyski danych**.


## <a name="initialize-a-new-data-disk"></a>Inicjowanie nowego dysku danych

1. Nawiąż połączenie z maszyną wirtualną.
1. Wybierz menu **Start** systemu Windows wewnątrz uruchomionej maszyny Wirtualnej i wprowadź **plik diskmgmt.msc** w polu wyszukiwania. Zostanie otwarta konsola **Zarządzanie dyskami.**
2. Zarządzanie dyskami rozpoznaje, że masz nowy, niezainicjowany dysk i zostanie wyświetlone okno **Inicjuj dysk.**
3. Sprawdź, czy nowy dysk jest zaznaczony, a następnie wybierz **przycisk OK,** aby go zainicjować.
4. Nowy dysk jest wyświetlany jako **nieprzydzielony**. Kliknij prawym przyciskiem myszy dowolne miejsce na dysku i wybierz pozycję **Nowy wolumin prosty**. Zostanie otwarte okno **Kreatora nowego woluminu prostego.**
5. Przejdź przez kreatora, zachowując wszystkie wartości domyślne, a po zakończeniu wybierz **pozycję Zakończ**.
6. Zamknij **zarządzanie dyskami**.
7. Zostanie wyświetlone wyskakujące okno z powiadomieniem, że należy sformatować nowy dysk, zanim będzie można go użyć. Wybierz **pozycję Formatuj dysk**.
8. W oknie **Formatowanie nowego dysku** sprawdź ustawienia, a następnie wybierz pozycję **Start**.
9. Pojawi się ostrzeżenie informujące, że formatowanie dysków powoduje wymazanie wszystkich danych. Kliknij przycisk **OK**.
10. Po zakończeniu formatowania wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

- Dysk z danymi można również [dołączyć za pomocą programu PowerShell](attach-disk-ps.md).
- Jeśli aplikacja musi używać dysku *D:* do przechowywania danych, można [zmienić literę dysku tymczasowego systemu Windows](change-drive-letter.md).
