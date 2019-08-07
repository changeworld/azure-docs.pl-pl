---
title: Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure | Microsoft Docs
description: Pobierz dysk VHD systemu Windows przy użyciu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: ebcc8301fa3693880974e45b594be218905e8311
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775409"
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego (VHD) systemu Windows z platformy Azure przy użyciu Azure Portal.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Musisz zatrzymać maszynę wirtualną, aby pobrać dysk VHD. Jeśli chcesz używać dysku VHD jako [obrazu](tutorial-custom-images.md) do tworzenia innych maszyn wirtualnych z nowymi dyskami, użyj programu [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) do uogólnienia systemu operacyjnego zawartego w pliku, a następnie Zatrzymaj maszynę wirtualną. Aby można było użyć wirtualnego dysku twardego dla nowego wystąpienia istniejącej maszyny wirtualnej lub dysku z danymi, należy zatrzymać i cofnąć alokację maszyny wirtualnej.

Aby użyć dysku VHD jako obrazu do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1.  Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  [Nawiąż połączenie z maszyną wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na maszynie wirtualnej Otwórz okno wiersza polecenia jako administrator.
4.  Zmień katalog na *%windir%\System32\Sysprep* i uruchom plik Sysprep. exe.
5.  W oknie dialogowym Narzędzie przygotowywania systemu wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i upewnij się, że opcja **generalize** jest zaznaczona.
6.  W opcje zamykania wybierz pozycję **Zamknij**, a następnie kliknij przycisk **OK**. 

Aby użyć dysku VHD jako dysku dla nowego wystąpienia istniejącej maszyny wirtualnej lub dysku danych, wykonaj następujące kroki:

1.  W menu centrum w Azure Portal kliknij pozycję **Virtual Machines**.
2.  Z listy wybierz maszynę wirtualną.
3.  W bloku maszyny wirtualnej kliknij pozycję **Zatrzymaj**.

    ![Zatrzymaj maszynę wirtualną](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generuj adres URL SAS

Aby pobrać plik VHD, należy wygenerować adres URL sygnatury [dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po wygenerowaniu adresu URL zostanie do niego przypisany czas wygaśnięcia.

1.  W menu bloku maszyny wirtualnej kliknij pozycję **dyski**.
2.  Wybierz dysk systemu operacyjnego dla maszyny wirtualnej, a następnie kliknij pozycję **eksport dysku**.
3.  Ustaw czas wygaśnięcia adresu URL na *36000*.
4.  Kliknij pozycję **Generuj adres URL**.

    ![Generuj adres URL](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> Czas wygaśnięcia zostanie zwiększony z ustawienia domyślnego, aby zapewnić wystarczającą ilość czasu na pobranie dużego pliku VHD dla systemu operacyjnego Windows Server. Można oczekiwać, że plik VHD zawierający system operacyjny Windows Server zostanie pobrany do kilku godzin w zależności od połączenia. Jeśli pobierasz dysk VHD dla dysku z danymi, wystarczy domyślny czas. 
> 
> 

## <a name="download-vhd"></a>Pobierz dysk VHD

1.  W obszarze wygenerowanego adresu URL kliknij pozycję Pobierz plik VHD.

    ![Pobierz dysk VHD](./media/download-vhd/export-download.png)

2.  Aby rozpocząć pobieranie, może być konieczne kliknięcie przycisku **Zapisz** w przeglądarce. Domyślna nazwa pliku VHD jest *ABCD*.

    ![Kliknij przycisk Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [przekazać plik VHD na platformę Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Utwórz dyski zarządzane na podstawie dysków niezarządzanych na koncie magazynu](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Zarządzanie dyskami platformy Azure za pomocą programu PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

