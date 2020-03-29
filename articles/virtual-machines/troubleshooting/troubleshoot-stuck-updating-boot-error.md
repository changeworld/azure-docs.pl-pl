---
title: Uruchamianie maszyny Wirtualnej platformy Azure utknęło w usłudze Windows Update| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać ten problem, gdy uruchomienie maszyny Wirtualnej platformy Azure utknęło w witrynie Windows Update.
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
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919434"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Uruchamianie maszyny Wirtualnej platformy Azure utknęło w usłudze Windows Update

Ten artykuł pomaga rozwiązać problem, gdy maszyna wirtualna (VM) utknęła na etapie usługi Windows Update podczas uruchamiania. 


## <a name="symptom"></a>Objaw

 Maszyna wirtualna systemu Windows nie uruchamia się. Po sprawdzeniu zrzutów ekranu w oknie [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) widać, że uruchamianie utknęło w procesie aktualizacji. Poniżej przedstawiono przykłady wiadomości, które mogą być odbierane:

- Instalowanie systemu Windows ##% Nie wyłączaj komputera. Zajmie to trochę czasu Komputer zostanie uruchomiony ponownie kilka razy
- Trzymaj komputer włączony, dopóki nie zostanie to zrobione. Instalowanie aktualizacji # #... 
- Nie można ukończyć aktualizacji Cofanie zmian Nie wyłączaj komputera
- Niepowodzenie konfigurowania aktualizacji systemu Windows Przywracanie zmian Nie wyłącza komputera
- Błąd < kod błędu > zastosowaniu operacji aktualizacji ##### z ##### (\Regist...)
- Błąd krytyczny < kod błędu > zastosowaniu operacji aktualizacji ##### z ##### ($$...)


## <a name="solution"></a>Rozwiązanie

W zależności od liczby aktualizacji, które są coraz instalowane lub walcowane kopii zapasowej, proces aktualizacji może zająć trochę czasu. Pozostaw maszynę wirtualną w tym stanie na 8 godzin. Jeśli maszyna wirtualna jest nadal w tym stanie po tym okresie, uruchom ponownie maszynę wirtualną z witryny Azure portal i zobacz, czy można uruchomić normalnie. Jeśli ten krok nie zadziała, wypróbuj następujące rozwiązanie.

### <a name="remove-the-update-that-causes-the-problem"></a>Usuń aktualizację powodującą problem

1. Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md). 
2. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).
3. Po podłączeniu dysku systemu operacyjnego na maszynie wirtualnej odzyskiwania uruchom **plik diskmgmt.msc,** aby otworzyć zarządzanie dyskami, i upewnij się, że podłączony dysk jest **online**. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego, na którym znajduje się folder \windows. Jeśli dysk jest zaszyfrowany, odszyfruj dysk przed przystąpieniem do kolejnych kroków w tym dokumencie.

4. Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenie, aby uzyskać listę pakietów aktualizacji, które znajdują się na podłączonym dysku systemu operacyjnego:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Jeśli na przykład dołączonym dyskiem systemu operacyjnego jest dysk F, uruchom następujące polecenie:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otwórz plik C:\temp\Patch_level.txt, a następnie odczytaj go od dołu do góry. Znajdź aktualizację, która jest w stanie **Oczekiwania na instalację** lub **Odinstaluj.**  Poniżej przedstawiono przykład stanu aktualizacji:

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
    > W zależności od rozmiaru pakietu narzędzie DISM zajmie trochę czasu, aby przetworzyć un-instalacji. Zwykle proces zostanie zakończony w ciągu 16 minut.

7. [Odłącz dysk systemu operacyjnego i ponownie stwórz maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Następnie sprawdź, czy problem został rozwiązany.
