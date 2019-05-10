---
title: Windows ponowny rozruch pętli na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z pętli ponownego uruchamiania Windows | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 1c97b1da094b759ccf85f310ceec4c7abfd91b9b
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472292"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Pętli ponownego uruchamiania Windows na Maszynie wirtualnej platformy Azure
W tym artykule opisano pętli ponownego uruchamiania, które mogą występować na Windows maszyn wirtualnych (VM) w systemie Microsoft Azure.

## <a name="symptom"></a>Objaw

Kiedy używasz [diagnostykę rozruchu](./boot-diagnostics.md) można pobrać zrzuty ekranu maszyny wirtualnej, możesz znaleźć maszyny wirtualnej jest uruchamiany, ale wprowadzenie przerwania procesu rozruchu i uruchamia proces za pośrednictwem.

![Ekran startowy 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Przyczyna

W pętli ponownego uruchamiania występuje z powodu następujących przyczyn:

### <a name="cause-1"></a>Przyczyny 1

Jest to usługa innej firmy, która jest oznaczone jako krytyczne i nie może zostać uruchomiony. Spowoduje to ponowne uruchomienie systemu operacyjnego.

### <a name="cause-2"></a>Przyczyny 2

Niektóre zmiany zostały wprowadzone w systemie operacyjnym. Zazwyczaj odnoszą się one do instalacji aktualizacji, instalacja aplikacji lub nowych zasad. Może być konieczne sprawdź następujące dzienniki, aby uzyskać więcej informacji:

- Dzienniki zdarzeń
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Przyczyny 3

Może to spowodować uszkodzenia systemu plików. Jednak jest trudne do zdiagnozowania i zidentyfikować zmiany, która powoduje uszkodzenie systemu operacyjnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md), i [Dołącz dysk systemu operacyjnego do ratownictwa maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md), a następnie postępuj zgodnie z opcjami rozwiązania lub wypróbuj rozwiązania jedno po drugim.

### <a name="solution-for-cause-1"></a>Rozwiązanie przyczyny 1

1. Gdy dysk systemu operacyjnego jest dołączony do działającej maszyny Wirtualnej, upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami i zanotuj literę dysku partycji, który przechowuje **\Windows** folderu.

2. Jeśli dysk jest równa **Offline**, wówczas ustaw ją na **Online**.

3. Utwórz kopię **\Windows\System32\config** folderu w przypadku wycofywania zmian jest wymagana.

4. Na ratownictwa maszynę Wirtualną Otwórz Edytor rejestru Windows (regedit).

5. Wybierz **HKEY_LOCAL_MACHINE** klucza, a następnie wybierz pozycję **pliku** > **Załaduj gałąź rejestru** z menu.

6. Przejdź do pliku systemu w **\Windows\System32\config** folderu.

7. Wybierz **Otwórz**, typ **BROKENSYSTEM** dla nazwy, rozwiń węzeł **HKEY_LOCAL_MACHINE** klucz, a następnie zostanie wyświetlony dodatkowy klucz o nazwie **BROKENSYSTEM** .

8. Sprawdź, które ControlSet komputer jest uruchamiany z. Zostaną wyświetlone jego numer klucza w następującym kluczu rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Sprawdzanie, czyli krytyczności usługę agenta maszyny Wirtualnej za pomocą następującego klucza rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Jeśli nie jest równa wartości klucza rejestru **2**, a następnie przejdź do następnego środki zaradcze.

11. Jeśli wartość klucza rejestru jest równa **2**, następnie zmień wartość z **2** do **1**.

12. Jeśli istnieją następujące klucze mają wartości **2** lub **3**, a następnie zmień te wartości do **1** odpowiednio:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Wybierz **BROKENSYSTEM** klucza, a następnie wybierz pozycję **pliku** > **Załaduj gałąź rejestru** z menu.

14. Odłącz dysk systemu operacyjnego od maszyny Wirtualnej rozwiązywania problemów.

15. Usuń dysk z maszyny Wirtualnej rozwiązywania problemów, a następnie poczekaj około 2 minuty na platformie Azure zwolnić tego dysku.

16. [Utwórz nową maszynę Wirtualną z dyskiem systemu operacyjnego](../windows/create-vm-specialized.md).

17. Jeśli problem zostanie rozwiązany, a następnie konieczne będzie ponowne zainstalowanie [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Rozwiązanie przyczyny 2

Przywróć maszynę Wirtualną do ostatniej znanej dobrej konfiguracji, wykonaj kroki opisane w [sposobu uruchamiania maszyn wirtualnych Windows Azure przy użyciu Ostatnia znana dobra konfiguracja](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Rozwiązanie, aby ustalić przyczynę 3
>[!NOTE]
>Poniższa procedura stosuje się tylko jako ostatni zasób. Podczas przywracania z regback przywrócić dostęp do maszyny, systemu operacyjnego nie jest uważany za stabilny, ponieważ ma danych utraconych w rejestrze między sygnatury czasowej gałęzi i bieżący dzień. Potrzebujesz do tworzenia nowej maszyny Wirtualnej i opracowuje plany w celu migracji danych.

1. Gdy dysk jest dołączony do maszyny wirtualnej rozwiązywania problemów, upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami.

2. Utwórz kopię **\Windows\System32\config** folderu w przypadku wycofywania zmian jest wymagana.

3. Skopiuj pliki z **\Windows\System32\config\regback** folder i zastąpić pliki w **\Windows\System32\config** folderu.

4. Usuń dysk z maszyny Wirtualnej rozwiązywania problemów, a następnie poczekaj około 2 minuty na platformie Azure zwolnić tego dysku.

5. [Utwórz nową maszynę Wirtualną z dyskiem systemu operacyjnego](../windows/create-vm-specialized.md).


