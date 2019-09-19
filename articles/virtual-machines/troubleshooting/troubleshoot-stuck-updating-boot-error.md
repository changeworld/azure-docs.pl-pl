---
title: Uruchamianie maszyny wirtualnej platformy Azure jest zablokowane w Windows Update | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem występujący w przypadku zablokowania uruchomienia maszyny wirtualnej platformy Azure w usłudze Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 226151d81319dc4e6f132e76ce2d310f88a484e8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087018"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Uruchamianie maszyny wirtualnej platformy Azure jest zablokowane w usłudze Windows Update

Ten artykuł pomaga w rozwiązaniu problemu, gdy maszyna wirtualna (VM) jest zablokowana na Windows Update etapie podczas uruchamiania. 

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano użycie Menedżer zasobów model wdrażania. Zalecamy używanie tego modelu w przypadku nowych wdrożeń zamiast korzystania z klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw

 Nie uruchomiono maszyny wirtualnej z systemem Windows. Po sprawdzeniu zrzutów ekranu w oknie [Diagnostyka rozruchu](../troubleshooting/boot-diagnostics.md) zobaczysz, że uruchamianie jest zablokowane w procesie aktualizacji. Poniżej przedstawiono przykłady komunikatów, które mogą zostać wyświetlone:

- Instalacja systemu Windows # #% nie wyłącza komputera. Zajmie to trochę czasu, gdy komputer zostanie uruchomiony ponownie kilka razy
- Zachowaj swój komputer, dopóki nie zostanie to zrobione. Trwa Instalowanie aktualizacji Update #... 
- Nie można ukończyć aktualizacji — cofanie zmian nie powoduje wyłączenia komputera
- Niepowodzenie podczas konfigurowania aktualizacji systemu Windows wycofywanie zmian nie powoduje wyłączenia komputera
- Błąd < kod błędu > stosowania operacji aktualizacji # # # # # z # # # # # (\Regist...)
- Błąd krytyczny < kod błędu > stosowania operacji aktualizacji # # # # # z # # # # # ($ $...)


## <a name="solution"></a>Rozwiązanie

W zależności od liczby aktualizacji, które są instalowane lub wycofywane, proces aktualizacji może chwilę potrwać. Pozostaw maszynę wirtualną w tym stanie przez 8 godzin. Jeśli maszyna wirtualna nadal znajduje się w tym stanie po tym okresie, uruchom ponownie maszynę wirtualną z Azure Portal i sprawdź, czy może ona zostać uruchomiona normalnie. Jeśli ten krok nie działa, wypróbuj poniższe rozwiązanie.

### <a name="remove-the-update-that-causes-the-problem"></a>Usuń aktualizację, która powoduje problem

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md). 
2. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).
3. Po dołączeniu dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania Uruchom polecenie **diskmgmt. msc** , aby otworzyć przystawkę Zarządzanie dyskami, a następnie upewnij się, że dołączony dysk jest w **trybie online**. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego, znajdującego się w folderze \Windows. Jeśli dysk jest zaszyfrowany, przed przejściem do następnego kroku w tym dokumencie należy go odszyfrować.

4. Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenie, aby uzyskać listę pakietów aktualizacji znajdujących się na dołączonym dysku systemu operacyjnego:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Na przykład jeśli dołączony dysk systemu operacyjnego to dysk F, uruchom następujące polecenie:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otwórz plik C:\temp\Patch_level.txt, a następnie przeczytaj go od dołu. Znajdź aktualizację, która jest w stanie oczekiwania na **instalację** lub **odinstalowanie** .  Poniżej znajduje się przykład stanu aktualizacji:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Usuń aktualizację, która spowodowała problem:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Przykład: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > W zależności od rozmiaru pakietu narzędzie DISM zajmie trochę czasu, aby przetworzyć dezinstalację. Zwykle proces zostanie zakończony w ciągu 16 minut.

7. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Następnie sprawdź, czy problem został rozwiązany.
