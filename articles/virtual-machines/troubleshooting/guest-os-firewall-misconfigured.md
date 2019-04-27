---
title: Zaporę systemu operacyjnego gościa maszyny Wirtualnej platformy Azure jest nieprawidłowo skonfigurowana. | Dokumentacja firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711020"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Usługa Azure zapory systemu operacyjnego gościa maszyny Wirtualnej jest błędnie skonfigurowane

Ten artykuł wprowadza jak naprawić gościa nieprawidłowo działających Zapora systemu na maszynie Wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

1.  Ekran powitalny maszynę wirtualną (VM) pokazuje, że maszyna wirtualna jest w pełni załadowane.

2.  W zależności od sposobu skonfigurowania systemu operacyjnego gościa może być niektórych lub ruch sieciowy docieranie do maszyny Wirtualnej.

## <a name="cause"></a>Przyczyna

Błędnej konfiguracji zapory systemu gościa można zablokować niektóre lub wszystkie rodzaje ruchu sieciowego do maszyny Wirtualnej.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemowego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć konsoli szeregowej lub [napraw maszynę Wirtualną w tryb offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) przez dołączenie dysku maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.

## <a name="online-mitigations"></a>Środki zaradcze w trybie online

Połączyć się z [konsoli szeregowej, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, przejdź do sekcji "Napraw maszynę Wirtualną w tryb Offline" w artykule platformy Azure:

 [Występuje błąd wewnętrzny podczas próby połączenia z Maszyną wirtualną platformy Azure za pośrednictwem pulpitu zdalnego](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Następujące reguły można edytować, albo umożliwiające dostęp do maszyny Wirtualnej (za pośrednictwem protokołu RDP) lub w celu udostępnienia środowiska łatwiejsze do rozwiązywania problemów:

*   Pulpit zdalny (ruch przychodzący TCP): Jest to standardowy regułę, która zapewnia dostępu do maszyny Wirtualnej, umożliwiając protokołu RDP na platformie Azure.

*   Zdalnego zarządzania Windows (ruch przychodzący HTTP): Dzięki temu reguły można nawiązać połączenia z maszyną Wirtualną przy użyciu programu PowerShell. W przypadku platformy Azure, tego rodzaju dostępu pozwala za pomocą skryptów aspekt skryptów zdalnym i rozwiązywanie problemów.

*   Udostępnianie plików i drukarek (ruch przychodzący SMB): Ta reguła umożliwia dostęp do udziału sieciowego jako opcji rozwiązywania problemów.

*   Udostępnianie plików i drukarek (żądanie echa — ICMPv4-In): Ta reguła umożliwia wysłać polecenie ping maszyny Wirtualnej.

W przypadku dostępu do konsoli szeregowej można tworzyć zapytania bieżący stan reguły zapory.

*   Zapytania przy użyciu nazwy wyświetlanej jako parametr:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Zapytanie, za pomocą Port lokalny, który korzysta z aplikacji:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Zapytania za pomocą adresu lokalnego adresu IP, którego używa aplikacja:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Jeśli widzisz, że reguła jest wyłączona, możesz je włączyć, uruchamiając następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Na potrzeby rozwiązywania problemów można wyłączyć profile zapory:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Jeśli to zrobisz to poprawne ustawienie zapory ponownie włącz zaporę, po zakończeniu rozwiązywania problemów.

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę Wirtualną, aby zastosować tę zmianę.

*   Spróbuj ponownie nawiązać połączenie z maszyną Wirtualną za pośrednictwem protokołu RDP.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  Aby włączyć lub wyłączyć reguły zapory, zapoznaj się [włączyć lub wyłączyć regułę zapory w systemie operacyjnym gościa maszyny Wirtualnej platformy Azure](enable-disable-firewall-rule-guest-os.md).

2.  Sprawdź, czy znajdują się w [blokowania scenariusz ruchu przychodzącego zapory systemu operacyjnego gościa](guest-os-firewall-blocking-inbound-traffic.md).

3.  Jeśli jesteś nadal w stanie wątpliwości, czy Zapora blokuje dostęp do usługi, zapoznaj się [Wyłącz gościa Zapora systemu operacyjnego na maszynie Wirtualnej platformy Azure](disable-guest-os-firewall-windows.md), a następnie ponownie włącz Zaporę systemu gościa za pomocą reguł poprawne.

