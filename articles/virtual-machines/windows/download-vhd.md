---
title: Pobierz zestaw Windows wirtualnego dysku twardego z platformy Azure | Dokumentacja firmy Microsoft
description: Pobierz dysku VHD systemu Windows przy użyciu portalu Azure.
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
ms.date: 06/26/2017
ms.author: cynthn
ms.openlocfilehash: 83d6715d6d8178e273131a6bc123627126644271
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobierz zestaw Windows wirtualnego dysku twardego z platformy Azure

W tym artykule opisano sposób pobierania [Windows wirtualnego dysku twardego (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pliku z platformy Azure przy użyciu portalu Azure. 

Maszynach wirtualnych (VM) platformy Azure używana [dysków](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jako miejsce do przechowywania systemu operacyjnego, aplikacji i danych. Wszystkie maszyny wirtualne Azure są co najmniej dwa dyski — dysk systemu operacyjnego Windows i dysku tymczasowym. Dysk systemu operacyjnego został początkowo utworzony z obrazu, a zarówno dysku systemu operacyjnego i obrazu są przechowywane na koncie magazynu Azure wirtualne dyski twarde. Maszyny wirtualne mogą także mieć co najmniej jeden dysk danych, które są także przechowywane jako wirtualne dyski twarde.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest podłączony do uruchomionej maszyny Wirtualnej. Należy zatrzymać maszynę Wirtualną do pobrania dysku VHD. Jeśli chcesz użyć wirtualnego dysku twardego [obrazu](tutorial-custom-images.md) utworzyć innych maszyn wirtualnych o nowe dyski, należy użyć [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) do uogólnienia systemu operacyjnego znajdującego się w pliku, a następnie Zatrzymaj maszynę Wirtualną. Do użycia jako dysk wirtualny dysk twardy nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku danych, wystarczy zatrzymać i cofnięcia przydzielenia Maszynie wirtualnej.

Aby użyć wirtualnego dysku twardego jako obraz do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1.  Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  [Połączenie z maszyną Wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na Maszynie wirtualnej Otwórz okno Wiersz polecenia jako administrator.
4.  Zmień katalog na *%windir%\system32\sysprep* i uruchom sysprep.exe.
5.  W oknie dialogowym Narzędzie przygotowania systemu wybierz **wprowadź systemu Out-of-Box Experience (OOBE)** i upewnij się, że **Generalize** jest zaznaczone.
6.  Wybierz opcje zamykania **zamknięcia**, a następnie kliknij przycisk **OK**. 

Do użycia jako dysk wirtualny dysk twardy nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku danych, wykonaj następujące kroki:

1.  W menu centralnym w portalu Azure, kliknij przycisk **maszyn wirtualnych**.
2.  Wybierz maszynę Wirtualną z listy.
3.  W bloku maszyny Wirtualnej, kliknij przycisk **zatrzymać**.

    ![Zatrzymanie maszyny Wirtualnej](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generowania adresu URL SAS

Aby pobrać plik wirtualnego dysku twardego, należy wygenerować [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresu URL. Podczas generowania adresu URL czas wygaśnięcia jest przypisana do adresu URL.

1.  W menu bloku dla maszyny Wirtualnej, kliknij przycisk **dysków**.
2.  Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej, a następnie kliknij przycisk **wyeksportować**.
3.  Czas wygaśnięcia adresu URL, aby ustawić *36000*.
4.  Kliknij przycisk **generowania adresu URL**.

    ![Generowania adresu URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Czas wygaśnięcia zwiększa się z domyślnej, aby zapewnić wystarczającą ilość czasu pobierania dużego pliku wirtualnego dysku twardego systemu operacyjnego Windows Server. Można oczekiwać, że plik wirtualnego dysku twardego, który zawiera systemu operacyjnego Windows Server, aby zająć wiele godzin, aby pobrać w zależności od połączenia. Jeśli pobierasz dysku VHD dla dysku danych, domyślny czas jest wystarczająca. 
> 
> 

## <a name="download-vhd"></a>Pobierz wirtualnego dysku twardego

1.  Pod adresem URL, który został wygenerowany kliknij przycisk Pobierz plik VHD.

    ![Pobierz wirtualnego dysku twardego](./media/download-vhd/export-download.png)

2.  Może zajść potrzeba kliknięcia przycisku **zapisać** w przeglądarce, aby rozpocząć pobieranie. Domyślna nazwa pliku VHD jest *abcd*.

    ![Kliknij przycisk Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Przekaż plik VHD na platformę Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Tworzenie dysków zarządzanych z niezarządzanego dysków na koncie magazynu](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Zarządzanie dyskami Azure przy użyciu programu PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

