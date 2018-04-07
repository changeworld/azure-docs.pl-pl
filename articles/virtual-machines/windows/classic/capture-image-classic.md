---
title: Przechwyć obraz maszyny Wirtualnej systemu Windows Azure | Dokumentacja firmy Microsoft
description: Przechwytywanie obrazu maszyny wirtualnej systemu Windows przy użyciu klasycznego modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 24366d3adf63fb6fafb8afa9f1a9fb359e88d8ed
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Przechwytywanie obrazu maszyny wirtualnej systemu Windows na platformie Azure utworzonego przy użyciu klasycznego modelu wdrażania
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Menedżer zasobów modelu informacji, zobacz [Przechwyć obraz zarządzanych uogólniony maszyny wirtualnej na platformie Azure](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule przedstawiono, jak przechwycić maszynę wirtualną platformy Azure systemem Windows, tak jak obraz służy do tworzenia innych maszyn wirtualnych. Ten obraz zawiera dysku systemu operacyjnego i wszystkich dysków danych, które są dołączone do maszyny wirtualnej. Nie ma wśród nich konfiguracje sieci, więc musisz skonfigurować konfiguracji sieci, podczas tworzenia innych maszyn wirtualnych korzystających z obrazu.

Azure zapisuje obraz w obszarze **obrazów maszyn wirtualnych (klasyczne)**, **obliczeniowe** usługi na liście, podczas wyświetlania wszystkich usług Azure. To jest tym samym miejscu, w którym są przechowywane wszystkie obrazy przekazanego. Aby uzyskać więcej informacji o obrazach, zobacz [o obrazów maszyn wirtualnych](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Tych krokach przyjęto założenie, że zostały już utworzeniu maszyny wirtualnej platformy Azure i skonfigurowaniu systemu operacyjnego, w tym dołączanie dowolnego dysku z danymi. Jeśli to nie zostało to jeszcze zrobione jeszcze, zobacz następujące artykuły, aby uzyskać informacje na temat tworzenia i przygotowywanie maszyny wirtualnej:

* [Utwórz maszynę wirtualną z obrazu](createportal.md)
* [Jak można dołączyć dysku danych do maszyny wirtualnej](attach-disk.md)
* Upewnij się, że role serwera są obsługiwane przy użyciu narzędzia Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Ten proces powoduje usunięcie oryginalna maszyna wirtualna po jego przechwyceniu.
>
>

Przed przechwyceniem obrazu maszyny wirtualnej platformy Azure, zaleca się docelowej maszyny wirtualnej można wykonać kopii zapasowej. Maszyny wirtualne platformy Azure można kopii zapasowej za pomocą usługi Kopia zapasowa Azure. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../../../backup/backup-azure-arm-vms.md). Inne rozwiązania są dostępne u certyfikowanych partnerów. Aby dowiedzieć się, co jest obecnie dostępne, przeszukaj witrynę Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Przechwytywanie maszyny wirtualnej
1. W [portalu Azure](http://portal.azure.com), **Connect** do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [jak zalogować się do maszyny wirtualnej z systemem Windows Server][How to sign in to a virtual machine running Windows Server].
2. Otwórz okno wiersza polecenia z uprawnieniami administratora.
3. Zmień katalog na `%windir%\system32\sysprep`, a następnie uruchom sysprep.exe.
4. **Narzędzie przygotowania systemu** zostanie wyświetlone okno dialogowe. Wykonaj następujące czynności:

   * W **Akcja oczyszczania systemu**, wybierz pozycję **wprowadź systemu Out-of-Box Experience (OOBE)** i upewnij się, że **Generalize** jest zaznaczony. Aby uzyskać więcej informacji o korzystaniu z programu Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie][How to Use Sysprep: An Introduction].
   * W **opcje zamykania**, wybierz pozycję **zamknięcia**.
   * Kliknij przycisk **OK**.

   ![Uruchom program Sysprep](./media/capture-image/SysprepGeneral.png)
5. Narzędzie Sysprep zamyka maszynę wirtualną, która zmieni stan maszyny wirtualnej w portalu Azure, aby **zatrzymane**.
6. W portalu Azure kliknij **maszyn wirtualnych (klasyczne)** i wybierz maszynę wirtualną, które mają być przechwytywane. **Obrazów maszyn wirtualnych (klasyczne)** grupy znajduje się w obszarze **obliczeniowe** podczas wyświetlania **wszystkie usługi**.

7. Na pasku poleceń, kliknij przycisk **przechwytywania**.

   ![Przechwytywanie maszyny wirtualnej](./media/capture-image/CaptureVM.png)

   **Przechwytywanie maszyny wirtualnej** zostanie wyświetlone okno dialogowe.

8. W **nazwa obrazu**, wpisz nazwę nowego obrazu. W **etykiety obrazu**, wpisz etykietę dla nowego obrazu.

9. Kliknij przycisk **uruchomiono program Sysprep na maszynie wirtualnej**. To pole wyboru odwołuje się do akcji przy użyciu narzędzia Sysprep w krokach 3 – 5. Obraz _musi_ uogólnione przed dodaniem obrazu systemu Windows Server do zestawu niestandardowych obrazów, uruchamiając program Sysprep.

10. Po zakończeniu Przechwytywanie nowego obrazu staje się dostępna **Marketplace**w **obliczeniowe**, **obrazów maszyn wirtualnych (klasyczne)** kontenera.

    ![Pomyślne przechwytywania obrazu](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Kolejne kroki
Obraz jest gotowy do utworzenia maszyny wirtualnej. Aby to zrobić, należy utworzyć maszynę wirtualną, wybierając **wszystkie usługi** element menu u dołu menu usługi, następnie **obrazów maszyn wirtualnych (klasyczne)** w **obliczeniowe** grupy. Aby uzyskać instrukcje, zobacz [Utwórz maszynę wirtualną z obrazu](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
