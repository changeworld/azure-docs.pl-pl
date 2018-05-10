---
title: Przechwyć obraz maszyny wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechwytywania obrazu opartych na systemie Linux Azure maszyny wirtualnej (VM) utworzone za pomocą klasycznym modelu wdrażania.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 6071e025352199c5ec559598a580a918c2e9c666
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Jak przechwycić klasyczną maszynę wirtualną z systemem Linux jako obraz
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule przedstawiono sposób Przechwytywanie klasycznego Azure maszyny wirtualnej (VM) systemem Linux jako obrazu, aby utworzyć pozostałe maszyny wirtualne. Ten obraz zawiera dysku systemu operacyjnego i dyskach danych dołączonych do maszyny Wirtualnej. Nie ma wśród nich konfiguracji sieci, dlatego należy skonfigurować, które po utworzeniu maszyny Wirtualnej z obrazu.

Azure zapisuje obraz w obszarze **obrazów**, oraz wszystkie obrazy zostały przekazane. Aby uzyskać więcej informacji na temat obrazów, zobacz [o obrazy maszyny wirtualnej na platformie Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Przed rozpoczęciem
Tych krokach przyjęto założenie, że już utworzone przy użyciu klasycznego modelu wdrożenia maszyny Wirtualnej platformy Azure i skonfigurować systemu operacyjnego, w tym dołączanie dowolnego dysku z danymi. Jeśli musisz utworzyć Maszynę wirtualną, przeczytaj [tworzenie maszyny wirtualnej systemu Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Przechwytywanie maszyny wirtualnej
1. [Połączenie z maszyną Wirtualną](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) przy użyciu klienta SSH wybranych przez użytkownika.
2. W oknie SSH wpisz następujące polecenie. Dane wyjściowe z `waagent` mogą się nieco różnić w zależności od wersji tego narzędzia:

    ```bash
    sudo waagent -deprovision+user
    ```

    Poprzednie polecenie próbuje wyczyścić systemu i zapewnić ich odpowiednie dla reprovisioning. Tę operację wykonuje następujące zadania:

   * Usuwa kluczy SSH hosta (jeśli Provisioning.RegenerateSshHostKeyPair "y" w pliku konfiguracji)
   * Czyści konfiguracji serwera nazw w /etc/resolv.conf
   * Usuwa `root` hasło użytkownika w tle/etc / (jeśli Provisioning.DeleteRootPassword "y" w pliku konfiguracji)
   * Usunięcie buforowanych dzierżaw klientów DHCP
   * Zresetowanie nazwy hosta na wartość localhost.localdomain
   * Usuwa konto użytkownika (uzyskane z /var/lib/waagent) udostępniane przez ostatnie **i skojarzone dane**.

     > [!NOTE]
     > Cofanie zastrzegania usuwa pliki i dane do "generalize" obrazu. To polecenie należy uruchamiać tylko na maszynie Wirtualnej, który ma zostać przechwytywania jako nowy szablon obrazu. Nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne lub nadaje się do ponownej dystrybucji osobom trzecim.

3. Typ **y** aby kontynuować. Możesz dodać `-force` parametr, aby uniknąć tego kroku potwierdzenia.
4. Typ **zakończenia** zamykania klienta SSH.

   > [!NOTE]
   > Pozostałe kroki założono, że masz już [zainstalowane interfejsu wiersza polecenia Azure](../../../cli-install-nodejs.md) na komputerze klienckim. Można również wykonać następujące kroki [portalu Azure](http://portal.azure.com).

5. Na komputerze klienckim otwórz wiersza polecenia platformy Azure i zaloguj się do subskrypcji platformy Azure. Aby uzyskać więcej informacji, przeczytaj [połączenie z subskrypcją platformy Azure z wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > W portalu Azure należy zalogować się do portalu.

6. Upewnij się, że jesteś w trybie zarządzania usługami:

    ```azurecli
    azure config mode asm
    ```

7. Zamknij maszynę Wirtualną, która jest już anulowana. Poniższy przykład wyłączania maszyny Wirtualnej o nazwie `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Jeśli to konieczne, można wyświetlić listę wszystkich maszyny wirtualne utworzone w ramach subskrypcji przy użyciu `azure vm list`

   > [!NOTE]
   > Jeśli używasz portalu Azure, wybierz maszynę Wirtualną i kliknij przycisk **zatrzymać** można zamknąć maszyny Wirtualnej.

8. Po zatrzymaniu maszyny Wirtualnej Przechwyć obraz. Poniższy przykład przechwytuje maszynę Wirtualną o nazwie `myVM` i tworzy uogólniony obraz o nazwie `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` Podpolecenia Usuwa oryginalny maszyny wirtualnej.

    > [!NOTE]
    > W portalu Azure, można przechwycić obraz, wybierając **obrazu** w menu Centrum. Należy podać następujące informacje dotyczące obrazu: Nazwa grupy zasobów, lokalizacji, typu systemu operacyjnego i ścieżki do magazynu obiektów blob.

9. Nowy obraz jest teraz dostępny na liście obrazów, które mogą służyć do konfigurowania żadnych nowej maszyny Wirtualnej. Możesz je wyświetlić przy użyciu polecenia:

   ```azurecli
   azure vm image list
   ```

   Na [portalu Azure](http://portal.azure.com), nowy obraz zostanie wyświetlony w **obrazów maszyn wirtualnych (klasyczne)** należący do **obliczeniowe** usług. Dostęp można uzyskać **obrazów maszyn wirtualnych (klasyczne)** , klikając **wszystkie usługi** w górnej części platformy Azure Usługa listy, a następnie sprawdzając **obliczeniowe** usług.   

   ![Pomyślne przechwytywania obrazu](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Kolejne kroki
Obraz jest gotowy do użycia do tworzenia maszyn wirtualnych. Możesz użyć polecenia interfejsu wiersza polecenia Azure `azure vm create` i podaj nazwę obraz został utworzony. Aby uzyskać więcej informacji, zobacz [z klasycznego modelu wdrażania przy użyciu interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Można również użyć [portalu Azure](http://portal.azure.com) Tworzenie niestandardowych maszyny Wirtualnej za pomocą **obrazu** — metoda i wybierając obraz został utworzony. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej niestandardowe][How to Create a Custom Virtual Machine].

**Zobacz też:** [Podręcznik użytkownika Azure agenta systemu Linux](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
