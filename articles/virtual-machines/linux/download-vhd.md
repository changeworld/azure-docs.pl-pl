---
title: Pobieranie wirtualnego dysku twardego systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Pobieranie wirtualnego dysku twardego systemu Linux przy użyciu wiersza polecenia platformy Azure i witryny Azure portal.
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
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390233"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego systemu Linux na platformie Azure

W tym artykule dowiesz się, jak pobrać pliku wirtualnego dysku twardego (VHD) systemu Linux na platformie Azure przy użyciu wiersza polecenia platformy Azure i witryny Azure portal. 

Jeśli jeszcze tego nie zrobiono, zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny Wirtualnej. Należy zatrzymać maszynę Wirtualną, aby pobrać wirtualnego dysku twardego. Jeśli chcesz używać wirtualnego dysku twardego [obraz](tutorial-custom-images.md) Aby utworzyć inne maszyny wirtualne przy użyciu nowych dysków, musisz anulowanie aprowizacji i Uogólnianie systemu operacyjnego, znajdujący się w pliku i Zatrzymaj maszynę Wirtualną. Na potrzeby dysku VHD jako dysk nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku z danymi, wystarczy Zatrzymaj i Cofnij Przydział maszyny Wirtualnej.

Aby użyć wirtualnego dysku twardego jako obraz do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1. Użyj protokołu SSH, nazwę konta i publiczny adres IP maszyny wirtualnej nawiązać z nim i jego deprovision. Można znaleźć publiczny adres IP z [az sieci public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). + Parametr użytkownika spowoduje również usunięcie ostatnie aprowizowane konto użytkownika. Jeśli są pieczenie poświadczenia konta w do maszyny Wirtualnej, należy pozostawić się z tą + parametru user. Poniższy przykład usuwa ostatnie aprowizowane konto użytkownika:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Zaloguj się do konta platformy Azure za pomocą [az login](https://docs.microsoft.com/cli/azure/reference-index).
3. Zatrzymaj i Cofnij Przydział maszyny Wirtualnej.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Uogólnianie maszyny Wirtualnej. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Na potrzeby dysku VHD jako dysk nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysk danych, wykonaj następujące kroki:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  W menu Centrum kliknij pozycję **Virtual Machines**.
3.  Wybierz maszynę Wirtualną z listy.
4.  W bloku maszyny wirtualnej, kliknij **zatrzymać**.

    ![Zatrzymywanie maszyny Wirtualnej](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generowanie adresu URL sygnatury dostępu Współdzielonego

Aby pobrać plik wirtualnego dysku twardego, należy wygenerować [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresu URL. Podczas generowania adresu URL czas wygaśnięcia jest przypisany do adresu URL.

1.  W menu bloku maszyny Wirtualnej, kliknij przycisk **dysków**.
2.  Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej, a następnie kliknij przycisk **wyeksportować**.
3.  Kliknij przycisk **generowania adresu URL**.

    ![Generuj adres URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Pobieranie wirtualnego dysku twardego

1.  W polu adres URL, który został wygenerowany kliknij pozycję Pobierz plik VHD.

    ![Pobieranie wirtualnego dysku twardego](./media/download-vhd/export-download.png)

2.  Może być konieczne kliknięcie **Zapisz** w przeglądarce, aby rozpocząć pobieranie. Domyślną nazwą pliku wirtualnego dysku twardego jest *abcd*.

    ![Kliknij przycisk Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [przekazywanie i tworzenie maszyny Wirtualnej z systemem Linux z niestandardowego dysku przy użyciu wiersza polecenia platformy Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Zarządzanie dyskami platformy Azure, interfejsu wiersza polecenia Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

