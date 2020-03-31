---
title: Pętla ponownego uruchomienia systemu Windows na maszynie Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z pętlą ponownego uruchamiania systemu Windows | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443584"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Pętla ponownego uruchamiania systemu Windows na maszynie Wirtualnej platformy Azure
W tym artykule opisano pętlę ponownego uruchamiania, która może wystąpić na maszynie wirtualnej systemu Windows (VM) na platformie Microsoft Azure.

## <a name="symptom"></a>Objaw

Korzystając z [diagnostyki rozruchu,](./boot-diagnostics.md) aby uzyskać zrzuty ekranu maszyny wirtualnej, można znaleźć maszyny wirtualnej jest rozruchu, ale proces rozruchu jest coraz przerwane i proces zaczyna się od początku.

![Ekran startowy 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Przyczyna

Pętla ponownego uruchomienia występuje z następujących przyczyn:

### <a name="cause-1"></a>Przyczyna 1

Istnieje usługa innej firmy, która jest oflagowana jako krytyczna i nie można jej uruchomić. Powoduje to ponowne uruchomienie systemu operacyjnego.

### <a name="cause-2"></a>Przyczyna 2

Wprowadzono pewne zmiany w systemie operacyjnym. Zazwyczaj są one związane z instalacją aktualizacji, instalacją aplikacji lub nową zasadą. Może być konieczne sprawdzenie następujących dzienników w celu uzyskania dodatkowych informacji:

- Dzienniki zdarzeń
- Okna CBS.log
- Update.log

### <a name="cause-3"></a>Przyczyna 3

Przyczyną tego może być uszkodzenie systemu plików. Jednak trudno jest zdiagnozować i zidentyfikować zmiany, które powoduje uszkodzenie systemu operacyjnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [wykonaj zapasową kopii zapasowej dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md)i [dołącz dysk systemu operacyjnego do maszyny wirtualnej ratunkowej,](../windows/troubleshoot-recovery-disks-portal.md)a następnie postępuj zgodnie z opcjami rozwiązania lub wypróbuj rozwiązania jeden po drugim.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Po dołączeniu dysku systemu operacyjnego do działającej maszyny Wirtualnej upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami i zanotuj literę dysku partycji zawierającej folder **\Windows.**

2. Jeśli dysk jest ustawiony na **Tryb offline,** należy ustawić go na **Online**.

3. Utwórz kopię folderu **\Windows\System32\config** na wypadek, gdyby konieczne było wycofanie zmian.

4. Na maszynie wirtualnej ratownictwa otwórz Edytor rejestru systemu Windows (regedit).

5. Wybierz klawisz **HKEY_LOCAL_MACHINE,** a następnie z menu wybierz polecenie**Gałąź obciążenia** **pliku.** > 

6. Przejdź do pliku SYSTEM w folderze **\Windows\System32\config.**

7. Wybierz **otwórz**, wpisz **BROKENSYSTEM** dla nazwy, rozwiń **HKEY_LOCAL_MACHINE** klawisz, a następnie pojawi się dodatkowy klucz o nazwie **BROKENSYSTEM**.

8. Sprawdź, z którego zestawu ControlSet uruchamia się komputer. Jego numer klucza zostanie wyświetlony w następującym kluczu rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Sprawdź, która jest krytyczność usługi agenta maszyny Wirtualnej za pośrednictwem następującego klucza rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Jeśli wartość klucza rejestru nie jest ustawiona na **2**, przejdź do następnego ograniczenia.

11. Jeśli wartość klucza rejestru jest ustawiona na **2**, zmień wartość z **2** na **1**.

12. Jeśli istnieje którykolwiek z następujących kluczy i mają one wartość **2** lub **3**, a następnie odpowiednio zmień te wartości na **1:**

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Wybierz klucz **BROKENSYSTEM,** a następnie z menu wybierz polecenie**Rozładuj** **plik.** > 

14. Odłącz dysk systemu operacyjnego od maszyny wirtualnej do rozwiązywania problemów.

15. Usuń dysk z maszyny Wirtualnej rozwiązywania problemów i poczekaj około 2 minut, aż platforma Azure wyda ten dysk.

16. [Utwórz nową maszynę wirtualną z dysku systemu operacyjnego](../windows/create-vm-specialized.md).

17. Jeśli problem został rozwiązany, może być trzeba ponownie zainstalować [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć maszynę wirtualną do ostatniej znanej dobrej konfiguracji, wykonaj kroki opisane w [jak uruchomić maszynę wirtualną systemu Azure systemu Windows z ostatnią znaną dobrą konfiguracją](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Roztwór dla przyczyny 3
>[!NOTE]
>Poniższa procedura powinna być używana tylko jako ostatni zasób. Podczas przywracania z regback może przywrócić dostęp do komputera, system operacyjny nie jest uważany za stabilny, ponieważ istnieją dane utracone w rejestrze między sygnaturą czasową gałęzi i bieżącego dnia. Należy utworzyć nową maszynę wirtualną i zrobić plany migracji danych.

1. Po podłączeniu dysku do maszyny wirtualnej do rozwiązywania problemów upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami.

2. Utwórz kopię folderu **\Windows\System32\config** na wypadek, gdyby konieczne było wycofanie zmian.

3. Skopiuj pliki w folderze **\Windows\System32\config\regback** i zastąp pliki w folderze **\Windows\System32\config.**

4. Usuń dysk z maszyny Wirtualnej rozwiązywania problemów i poczekaj około 2 minut, aż platforma Azure wyda ten dysk.

5. [Utwórz nową maszynę wirtualną z dysku systemu operacyjnego](../windows/create-vm-specialized.md).


