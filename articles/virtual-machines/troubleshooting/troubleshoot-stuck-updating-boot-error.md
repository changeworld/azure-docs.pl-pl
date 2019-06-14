---
title: Azure uruchamiania maszyn wirtualnych jest zablokowana w Windows Update | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać ten problem, podczas uruchamiania maszyny Wirtualnej platformy Azure jest zablokowana w Windows aktualizacji.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: cff1577eacd0af86d3ad1c99e1eb2164b64318c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443776"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Usługa Azure uruchamiania maszyn wirtualnych jest zablokowana w Windows aktualizacji

Ten artykuł pomoże rozwiązać ten problem, gdy Twoja maszyna wirtualna (VM) jest zablokowany na etapie Windows Update podczas uruchamiania. 

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager. Zalecamy użycie tego modelu w przypadku nowych wdrożeń zamiast przy użyciu klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw

 Nie zaczyna się maszyny Wirtualnej z systemem Windows. Podczas ewidencjonowania zrzuty ekranu [diagnostykę rozruchu](../troubleshooting/boot-diagnostics.md) okna, zobaczysz, że początkową utkwiła w automatycznej procesu aktualizacji. Poniżej przedstawiono przykłady wiadomości, które mogą wystąpić:

- Instalowanie Windows ##% nie wyłączaj komputera PC. To zajmie trochę czasu swój komputer zostanie uruchomiony ponownie kilka razy
- Zabezpieczenia komputera, dopóki nie odbywa się. Instalowanie aktualizacji # #... 
- Firma Microsoft nie można ukończyć aktualizacji, które cofanie zmian nie wyłączaj komputera
- Błąd konfigurowania aktualizacji Windows Reverting zmiany nie wyłączaj komputera
- Błąd < kod błędu > Stosowanie operacje aktualizacji ### z ### (\Regist...)
- Błąd krytyczny < kod błędu > Stosowanie operacje aktualizacji ### z ### ($USD...)


## <a name="solution"></a>Rozwiązanie

W zależności od liczby aktualizacji, które są objęte zainstalowane lub wycofana zapasowy, proces aktualizacji może wymagać trochę czasu. Pozostaw maszynę Wirtualną w tym stanie przez 8 godzin. Jeśli maszyna wirtualna jest nadal w tym stanie, po upływie tego czasu, uruchom ponownie maszynę Wirtualną w witrynie Azure portal i zobacz, jeśli można ją uruchamiać normalnie. Jeśli ten krok nie zadziała, spróbuj następujących rozwiązań.

### <a name="remove-the-update-that-causes-the-problem"></a>Usuń aktualizację, która powoduje, że problem

1. Utworzenie migawki dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej służy do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md). 
2. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).
3. Dołączony dysk systemu operacyjnego na maszynie Wirtualnej odzyskiwania Uruchom **diskmgmt.msc** Otwórz przystawkę Zarządzanie dyskami i upewnij się, dysk dołączony **ONLINE**. Zanotuj literę dysku, który jest przypisany do dołączonym dysku systemu operacyjnego, zawierający folderu \windows. Jeśli dysk jest zaszyfrowany, odszyfrowania dysku przed przejściem do następnej procedury w tym dokumencie.

4. Otwórz wystąpienie wiersz polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenie, aby uzyskać listę pakietów aktualizacji, które znajdują się na dołączonym dysku systemu operacyjnego:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Na przykład jeśli dołączonym dysku systemu operacyjnego jest dysku F, uruchom następujące polecenie:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otwórz plik C:\temp\Patch_level.txt, a następnie przeczytaj go od dołu. Znajdź aktualizację, która znajduje się w **oczekująca instalacja** lub **odinstalować oczekujące** stanu.  Poniżej przedstawiono przykładowe stan aktualizacji:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Usunąć aktualizację, która spowodowała problem:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Przykład: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > W zależności od rozmiaru pakietu narzędzia DISM zajmie trochę czasu przetwarzania dezinstalacji. Zwykle proces zostanie zakończony w ciągu minut 16.

7. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Sprawdź, czy problem został rozwiązany.
