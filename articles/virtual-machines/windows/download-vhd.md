---
title: Pobierz Windows wirtualnego dysku twardego na platformie Azure | Dokumentacja firmy Microsoft
description: Pobieranie wirtualnego dysku twardego Windows przy użyciu witryny Azure portal.
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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076331"
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobierz Windows wirtualnego dysku twardego z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego (VHD) Windows na platformie Azure przy użyciu witryny Azure portal.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny Wirtualnej. Należy zatrzymać maszynę Wirtualną, aby pobrać wirtualnego dysku twardego. Jeśli chcesz używać wirtualnego dysku twardego [obraz](tutorial-custom-images.md) Aby utworzyć inne maszyny wirtualne przy użyciu nowych dysków, należy użyć [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) aby zawarte w pliku systemu operacyjnego, a następnie Zatrzymaj maszynę Wirtualną. Na potrzeby dysku VHD jako dysk nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku z danymi, wystarczy Zatrzymaj i Cofnij Przydział maszyny Wirtualnej.

Aby użyć wirtualnego dysku twardego jako obraz do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1.  Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  [Łączenie z maszyną wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na maszynie Wirtualnej Otwórz okno wiersza polecenia jako administrator.
4.  Zmień katalog na *%windir%\system32\sysprep* i uruchom sysprep.exe.
5.  W oknie dialogowym narzędzia przygotowywania systemu wybierz **wprowadź System Out-of-Box środowiska (OOBE)** i upewnij się, że **Generalize** jest zaznaczone.
6.  W oknie dialogowym Opcje zamykania wybierz **zamknięcia**, a następnie kliknij przycisk **OK**. 

Na potrzeby dysku VHD jako dysk nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysk danych, wykonaj następujące kroki:

1.  W menu Centrum w witrynie Azure portal, kliknij przycisk **maszyn wirtualnych**.
2.  Wybierz maszynę Wirtualną z listy.
3.  W bloku maszyny wirtualnej, kliknij **zatrzymać**.

    ![Zatrzymywanie maszyny Wirtualnej](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generowanie adresu URL sygnatury dostępu Współdzielonego

Aby pobrać plik wirtualnego dysku twardego, należy wygenerować [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresu URL. Podczas generowania adresu URL czas wygaśnięcia jest przypisany do adresu URL.

1.  W menu bloku maszyny Wirtualnej, kliknij przycisk **dysków**.
2.  Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej, a następnie kliknij przycisk **wyeksportować**.
3.  Ustaw czas wygaśnięcia adresu URL, aby *36000*.
4.  Kliknij przycisk **generowania adresu URL**.

    ![Generuj adres URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Czas wygaśnięcia zwiększa się w domyślnej, aby zapewnić wystarczającą ilość czasu pobierania dużych plików wirtualnego dysku twardego systemu operacyjnego Windows Server. Można oczekiwać, że plik wirtualnego dysku twardego zawierającego system operacyjny Windows Server, aby zająć wiele godzin, aby pobrać w zależności od połączenia. Jeśli pobierasz wirtualnego dysku twardego dla dysku z danymi, domyślny czas jest wystarczająca. 
> 
> 

## <a name="download-vhd"></a>Pobieranie wirtualnego dysku twardego

1.  W polu adres URL, który został wygenerowany kliknij pozycję Pobierz plik VHD.

    ![Pobieranie wirtualnego dysku twardego](./media/download-vhd/export-download.png)

2.  Może być konieczne kliknięcie **Zapisz** w przeglądarce, aby rozpocząć pobieranie. Domyślną nazwą pliku wirtualnego dysku twardego jest *abcd*.

    ![Kliknij przycisk Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [przesłać plik VHD na platformie Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Tworzenie dysków zarządzanych z dysków niezarządzanych na koncie magazynu](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Zarządzanie dyskami platformy Azure przy użyciu programu PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

