---
title: Przechwytywanie obrazu maszyny Wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechwytywania obrazu opartych na systemie Linux maszyn wirtualnych (VM) utworzone za pomocą klasycznego modelu wdrażania.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: ae87af45ffc442c0de6c7f703694a994e536cdb8
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929216"
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Jak przechwycić klasyczną maszynę wirtualną z systemem Linux jako obraz
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule pokazano, jak przechwytywanie klasycznej maszyny wirtualnej platformy Azure (VM) z systemem Linux jako obraz do tworzenia innych maszyn wirtualnych. Ten obraz zawiera dysk systemu operacyjnego i dysków danych podłączonych do maszyny Wirtualnej. Nie zawiera konfiguracji sieci, dlatego musisz skonfigurować podczas tworzenia maszyny Wirtualnej z obrazu.

Azure zapisuje obraz w ramach **obrazów**, wraz z wszystkie obrazy zostały przekazane. Aby uzyskać więcej informacji o obrazach, zobacz [dotyczące obrazów maszyn wirtualnych na platformie Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Przed rozpoczęciem
Tych krokach założono, że już utworzyć Maszynę wirtualną platformy Azure przy użyciu klasycznego modelu wdrożenia i skonfigurowana systemu operacyjnego, dołączając dyski z danymi. Jeśli musisz utworzyć Maszynę wirtualną, zapoznaj się z [jak utworzyć maszynę wirtualną z systemem Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Przechwytywanie maszyny wirtualnej
1. [Łączenie z maszyną wirtualną](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) za pomocą dowolnego klienta SSH.
2. W oknie SSH wpisz następujące polecenie. Dane wyjściowe z `waagent` mogą się nieco różnić w zależności od wersji tego narzędzia:

    ```bash
    sudo waagent -deprovision+user
    ```

    Poprzednie polecenie próbuje wyczyścić systemu, co dotyczy reprovisioning. Ta operacja wykonuje następujące zadania:

   * Usuwa klucze hosta SSH (jeśli Provisioning.RegenerateSshHostKeyPair "y" w pliku konfiguracji)
   * Czyści konfiguracji w /etc/resolv.conf
   * Usuwa `root` hasło użytkownika za pomocą/etc/w tle (jeśli Provisioning.DeleteRootPassword "y" w pliku konfiguracji)
   * Usunięcie buforowanych dzierżaw klientów DHCP
   * Zresetowanie nazwy hosta na wartość localhost.localdomain
   * Usuwa ostatnie aprowizowane konto użytkownika (uzyskana z /var/lib/waagent) **i skojarzone dane**.

     > [!NOTE]
     > Anulowanie aprowizacji usuwa pliki i dane "Uogólnij" obraz. To polecenie można uruchamiać tylko na maszynie Wirtualnej, którą zamierzasz przechwycić jako nowy szablon obrazu. Nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne lub nadaje się do redystrybucji stronom trzecim.

3. Typ **y** aby kontynuować. Możesz dodać `-force` parametru, aby uniknąć tego kroku potwierdzenia.
4. Typ **zakończenia** zamknąć klienta SSH.

   > [!NOTE]
   > Pozostałe kroki zakładają, że już [zainstalowany interfejs wiersza polecenia platformy Azure](../../../cli-install-nodejs.md) na swoim komputerze klienckim. Można również wykonać następujące kroki [witryny Azure portal](http://portal.azure.com).

5. Na komputerze klienckim otwórz wiersza polecenia platformy Azure i zaloguj się do subskrypcji platformy Azure. Aby uzyskać więcej informacji, przeczytaj [Połącz z subskrypcją platformy Azure z wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > W witrynie Azure portal Zaloguj się do portalu.

6. Upewnij się, że jesteś w trybie zarządzania usługami:

    ```azurecli
    azure config mode asm
    ```

7. Zamknij maszynę Wirtualną, która już anulowanie aprowizacji. Poniższy przykład wyłącza maszynę Wirtualną o nazwie `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Jeśli to konieczne, można wyświetlić listę wszystkich maszyn wirtualnych tworzonych w ramach subskrypcji za pomocą `azure vm list`

   > [!NOTE]
   > Jeśli używasz witryny Azure portal, wybierz maszynę Wirtualną i kliknij **zatrzymać** do zamykania maszyny Wirtualnej.

8. Po zatrzymaniu maszyny Wirtualnej Przechwyć obraz. Poniższy przykład przechwytuje maszynę Wirtualną o nazwie `myVM` i tworzy uogólnionego obrazu o nazwie `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` Podpolecenia usuwa oryginalną maszynę wirtualną.

    > [!NOTE]
    > W witrynie Azure portal można przechwycić obrazu, wybierając **obraz** w menu Centrum. Należy podać następujące informacje dotyczące obrazu: nazwę, grupę zasobów, lokalizacji, typu systemu operacyjnego i ścieżki do magazynu obiektów blob.

9. Nowy obraz jest teraz dostępne na liście obrazów, które mogą być używane do konfigurowania dowolnej nowej maszyny Wirtualnej. Istnieje możliwość wyświetlenia, za pomocą polecenia:

   ```azurecli
   azure vm image list
   ```

   Na [witryny Azure portal](http://portal.azure.com), nowy obraz jest wyświetlany w **obrazów maszyn wirtualnych (klasyczne)** należący do **obliczenia** usług. Możesz uzyskać dostęp **obrazów maszyn wirtualnych (klasyczne)** , klikając **wszystkich usług** u góry Azure usługi listy, a następnie wyszukiwanie w **obliczenia** usług.   

   ![Pomyślne przechwytywania obrazu](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Kolejne kroki
Obraz jest gotowy do użycia do tworzenia maszyn wirtualnych. Polecenie wiersza polecenia platformy Azure `azure vm create` i podaj nazwę obrazu, został utworzony. Aby uzyskać więcej informacji, zobacz [przy użyciu wiersza polecenia platformy Azure przy użyciu klasycznego modelu wdrażania](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Można również użyć [witryny Azure portal](http://portal.azure.com) utworzyć niestandardową maszynę Wirtualną przy użyciu **obraz** metody i wybierając polecenie Obraz został utworzony. Aby uzyskać więcej informacji, zobacz [sposób tworzenia maszyny Wirtualnej niestandardowego][How to Create a Custom Virtual Machine].

**Zobacz również:** [przewodnik użytkownika agenta platformy Azure](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
