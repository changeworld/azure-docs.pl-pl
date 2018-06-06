---
title: Pobierz dysku VHD systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Pobierz dysku VHD systemu Linux przy użyciu wiersza polecenia platformy Azure i portalu Azure.
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
ms.openlocfilehash: 1c6751d980a7bb28e58a3aa00514411959f515d7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725870"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobierz dysku VHD systemu Linux na platformie Azure

W tym artykule opisano sposób pobierania [Linux wirtualnego dysku twardego (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pliku z platformy Azure przy użyciu wiersza polecenia platformy Azure i portalu Azure. 

Jeśli jeszcze tego nie zrobiono, zainstaluj [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest podłączony do uruchomionej maszyny Wirtualnej. Należy zatrzymać maszynę Wirtualną do pobrania dysku VHD. Jeśli chcesz użyć wirtualnego dysku twardego [obrazu](tutorial-custom-images.md) Aby utworzyć pozostałe maszyny wirtualne o nowe dyski, należy anulowanie zastrzeżenia i uogólnienia systemu operacyjnego znajdującego się w pliku i Zatrzymaj maszynę Wirtualną. Do użycia jako dysk wirtualny dysk twardy nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku danych, wystarczy zatrzymać i cofnięcia przydzielenia Maszynie wirtualnej.

Aby użyć wirtualnego dysku twardego jako obraz do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1. Aby podłączyć się do niego i anulowanie zastrzeżenia go, należy użyć SSH, nazwę konta i publiczny adres IP maszyny wirtualnej. Można znaleźć publicznego adresu IP z [az sieci ip publicznego Pokaż](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). + Parametr użytkownika spowoduje również usunięcie ostatniego elastycznie konta. Jeśli poświadczenia konta w celu maszyny Wirtualnej są pieczenia, Opuść to + parametr użytkownika. Poniższy przykład umożliwia usunięcie ostatniego elastycznie konta:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Zaloguj się do konta platformy Azure z [logowania az](https://docs.microsoft.com/cli/azure/reference-index#az_login).
3. Zatrzymaj i cofnięcia przydzielenia Maszynie wirtualnej.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize maszyny Wirtualnej. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Do użycia jako dysk wirtualny dysk twardy nowe wystąpienie klasy istniejącej maszyny Wirtualnej lub dysku danych, wykonaj następujące kroki:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  W menu Centrum kliknij pozycję **Virtual Machines**.
3.  Wybierz maszynę Wirtualną z listy.
4.  W bloku maszyny Wirtualnej, kliknij przycisk **zatrzymać**.

    ![Zatrzymanie maszyny Wirtualnej](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generowania adresu URL SAS

Aby pobrać plik wirtualnego dysku twardego, należy wygenerować [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresu URL. Podczas generowania adresu URL czas wygaśnięcia jest przypisana do adresu URL.

1.  W menu bloku dla maszyny Wirtualnej, kliknij przycisk **dysków**.
2.  Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej, a następnie kliknij przycisk **wyeksportować**.
3.  Kliknij przycisk **generowania adresu URL**.

    ![Generowania adresu URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Pobierz wirtualnego dysku twardego

1.  Pod adresem URL, który został wygenerowany kliknij przycisk Pobierz plik VHD.

    ![Pobierz wirtualnego dysku twardego](./media/download-vhd/export-download.png)

2.  Może zajść potrzeba kliknięcia przycisku **zapisać** w przeglądarce, aby rozpocząć pobieranie. Domyślna nazwa pliku VHD jest *abcd*.

    ![Kliknij przycisk Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [przekazywanie i Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku 2.0 interfejsu wiersza polecenia Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Zarządzanie dyskami Azure, Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

