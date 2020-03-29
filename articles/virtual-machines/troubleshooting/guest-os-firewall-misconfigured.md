---
title: Zapora systemu operacyjnego gościa platformy Azure jest nieprawidłowo skonfigurowana | Dokumenty firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153890"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Zapora systemu operacyjnego gościa platformy Azure vm jest nieprawidłowo skonfigurowana

W tym artykule przedstawiono sposób naprawienia nieprawidłowo skonfigurowanej zapory systemu operacyjnego gościa na maszynie Wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

1.  Na ekranie powitalnym maszyny wirtualnej (VM) jest wyświetlany, że maszyna wirtualna jest w pełni załadowana.

2.  W zależności od sposobu skonfigurowania systemu operacyjnego gościa może istnieć ruch sieciowy lub nie może być on żaden ruch sieciowy docierający do maszyny Wirtualnej.

## <a name="cause"></a>Przyczyna

Błędna konfiguracja zapory systemu gościa może blokować niektóre lub wszystkie rodzaje ruchu sieciowego do maszyny Wirtualnej.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemowego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline,](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) dołączając dysk systemowy maszyny Wirtualnej do maszyny Wirtualnej odzyskiwania.

## <a name="online-mitigations"></a>Środki zaradcze online

Połącz się z [konsolą szeregową, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do sekcji "Napraw maszynę wirtualną w trybie offline" w następującym artykule platformy Azure:

 [An internal error occurs when you try to connect to an Azure VM through Remote Desktop (Podczas próby połączenia z maszyną wirtualną platformy Azure za pośrednictwem pulpitu zdalnego występuje błąd wewnętrzny)](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Następujące reguły można edytować, aby umożliwić dostęp do maszyny wirtualnej (za pośrednictwem protokołu RDP) lub zapewnić łatwiejsze rozwiązywanie problemów:

*   Pulpit zdalny (TCP-In): Jest to standardowa reguła, która zapewnia podstawowy dostęp do maszyny Wirtualnej, zezwalając na protokół RDP na platformie Azure.

*   Zdalne zarządzanie systemem Windows (HTTP-In): Ta reguła umożliwia łączenie się z maszyną wirtualną przy użyciu programu PowerShell., Na platformie Azure ten rodzaj dostępu umożliwia korzystanie z aspektu skryptów zdalnego skryptów i rozwiązywania problemów.

*   Udostępnianie plików i drukarek (SMB-In): Ta reguła umożliwia dostęp do udziału w sieci jako opcję rozwiązywania problemów.

*   Udostępnianie plików i drukarek (echo request - ICMPv4-In): Ta reguła umożliwia ping maszyny Wirtualnej.

W wystąpieniu dostęp do konsoli szeregowej można zbadać bieżący stan reguły zapory.

*   Kwerenda przy użyciu nazwy wyświetlanej jako parametru:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Zapytanie przy użyciu portu lokalnego, który używa aplikacji:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Zapytanie przy użyciu lokalnego adresu IP używanego przez aplikację:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Jeśli widzisz, że reguła jest wyłączona, możesz ją włączyć, uruchamiając następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Aby rozwiązać problemy, można wyłączyć profile zapory:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Jeśli to zrobisz, aby poprawnie ustawić zaporę, włącz ponownie zaporę po zakończeniu rozwiązywania problemów.

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę wirtualną, aby zastosować tę zmianę.

*   Spróbuj ponownie połączyć się z maszyną wirtualną za pośrednictwem protokołu RDP.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  Aby włączyć lub wyłączyć reguły zapory, zapoznaj się [z artykułem Włącz lub wyłącz regułę zapory w systemu operacyjnym gościa maszyny Wirtualnej platformy Azure](enable-disable-firewall-rule-guest-os.md).

2.  Sprawdź, czy znajdujesz się w [zaporze systemu operacyjnego gościa blokującej scenariusz ruchu przychodzącego](guest-os-firewall-blocking-inbound-traffic.md).

3.  Jeśli nadal masz wątpliwości, czy zapora blokuje dostęp, zapoznaj się [z opcję Wyłącz zaporę systemu operacyjnego gościa w usłudze Azure VM,](disable-guest-os-firewall-windows.md)a następnie ponownie włącz zaporę systemu gościa przy użyciu poprawnych reguł.

