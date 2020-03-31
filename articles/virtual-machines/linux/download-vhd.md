---
title: Pobieranie dysku VHD dla systemu Linux z platformy Azure
description: Pobierz dysk VHD systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968796"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobieranie dysku VHD dla systemu Linux z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego systemu Linux (VHD) z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure portal. 

Jeśli jeszcze tego nie zrobiono, zainstaluj platformę [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać dysku VHD z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny Wirtualnej. Aby pobrać dysk VHD, musisz zatrzymać maszynę wirtualną. Jeśli chcesz użyć dysku wirtualnego jako [obrazu](tutorial-custom-images.md) do tworzenia innych maszyn wirtualnych z nowymi dyskami, musisz anulować aprowizację i uogólnić system operacyjny zawarty w pliku i zatrzymać maszynę wirtualną. Aby użyć dysku VHD jako dysku dla nowego wystąpienia istniejącej maszyny Wirtualnej lub dysku z danymi, wystarczy zatrzymać i zdelocytować maszynę wirtualną.

Aby użyć dysku VHD jako obrazu do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1. Użyj SSH, nazwy konta i publicznego adresu IP maszyny Wirtualnej, aby połączyć się z nią i anulować jej anulowanie. Publiczny adres IP można znaleźć w programie [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametr +user usuwa również ostatnie konto użytkownika aprowizowanego. Jeśli są pieczenia poświadczeń konta w maszynie wirtualnej, pozostaw ten +parametr użytkownika. Poniższy przykład usuwa ostatnio aprowizowanego konta użytkownika:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Zaloguj się do swojego konta platformy Azure [za pomocą logowania az](https://docs.microsoft.com/cli/azure/reference-index).
3. Zatrzymaj i przydziel alokację maszyny Wirtualnej.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Uogólniaj maszynę wirtualną. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Aby użyć dysku VHD jako dysku dla nowego wystąpienia istniejącej maszyny Wirtualnej lub dysku z danymi, wykonaj następujące kroki:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2.  W menu po lewej stronie wybierz polecenie **Maszyny wirtualne**.
3.  Wybierz maszynę wirtualną z listy.
4.  Na stronie maszyny Wirtualnej wybierz pozycję **Zatrzymaj**.

    ![Zatrzymywanie maszyny wirtualnej](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generowanie adresu URL sygnatury dostępu Współ

Aby pobrać plik VHD, musisz wygenerować adres URL [sygnatury dostępu współdzielonego (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Po wygenerowaniu adresu URL do adresu URL jest przypisywany czas wygaśnięcia.

1.  W menu strony maszyny Wirtualnej wybierz pozycję **Dyski**.
2.  Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej, a następnie wybierz pozycję **Eksportuj dysk**.
3.  Wybierz **pozycję Generuj adres URL**.

    ![Generowanie adresu URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Pobieranie VHD

1.  Pod wygenerowanym adresem URL wybierz pozycję **Pobierz plik VHD**.
**
    ![Pobieranie VHD](./media/download-vhd/export-download.png)

2.  Aby rozpocząć pobieranie, może być konieczne **wybranie** opcji Zapisz w przeglądarce. Domyślną nazwą pliku VHD jest *abcd*.

    ![Wybierz pozycję Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać i utworzyć maszynę wirtualną z systemem Linux z dysku niestandardowego za pomocą interfejsu wiersza polecenia platformy Azure.](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
- [Zarządzanie dyskami platformy Azure w pliku CLI platformy Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

